# 30 · AI Systems 性能模型、SLO 与容量分析

[知识地图](00-learning-roadmap.md) · [Serving](10-serving-and-distributed.md) · [生产评估](22-evaluation-and-production.md)

## 为什么慢：先定义完成的工作，再定位等待

AI 系统的结果是在正确性、质量、时限和成本约束下完成训练更新或用户请求。GPU utilization 不能替代有效工作量；怎样辨认忙碌与等待的真实原因由[第 31 章](31-ai-systems-observability-and-debugging.md)主责。

本章统一从请求延迟、系统吞吐、资源容量到成本的关系。执行细节仍由[Compiler / Runtime](28-ai-compiler-and-runtime.md)、[训练](17-training-engineering.md)、[引擎](18-inference-engineering.md)和[平台](29-ai-platform-and-cluster-scheduling.md)展开。

## Request latency 是一条有边界的关键路径

对一次生成请求，可以用下面的分解建立观测口径：

```math
T_{\mathrm{request}} = T_{\mathrm{queueing}} + T_{\mathrm{preprocessing}} + T_{\mathrm{prefill}} + T_{\mathrm{decode}} + T_{\mathrm{communication}} + T_{\mathrm{output}}
```

Queueing 包含入口/阶段等待，preprocessing 包含 Tokenize 与输入准备，prefill 处理尚未计算的提示词位置，decode 是后续生成推进，communication 包含路由传输、KV transfer 与关键路径通信，output 包含解码文本、缓冲和交付。

**加法成立的前提是各项在同一计时边界上互不重计。**Decode 内核时间如果已包含 TP 等待，就不能再完整加一次该通信；输出与下一轮 Decode 重叠时，只计暴露在关键路径上的部分。真实 trace 是有依赖的时间线，不能把不同组件的总 busy time 直接相加。多次阶段排队也不能只算入口等待。

客户端发送到最后一个可见结果、Gateway 接收到完成、引擎入队到末 Token，是不同边界。RAG/Agent 的任务还可能包括检索、工具与多次模型调用；模型请求延迟不是整个业务任务延迟。

## TTFT、TPOT / ITL 与端到端延迟

| 指标 | 定义与对象 | 使用边界 |
| --- | --- | --- |
| TTFT | 起始时刻到首个输出 Token/可见事件 | 说明是否含网络、预处理、排队、KV 取回；首事件可能包含多个 Token |
| ITL | 相邻输出 Token 的间隔 | 逐间隔分布可暴露流中卡顿；客户端事件间隔不必等于引擎 Token 间隔 |
| TPOT | 请求首 Token 后，每个后续输出 Token 的平均耗时 | 平均值会隐藏单次很长的 ITL；说明分母与不足两 Token 的处理 |
| End-to-end latency | 同一起点到最终完成 | 包含长输出的累计 Decode 和最终输出；与 TTFT 分开报告 |

当起点一致、末 Token 与完成边界一致，输出 Token 数 `N_out` 大于 1 时，常见请求级定义是：

```math
\mathrm{TPOT} = \frac{T_{\mathrm{end}}-T_{\mathrm{first}}}{N_{\mathrm{out}}-1}
```

零或一个输出 Token 没有这种平均后续间隔；实现可能记录零、空值或排除，应说明，不能让大量单 Token 请求人为降低总体均值。跨请求平均 TPOT 与把所有间隔汇总再平均，也有不同权重。vLLM 的具体计时和不足两 Token 处理见[Metrics](https://docs.vllm.ai/en/stable/design/metrics/)，以实际版本为准。

Prefill 通常产生首输出 logits，后续 Token 才逐轮进入 Decode；TTFT 不等于整个 Prefill Kernel 耗时。P/D 分离的传输与目标等待可能抬高 TTFT，阶段隔离又可能改善 ITL，必须同时比较。

## P50 / P95 / P99 与 tail latency

P50 是一半观测值不超过的延迟，P95/P99 反映更靠后的分位，tail latency（尾延迟）关注这些慢样本。它不是“最慢请求的固定比例成本”，也不说明慢样本原因。

平均值不能覆盖突发队列、长上下文、冷启动或远端缓存失败。应按模型版本、长度、租户/服务等级、冷热状态和停止原因分组，并保证足够样本。不同阶段的 P99 不能相加得到请求 P99；每个阶段的慢样本可能不是同一批请求。

TPOT P99 与 ITL P99 也不同：前者是请求均值的尾部，后者是单次间隔的尾部。一个请求平均间隔合格，仍可能中途停顿很久。超时、取消和拒绝应单独计数，不能剔除后只报告“成功请求 P99”而宣称全服务合格。

## Throughput 的分子决定解释

| 口径 | 表达的工作 | 需要同时记录 |
| --- | --- | --- |
| requests/s | 单位时间完成的请求数 | 成功/失败、长度与任务难度分布 |
| input / output tokens/s | 输入处理量或输出生成量 | 输入与输出分开，避免不同成本合并 |
| useful tokens/s | 满足定义的质量/SLO、真正交付的有效 Token | 明确合格条件，另报失败、拒绝和浪费工作 |
| batch throughput | 一批工作完成量除以其时间 | 批次形成等待、实际有效 Token、padding 与阶段形态 |

推测解码中被拒绝的草稿、取消后继续计算的 Token、padding 和失败重试可能消耗 GPU，却不是全部可交付吞吐。Useful tokens/s 是按明确口径定义的指标，不是所有框架统一的内置名字。若用合格请求的交付量衡量 goodput，还要报告总到达量和拒绝率，防止只接容易请求提高分数。

更大 batch 可提高权重/算子复用，但等待成批、单轮时间、活跃 KV 与公平性代价上升。吞吐最高的工作点不一定满足 TTFT/ITL。连续批处理改变迭代集合，分块 Prefill 减少对 Decode 的单轮干扰，又引入额外调度；应比较满足 SLO 的吞吐，而非无约束峰值。

## Memory capacity 与 compute capacity 不同

权重、KV、激活、workspace、通信缓冲、图缓冲与 allocator 保留共同占设备内存。以下是组织预算的约束，不表示所有项都在每个时刻取各自最大值：

```math
M_{\mathrm{weight}} + M_{\mathrm{KV}} + M_{\mathrm{activation/workspace}} + M_{\mathrm{communication/graph}} + M_{\mathrm{reserve}} \leq M_{\mathrm{usable}}
```

实际峰值由**同时存活**对象决定。权重加载转换或 KV transfer 时可能双份驻留；Compiler memory planning 和训练重计算会改时间线。Allocator 保留、缓存可回收容量与正在使用的字节需分别看，不能把预留全部加成新的真实张量后重复计算。

对普通 Transformer KV，按单请求、未量化且不含元数据的逻辑存储估计：

```math
M_{\mathrm{KV,request}} \approx 2 L N_{\mathrm{KVhead}} d_{\mathrm{head}} S b
```

L 是层数，`N_KVhead` 是 KV 头数，`d_head` 是头维度，S 是已保存位置数，b 是每元素字节；2 代表 K 和 V。GQA/MQA、量化及特殊状态模型会改变对应参数或公式。分页尾块、尺度元数据和物理分片/复制会改变实际占用；TP 下不能保证每卡简单除以 rank 数。

Concurrency 是同时活跃请求数量，context length 与生成增长决定每请求状态，二者共同约束 KV。共享前缀减少物理重复，但不能把有活跃引用的块当作随时可回收。长度分布比单一平均更能说明长请求风险，输出上限与准入策略需一致。

Memory capacity 回答“状态能否同时驻留”；compute capacity 回答“处理能否及时推进”。放得下的大批长历史请求可能每轮读 KV 太多、无法满足 ITL；算力空闲的设备也可能因权重/KV 容量无法接新请求。扩 HBM 与扩 FLOP/s 解决的问题不同。

## Compute-bound、memory-bound 与 communication-bound

Compute-bound 指运算吞吐限制进度；memory-bound 指所考察内存层级的访问限制；communication-bound 指设备/节点之间交换或同步限制。主机提交、CPU 预处理和排队还可能在这些分类之外成为瓶颈。不同阶段甚至不同 Kernel 的瓶颈可以不同。

算术强度 arithmetic intensity 是所选边界的 FLOPs 除以搬运字节；必须说明字节来自 HBM、片上资源还是另一层级。Roofline 将运算上限与带宽上限连接：

```math
I = \frac{F}{B}, \qquad P_{\mathrm{attainable}} \leq \min(P_{\mathrm{compute}},\ I\beta_{\mathrm{memory}})
```

F 为运算量，B 为对应层级字节，beta 为该层带宽，P 为运算速率。这是简化上限模型，不承诺达到；实测还受布局、依赖、占用率、精度、launch 与算法限制。标称低精度 Tensor Core 峰值不能直接用于所有算子。依据：[Roofline 原始论文](https://escholarship.org/uc/item/78h8v7mr)。

Prefill 大矩阵通常有更多复用，小 batch Decode 常重复读取权重并读历史 KV；这是趋势而非固定定律。Fusion/FlashAttention 减少某些中间字节，batching 提高复用，量化改变字节与计算路径；都需要重新测量实际瓶颈。通信可近似分成启动/同步与数据搬运，短频繁 TP 消息可能对时延敏感，CP/EP 大交换可能受带宽和不均衡限制。

## Amdahl’s Law：局部加速为何不等于整请求加速

在固定工作量、被优化部分占原时间比例 f、该部分加速 s，其他部分保持不变的简化模型中：

```math
S_{\mathrm{overall}} = \frac{1}{(1-f)+f/s}
```

即使某 Kernel 很快，排队、检索、其他 Kernel、通信和输出仍限制整请求收益。该公式假设时间比例和依赖不随优化改变；实际加速可能改变 batch、队列与争用，因此不是生产收益的自动预测器。依据：[Amdahl 原始论文](https://doi.org/10.1145/1465482.1465560)。

优化后重新定位关键路径：降低计算可能暴露通信，增加缓存可能压缩活跃容量，提升吞吐可能减少排队。这些连锁变化不能只看一个 Kernel 的 speedup。

## Little’s Law 与 queueing：并发不是输入速率

在稳定、流量统计与边界一致且平均量存在的系统中，Little’s Law 将平均在系统内的工作数、有效到达率与平均停留时间连接：

```math
\overline{N} = \lambda\overline{W}
```

若边界包含等待和执行，N 包括两者，W 是完整停留；若只看队列，则用队列中的数量与等待时间。不能用 P99 代替平均 W，也不能混用请求/s 与 Token 数。拒绝、取消和重试需按所选边界计流量，不把进不了系统的请求与已准入流量随意混合。依据：[Little 原始论文](https://doi.org/10.1287/opre.9.3.383)。

持续过载、队列无限增长时，稳定平均关系不能当作目标容量证明。Little’s Law 是一致性关系，不单独提供服务时间分布或 P99 预测。服务时间变异、突发和调度策略会影响尾延迟；接近资源饱和时，少量扰动也可能堆积成长等待。

闭环压测让每个客户端等请求结束再发，服务慢时到达率随之下降；它适合固定并发观察，却可能隐藏外部持续到达的过载。开放负载按独立到达过程发请求，需同时测队列、拒绝、超时和恢复。不能以有限客户端“没崩”证明峰值生产到达可承受。

## Overlap、cache 与 locality 改变关键路径

Communication / computation overlap 在依赖允许时同时推进工作。通信总耗时不等于暴露通信：参数预取可被当前计算覆盖，反向 bucket 归约可与后续反向重叠；需要等待最终通信的部分仍阻塞。多个 stream 或异步调用不是已发生重叠的证据，要看设备时间线。

重叠争用 HBM、SM、NIC，并延长缓冲寿命；为了重叠多留几份权重/通信缓冲，可能让峰值 OOM。优化按依赖、实际重叠与容量共同评估，训练见[第 17 章](17-training-engineering.md)，硬件见[第 27 章](27-compute-infrastructure.md)。

Cache 性能比较应记录命中长度/字节、真正省去的工作、取回耗时和驻留代价，而不只计有任意命中的请求比例。Prefix Cache 机制由[第 18 章](18-inference-engineering.md)主责，KV/load/locality 路由冲突由[第 10 章](10-serving-and-distributed.md)主责，权重缓存与冷启动由[第 29 章](29-ai-platform-and-cluster-scheduling.md)主责；本章用剩余关键路径和 SLO 比较其收益。

## Cost per request / token 的分母也有约束

在同一计费窗口，可定义总服务成本除以合格完成请求数，或除以合格交付的输出 Token 数。总成本包含闲置/预留 GPU、CPU、主机内存、网络、存储、启动与浪费工作；只计 Kernel 忙碌秒数会遗漏平台成本。

Token 成本要说明输入/输出与质量边界；单请求成本要保持任务和长度分布可比。更短的错误输出、拒绝难请求、额外重试或更多人工返工不能被当成成本改善。RAG/Agent 应再看每个合格任务成本，因为一个任务可能调用多个模型和工具。

更高利用率可能摊薄成本，也可能以违反尾延迟为代价。优质服务容量是在 SLO、错误率和质量限制下的可交付量，闲置余量可能是突发与故障预算的一部分。

## 从 Token 单价到每个合格任务的总成本

API 输入、缓存输入与输出可能采用不同价格；thinking、工具循环与失败重试又改变实际用量。自建系统还要计入资源驻留、草稿训练和环境启动。模型输出速度更高，不意味着串行工具或验证主导的任务同样加速；低单价也可能被额外生成与重试抵消。

评估模型选择时，在同一任务集与完成标准下记录全部调用、缓存冷热、实际 Token、工具/环境用量、失败和人工返工，并同时报告成功率、合格任务成本及端到端尾延迟。路由到更小模型或按难度增加 reasoning effort，是需要验证的策略；分类、升级重试和多候选本身也有成本，不能默认都能节省。

近期发布可作为这种评估的对象：[MiniMax M3.1 Flash（2026-10-19）](https://www.minimax.io/blog/minimax-m31f)强调 Agent 效率与成本，但其发布说明中的速度、降本和分数属于厂商口径，并说明权重将后续发布；[Gemini 4 Argon 开发者发布（2026-10-13）](https://blog.google/innovation-and-ai/technology/developers-tools/build-with-gemini-4-argon/)强调长流程工作。这些资料不公开完整内部机制，不能据名称推断架构或沿用宣传倍率做容量规划。发布与开放范围核实于 2026-10-21。

引擎离线吞吐、独立 Prefill/Decode 测量与在线服务 goodput 也要分别报告。阶段测试若用了不同硬件规模和负载，不能直接拼成一个端到端服务结果；还需加入排队、传输和输出边界。流式语音另有首音频、滞后和打断指标，定义见[第 13 章](13-multimodal-and-reasoning.md)。

## SLO-driven capacity planning 怎样形成闭环

SLO 是明确时间窗口内服务应达到的目标。容量规划应先固定模型/精度/生成策略、任务质量、输入输出长度和租户混合，再定义 TTFT、ITL/TPOT、端到端、错误和准入目标。不同服务等级可以有不同目标，不能用一个短输入平均值代表全部。

1. 建立稳态与冷启动负载边界，记录缓存冷热、突发与开放到达过程。
2. 测出随到达率/并发变化的吞吐、分位延迟、队列和内存，而非只找 OOM 前最大 batch。
3. 关联 Kernel、主机、通信、KV transfer、加载与输出时间线，定位最先失守的资源/阶段。
4. 在满足目标的工作点配置副本、P/D 池和 placement，保留启动时间、故障失去副本与突发余量。
5. 验证扩缩容、缓存失效、长请求、故障与恢复期间的指标；检查重试是否放大流量。
6. 上线后将实际混合负载与容量假设对照，模型、引擎、Compiler 或路由升级后重新确认受影响边界。

训练没有相同的流式 TTFT，却同样需要有效 tokens/s、time-to-train、step time、扩展效率与恢复停顿。保存 checkpoint 期间、最慢 rank 和输入等待都应进入时间预算；降低 step 的 Kernel 时间不必然缩短全训练完成时间。

## 将症状连接到可验证的原因

本章定义指标、资源模型与 SLO 工作点；请求 trace、CPU/GPU timeline、工具选择以及 TTFT、utilization、hang/OOM 的定位路径由[第 31 章](31-ai-systems-observability-and-debugging.md)主责。比较优化仍须保持语义、质量和负载一致，记录优化后暴露的新限制，而非只报告一个局部 speedup。
