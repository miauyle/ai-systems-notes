# 术语表

[返回目录](../README.md)

| 术语 | 中文 / 含义 | 容易混淆的边界 | 章节 |
| --- | --- | --- | --- |
| Token | Tokenizer 使用的离散单位 | 不等于字符或单词 | [03](03-tokenization-and-embeddings.md) |
| Tokenizer | 文本与 Token ID 之间的映射组件 | 必须与模型匹配 | [03](03-tokenization-and-embeddings.md) |
| Vocabulary | 词表，可用 Token 集合 | 大小不等于上下文长度 | [03](03-tokenization-and-embeddings.md) |
| Embedding | 可学习的向量表示 | 内部 Token 表与检索模型用途不同 | [03](03-tokenization-and-embeddings.md) |
| Hidden state | 隐藏状态，网络中间表示 | 不是人类可直接读出的思想 | [04](04-transformer.md) |
| Parameter / Weight | 参数 / 权重，训练学习的数值 | 与请求中的激活不同 | [02](02-math-and-tensors.md) |
| Activation | 激活，某次输入产生的中间数值 | 不属于永久参数 | [02](02-math-and-tensors.md) |
| Logits | 未归一化输出分数 | 还不是概率 | [02](02-math-and-tensors.md) |
| Softmax | 将分数归一化为分布 | 不等于事实置信度校准 | [02](02-math-and-tensors.md) |
| Attention | 按关联权重汇总表示 | 不等于完整解释机制 | [04](04-transformer.md) |
| Q / K / V | 查询 / 键 / 值投影 | 不是数据库字段 | [05](05-attention-walkthrough.md) |
| Causal mask | 因果掩码，阻止读取未来 | 与损失掩码不同 | [04](04-transformer.md) |
| FFN / MLP | 前馈网络 / 多层感知器 | 典型实现逐位置计算 | [04](04-transformer.md) |
| Residual | 残差连接 | 不是模型剩余错误的专用模块 | [02](02-math-and-tensors.md) |
| Norm | 归一化 | 与输出概率归一化用途不同 | [02](02-math-and-tensors.md) |
| RoPE | 旋转位置编码 | 不保证任意长度外推 | [06](06-modern-architectures.md) |
| GQA / MQA | 分组查询 / 多查询注意力 | 查询头数不等于 K/V 头数 | [06](06-modern-architectures.md) |
| MoE | 混合专家，常见为稀疏路由 FFN | 总参数不等于激活参数 | [06](06-modern-architectures.md) |
| SSM | 状态空间模型 | 不代表所有任务都优于 Attention | [06](06-modern-architectures.md) |
| Pretraining | 预训练 | 基础目标不等于直接业务验收 | [07](07-pretraining.md) |
| SFT | 监督微调 | 是训练目标与数据形式，不是参数效率方法 | [08](08-post-training.md) |
| RLHF | 基于人类反馈的强化学习流程 | 不专指一个算法 | [08](08-post-training.md) |
| DPO | 直接偏好优化 | 通常无需独立奖励模型 | [08](08-post-training.md) |
| LoRA / QLoRA | 低秩适配 / 量化基础权重上的低秩适配 | 不意味着全部张量低比特 | [08](08-post-training.md) |
| Context window | 上下文窗口 | 可接收长度不等于有效利用能力 | [03](03-tokenization-and-embeddings.md) |
| KV Cache | 每层历史 Key/Value 缓存 | 不存储答案，不更新参数 | [09](09-inference-and-memory.md) |
| Prefill / Decode | 输入处理 / 增量生成阶段 | 与完整首字延迟、网络延迟不同 | [09](09-inference-and-memory.md) |
| Quantization | 量化，低比特表示与相关计算技术 | 权重与 KV 分别判断；降存储不必然降延迟 | [18](18-inference-engineering.md) |
| TTFT | 首 Token 延迟 | 先说明计时边界，再判断包含哪些链路开销 | [30](30-ai-systems-performance.md) |
| Throughput | 吞吐，单位时间的处理量 | 与单请求延迟不同；说明有效交付分子 | [30](30-ai-systems-performance.md) |
| RAG | 检索增强生成 | 不只有向量数据库 | [11](11-rag-and-agents.md) |
| Reranker | 对检索候选再排序的组件 | 不能找回从未进入候选的文档 | [19](19-retrieval-engineering.md) |
| Tool calling | 模型提出结构化工具调用 | 提议与授权执行是两个环节 | [11](11-rag-and-agents.md) |
| Agent | 模型动态选择行动的系统范式 | 名称不说明具体自主范围 | [11](11-rag-and-agents.md) |
| Hallucination | 不正确或无依据的生成内容 | 低温度不能根除 | [12](12-evaluation.md) |
| Grounding | 将回答关联到可验证来源或环境 | 仅提供引用字符串还不够 | [12](12-evaluation.md) |
| Test-time compute | 测试时计算预算 | 更多计算不保证更准确 | [13](13-multimodal-and-reasoning.md) |

缩写的实际含义可能随论文或框架变化。阅读配置时应以对应实现为准。

## 基础、生成与系统资产

| 术语 | 含义与边界 | 正文 |
| --- | --- | --- |
| Generalization | 泛化，对未见输入的表现；训练拟合不等于泛化 | [23](23-machine-learning-foundations.md) |
| Self-supervised learning | 自监督，从数据构造目标；仍有学习信号 | [23](23-machine-learning-foundations.md) |
| Calibration | 校准，置信度与实际正确比例的关系 | [23](23-machine-learning-foundations.md) |
| Data lineage | 数据血缘，来源与派生处理关系 | [15](15-data-lifecycle.md) |
| VAE | 变分自编码器，学习可采样的潜在表示 | [24](24-generative-models.md) |
| GAN | 生成对抗网络，生成器与判别器共同训练 | [24](24-generative-models.md) |
| Diffusion | 扩散生成，训练与采样采用噪声相关机制 | [24](24-generative-models.md) |
| Flow Matching | 流匹配，学习分布路径上的向量场 | [24](24-generative-models.md) |
| RDMA | 远端内存访问；不自动替代应用协议 | [27](27-compute-infrastructure.md) |
| Prompt injection | 不可信内容改变模型指令处理 | [25](25-ai-security.md) |
| Model artifact | 模型资产，包含权重及匹配配置与处理器 | [26](26-model-lifecycle.md) |
| Checkpoint | 检查点，推理导出与训练恢复所需内容不同 | [17](17-training-engineering.md) |
| Idempotency | 幂等，同一业务意图重试不重复产生效果 | [20](20-agent-runtime.md) |
| MCP | 工具与资源连接协议；不是完整任务运行时 | [21](21-agent-planning-and-memory.md) |

## 编译、执行与现代并行

| 术语 | 含义与边界 | 正文 |
| --- | --- | --- |
| Eager Mode | 随程序运行分派张量算子；GPU 提交通常异步 | [28](28-ai-compiler-and-runtime.md) |
| Computational graph | 操作与数据依赖的表示，不是一次激活快照 | [28](28-ai-compiler-and-runtime.md) |
| Graph capture / graph break | 提取可编译片段 / 捕获边界中断；break 不等于 guard 失败后的重新编译 | [28](28-ai-compiler-and-runtime.md) |
| Guard / dynamic shape | 编译产物适用条件 / 符号形状约束；不保证任意输入无重编译 | [28](28-ai-compiler-and-runtime.md) |
| TorchDynamo | 从 Python 执行捕获张量图与适用条件 | [28](28-ai-compiler-and-runtime.md) |
| AOTAutograd | 提前构造可编译前向/反向及相关变换；不是完整离线部署的同义词 | [28](28-ai-compiler-and-runtime.md) |
| TorchInductor | 将图降低为调度、缓冲安排、代码与库调用的 backend | [28](28-ai-compiler-and-runtime.md) |
| Compiler IR | 中间表示；不同层级分别保留算子关系或循环/索引/布局 | [28](28-ai-compiler-and-runtime.md) |
| Operator / kernel fusion | 图上的融合区域 / 设备程序中的融合；一个区域未必一个 Kernel | [28](28-ai-compiler-and-runtime.md) |
| Memory planning | 根据存活与依赖安排缓冲；不等于整个进程的显存预算 | [28](28-ai-compiler-and-runtime.md) |
| Compile cache / autotuning | 复用兼容编译状态 / 测候选实现作选择；均依赖输入与环境 | [28](28-ai-compiler-and-runtime.md) |
| Triton | GPU Kernel 语言与编译器，不替代完整 CUDA 平台 | [28](28-ai-compiler-and-runtime.md) |
| CUDA Graph | 可重放的设备工作与依赖；不同于模型计算图，不自动融合 Kernel | [28](28-ai-compiler-and-runtime.md) |
| FlashAttention | IO-aware 精确 Attention 算法与 Kernel 实现家族 | [16](16-transformer-implementation.md)、[28](28-ai-compiler-and-runtime.md) |
| Device Mesh | rank 的逻辑多维组织；逻辑轴不是张量维度或物理链路保证 | [17](17-training-engineering.md) |
| DTensor / placement | 全局逻辑张量与本地片段关系；Replicate、Shard、Partial 定义布局 | [17](17-training-engineering.md) |
| FSDP2 | 逐参数 DTensor 分片的训练状态管理；计算前仍需取回参数 | [17](17-training-engineering.md) |
| TP / PP / EP | 矩阵/特征、层/阶段、专家的不同切分对象 | [17](17-training-engineering.md)、[10](10-serving-and-distributed.md) |
| Sequence Parallel (SP) | 本库采用 TP 语境，部分激活沿序列切分；命名依论文变化 | [17](17-training-engineering.md) |
| Context Parallel (CP) | 同一样本上下文位置跨 GPU 切分，Attention 保持跨位置依赖 | [17](17-training-engineering.md) |
| Ring Attention | 分块 K/V 轮转与在线归一化；不消除全注意力计算 | [17](17-training-engineering.md) |
| Distributed Checkpoint / reshard | 用逻辑状态元数据保存并按新布局加载；不保证任意版本/模型迁移 | [17](17-training-engineering.md) |

## 集群 Serving、调度与性能

| 术语 | 含义与边界 | 正文 |
| --- | --- | --- |
| P/D disaggregation | Prefill 与 Decode 独立资源池，需交接兼容 KV 与请求状态 | [10](10-serving-and-distributed.md) |
| Inference Gateway / Endpoint Picker | 请求入口与模型端点选择；不分配 GPU、不自动转移 KV | [10](10-serving-and-distributed.md)、[29](29-ai-platform-and-cluster-scheduling.md) |
| KV ownership | KV 引用归属与活动请求执行所有权需要分别跟踪 | [10](10-serving-and-distributed.md) |
| KV-aware / locality-aware / load-aware routing | 复用、数据/设备距离、预计负载的不同目标，可能冲突 | [10](10-serving-and-distributed.md) |
| Remote KV / KV offload | 从其他位置取兼容状态 / 状态离开紧缺层级；命中不等于本地可执行 | [18](18-inference-engineering.md) |
| Fleet-level serving | 多模型、副本与阶段池的协同服务及生命周期 | [10](10-serving-and-distributed.md) |
| Topology-aware placement / GPU + NIC affinity | 将逻辑通信组映射到实际互联和主机/网卡路径 | [29](29-ai-platform-and-cluster-scheduling.md) |
| Gang / coordinated scheduling | 组级足够资源 / 启动、就绪与恢复协同；调度成功不保证 Runtime 就绪 | [29](29-ai-platform-and-cluster-scheduling.md) |
| Device plugin / DRA | 设备报告与容器访问 / 更丰富的声明、匹配与准备机制 | [29](29-ai-platform-and-cluster-scheduling.md) |
| Quota / priority / preemption | 可用份额、竞争顺序、资源回收；不自动保证公平或无损恢复 | [29](29-ai-platform-and-cluster-scheduling.md) |
| Autoscaling / cold start / drain | 副本弹性 / 加载与预热 / 停止准入后完成或迁移活动工作 | [29](29-ai-platform-and-cluster-scheduling.md) |
| TPOT / ITL | 请求平均后续 Token 时间 / 相邻 Token 间隔；分布与计时边界不同 | [30](30-ai-systems-performance.md) |
| Tail latency / P99 | 慢样本与延迟分位；不能用阶段 P99 相加构造请求 P99 | [30](30-ai-systems-performance.md) |
| Useful throughput / goodput | 按质量与 SLO 定义的有效交付量；需另报到达、拒绝与失败 | [30](30-ai-systems-performance.md) |
| Roofline / arithmetic intensity | 运算上限与指定层级字节搬运的关系；不是可达性能保证 | [30](30-ai-systems-performance.md) |
| Amdahl’s Law | 固定工作量下局部优化受未优化部分限制 | [30](30-ai-systems-performance.md) |
| Little’s Law | 稳定边界中的平均在系统数量 = 到达率 × 平均停留；不是 P99 预测 | [30](30-ai-systems-performance.md) |
| SLO-driven capacity | 在质量、延迟、错误/准入约束下的服务容量 | [30](30-ai-systems-performance.md) |
| Cost per request / token | 同一窗口总资源成本除以明确口径的交付量 | [30](30-ai-systems-performance.md) |

## 训练数据、RL 闭环与可观测性

| 术语 | 含义与边界 | 正文 |
| --- | --- | --- |
| Dataset manifest / version | 内容与处理清单 / 不可变数据快照；可变地址不是充分版本 | [15](15-data-lifecycle.md) |
| Mixture / sampling weight | 数据源组合与抽样权重；样本份额不等于 Token 份额 | [15](15-data-lifecycle.md) |
| Token budget / epoch | 按明确 Token 口径的进度 / 固定集合一轮；无限流不必有自然 epoch | [15](15-data-lifecycle.md) |
| Sample / sequence packing | 长度编组或同序列拼接；必须保护样本边界与监督语义 | [15](15-data-lifecycle.md) |
| Streaming dataset / shard | 按需迭代 / I/O 与归属单位；分片不自动解决 mixture 与重复读取 | [15](15-data-lifecycle.md) |
| Distributed sampler / loader resume | rank 样本分配 / 消费进度恢复；预取位置不等于已训练位置 | [15](15-data-lifecycle.md) |
| Synthetic data / contamination | 合成数据 / 评测信息进入训练或开发链；生成数据也需血缘与独立验证 | [15](15-data-lifecycle.md) |
| Rollout / trajectory | 策略与环境交互生成 / 带版本、动作/观察和完成状态的训练对象 | [08](08-post-training.md) |
| Behavior policy / reference model | 本轨迹采样策略 / 目标中的参考约束；不等于更新中的 trainer | [08](08-post-training.md) |
| Rollout buffer / policy staleness | 待消费轨迹及提交状态 / 采样与训练策略偏离；不能只用墙钟时间定义 | [08](08-post-training.md) |
| On-policy / asynchronous rollout | 与算法采样策略匹配 / 采样训练重叠；异步不是任意旧样本都可用 | [08](08-post-training.md) |
| Weight sync / trainer-rollout disaggregation | 一致版本发布 / 训练和生成分池；需要布局转换与组级切换 | [17](17-training-engineering.md) |
| Observability | 用相关证据解释状态与依赖；不是监控指标数量 | [31](31-ai-systems-observability-and-debugging.md) |
| Metric / log | 窗口聚合 / 对象事件；单独无法恢复完整因果链 | [31](31-ai-systems-observability-and-debugging.md) |
| Trace / span / context propagation | 操作依赖、阶段与跨组件关联；业务 request ID 不必等于 trace ID | [31](31-ai-systems-observability-and-debugging.md) |
| Event timeline / profiler | 事件执行依赖 / 调用与设备成本；CPU 提交不等于 GPU 完成 | [31](31-ai-systems-observability-and-debugging.md) |
| NVTX / hardware counter | 时间线语义标注 / 设备特征计数；marker 不是测量器，counter 不是业务正确性 | [31](31-ai-systems-observability-and-debugging.md) |
| GPU utilization / occupancy | 特定口径设备忙碌 / 驻留执行资源比例；二者不等于有效吞吐 | [31](31-ai-systems-observability-and-debugging.md) |
| Straggler / exposed communication | 同步组迟到参与者 / 未被其他工作隐藏的通信等待 | [31](31-ai-systems-observability-and-debugging.md) |
| Allocator reserved / fragmentation | allocator 保留容量 / 分配布局碎片；保留中活跃字节不可重复相加 | [31](31-ai-systems-observability-and-debugging.md) |

## 推理时求解与端到端整合

| 术语 | 含义与边界 | 正文 |
| --- | --- | --- |
| Candidate / branch state | 答案或中间候选及 parent、历史、评分与完成状态；不是只有一段文本 | [13](13-multimodal-and-reasoning.md) |
| Parallel sampling / best-of-N | 多候选生成 / 生成后选择；不保证全部同时执行或选择正确 | [13](13-multimodal-and-reasoning.md) |
| Verifier-guided search / iterative refinement | 验证信号引导扩展 / 候选与反馈进入下一轮；评分不是普遍正确性证明 | [13](13-multimodal-and-reasoning.md) |
| Reasoning budget / early stop | 任务级计算、Token、时间与验证预算 / 停止扩展；预算耗尽不等于成功 | [13](13-multimodal-and-reasoning.md) |
| Compiled artifact / running replica | 有适用条件的执行产物 / 具体资源上的运行实例；二者都不同于 checkpoint | [28](28-ai-compiler-and-runtime.md)、[14](14-end-to-end-case.md) |
| Model Ready / SLO-ready | 模型执行路径就绪 / 约定负载下通过服务目标验收；Pod Running 不能替代 | [29](29-ai-platform-and-cluster-scheduling.md)、[14](14-end-to-end-case.md) |
| Application / cluster / inference scheduler | 分别调度 Task/Tool、GPU/Replica、Token/KV；Router 另选择请求落点 | [14](14-end-to-end-case.md) |

## 混合状态与长任务执行

| 术语 | 含义与边界 | 正文 |
| --- | --- | --- |
| Gated Delta Network | 门控与增量更新的递归状态模块，可与 Attention、MoE 组合 | [06](06-modern-architectures.md) |
| CED / CSA2 | 具体模型中的阶段分工 / 压缩稀疏注意力与跨层复用设计，不是通用缓存开关 | [06](06-modern-architectures.md) |
| SWA bounded replay | 有限后缀近似恢复滑窗状态；不保证完整前向等价 | [18](18-inference-engineering.md) |
| Confidence-scheduled verification | 依据草稿接受概率估计与引擎特征分配验证长度，仍需目标验证 | [18](18-inference-engineering.md) |
| Batch-invariant inference | 约束请求在不同组批下的计算结果；不同于固定采样种子 | [18](18-inference-engineering.md) |
| Harness / Session / Sandbox | 执行控制框架 / 持久事件记录 / 工具执行环境，具有不同生命周期 | [20](20-agent-runtime.md) |
| Compaction / context reset | 压缩当前上下文 / 重建输入视图继续任务，都需可靠交接记录 | [21](21-agent-planning-and-memory.md) |
