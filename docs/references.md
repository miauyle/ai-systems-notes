# 原始资料与延伸阅读

[返回目录](../README.md)

本表提供稳定知识的原始资料入口，不是实时模型榜单。关键原理在对应章节解释，资料用于追溯依据。2026-10-01 核对了核心计算、训练、缓存、并行、检索与运行时的部分论文和官方机制文档，并补充对应入口；未对所有历史外链逐一确认。官方接口和支持条件以所用版本为准。

2026-10-03 补充并查阅 Compiler、现代并行、DCP、集群调度、KV-aware routing 与性能模型的官方资料/原始论文入口。`stable`、`main`、`dev` 文档可能移动或改变支持范围，本文不固定未验证的版本默认行为；部分新接口仍在演进。章内区分稳定机制、具体实现和设计合同，未声称复现论文性能。

同日端到端整合时查阅下表 Test-Time Compute Scaling、Tree of Thoughts、Self-Refine 与 Let's Verify Step by Step 原始论文入口，用于第 13 章区分求解、验证、搜索与预算；不将某种候选架构或实验收益当作 reasoning model 的统一实现。

## 核心结构与表示

| 资料 | 年份 | 支持本仓库中的主题 | 建议先看 |
| --- | --- | --- | --- |
| [Attention Is All You Need](https://arxiv.org/abs/1706.03762) | 2017 | Transformer、Attention、FFN、位置编码 | 架构图与第 3 节 |
| [BERT](https://arxiv.org/abs/1810.04805) | 2018 | Encoder-only 与双向表示 | 模型和预训练目标 |
| [T5](https://arxiv.org/abs/1910.10683) | 2019 | Encoder–Decoder 与统一文本任务 | 方法图与任务格式 |
| [Neural Machine Translation of Rare Words with Subword Units](https://arxiv.org/abs/1508.07909) | 2015 | 子词 BPE | 算法示例 |
| [SentencePiece](https://arxiv.org/abs/1808.06226) | 2018 | Tokenization 工具与表示 | 方法与使用方式 |
| [RMSNorm](https://arxiv.org/abs/1910.07467) | 2019 | 均方根归一化 | 归一化公式 |
| [GLU Variants Improve Transformer](https://arxiv.org/abs/2002.05202) | 2020 | 门控 FFN 与 SwiGLU | 变体公式与成本比较 |
| [RoFormer / RoPE](https://arxiv.org/abs/2104.09864) | 2021 | 旋转位置编码 | 相对位置机制 |

## 现代架构与效率

| 资料 | 年份 | 主题 | 建议先看 |
| --- | --- | --- | --- |
| [Multi-Query Attention](https://arxiv.org/abs/1911.02150) | 2019 | MQA 与增量推理带宽 | 头共享机制 |
| [GQA](https://arxiv.org/abs/2305.13245) | 2023 | 分组查询注意力 | MHA/MQA/GQA 对照图 |
| [Switch Transformers](https://arxiv.org/abs/2101.03961) | 2021 | 稀疏 MoE 路由与负载 | 专家结构与稳定训练 |
| [Mixtral of Experts](https://arxiv.org/abs/2401.04088) | 2024 | MoE 的具体实例 | 总参数和激活参数 |
| [FlashAttention](https://arxiv.org/abs/2205.14135) | 2022 | IO-aware 精确注意力 | 分块与内存访问 |
| [Mamba](https://arxiv.org/abs/2312.00752) | 2023 | 选择性状态空间模型 | 状态选择与实现思路 |

## 训练与适配

| 资料 | 年份 | 主题 | 建议先看 |
| --- | --- | --- | --- |
| [Training Compute-Optimal Large Language Models](https://arxiv.org/abs/2203.15556) | 2022 | 参数、数据与计算预算 | 计算最优的前提与实验 |
| [InstructGPT](https://arxiv.org/abs/2203.02155) | 2022 | SFT、偏好、奖励模型与 RLHF | 训练流程图 |
| [Direct Preference Optimization](https://arxiv.org/abs/2305.18290) | 2023 | DPO | 偏好目标与参考模型 |
| [LoRA](https://arxiv.org/abs/2106.09685) | 2021 | 低秩适配 | 低秩更新图与公式 |
| [QLoRA](https://arxiv.org/abs/2305.14314) | 2023 | 量化基础模型上的微调 | 内存机制与实验设置 |
| [ZeRO](https://arxiv.org/abs/1910.02054) | 2019 | 分片训练状态 | 各阶段分片对象 |
| [Megatron-LM](https://arxiv.org/abs/1909.08053) | 2019 | TP 矩阵切分与层内通信 | FFN 与 Attention 划分图 |
| [PyTorch Distributed](https://arxiv.org/abs/2006.15704) | 2020 | DDP 梯度桶、通信重叠与跳过同步 | 反向就绪与归约顺序 |

## 服务、应用与多模态

| 资料 | 年份 | 主题 | 建议先看 |
| --- | --- | --- | --- |
| [PagedAttention / vLLM](https://arxiv.org/abs/2309.06180) | 2023 | KV 管理与服务 | 分页设计及负载假设 |
| [Orca](https://www.usenix.org/conference/osdi22/presentation/yu) | 2022 | 迭代级服务调度 | 请求加入与完成的调度边界 |
| [DistServe](https://arxiv.org/abs/2401.09670) | 2024 | Prefill / Decode 分离 | 阶段隔离、KV 传输与服务目标 |
| [Speculative Decoding](https://arxiv.org/abs/2211.17192) | 2022 | 草稿与验证 | 采样正确性及加速条件 |
| [Retrieval-Augmented Generation](https://arxiv.org/abs/2005.11401) | 2020 | 检索增强生成 | 原始方法与实验任务 |
| [ReAct](https://arxiv.org/abs/2210.03629) | 2022 | 推理与行动交织的 Agent 范式 | 工具反馈循环 |
| [Vision Transformer](https://arxiv.org/abs/2010.11929) | 2020 | 图像 patch 表示 | 图像到序列的结构图 |
| [LLaVA / Visual Instruction Tuning](https://arxiv.org/abs/2304.08485) | 2023 | 视觉与语言连接、指令训练 | 编码器与投影结构 |
| [DeepSeek-R1](https://arxiv.org/abs/2501.12948) | 2025 | 推理训练的公开实例 | 训练策略与评估限制 |

## 深入机制的资料

| 资料 | 年份 | 对应内容 |
| --- | --- | --- |
| [Adam](https://arxiv.org/abs/1412.6980) | 2014 | 一阶/二阶矩、偏差修正与优化更新 |
| [Decoupled Weight Decay Regularization](https://arxiv.org/abs/1711.05101) | 2017 | AdamW 与权重衰减 |
| [Dense Passage Retrieval](https://arxiv.org/abs/2004.04906) | 2020 | 稠密检索的训练与评估 |
| [Sentence-BERT](https://arxiv.org/abs/1908.10084) | 2019 | 句向量与相似度任务 |
| [BM25 综述](https://doi.org/10.1561/1500000019) | 2009 | 词频、逆文档频率与长度归一化 |
| [Reciprocal Rank Fusion 作者版](https://plg.uwaterloo.ca/~gvcormac/cormacksigir09-rrf.pdf) | 2009 | 基于名次的检索融合；[DOI](https://doi.org/10.1145/1571941.1572114) |
| [HNSW](https://arxiv.org/abs/1603.09320) | 2016 | 近似最近邻与图索引 |
| [Reflexion](https://arxiv.org/abs/2303.11366) | 2023 | 反馈与记忆机制的研究实例 |
| [MemGPT](https://arxiv.org/abs/2310.08560) | 2023 | 上下文与外部记忆管理的实例 |

这些论文是具体方案，不能由某个实验结果推断所有 Agent 或业务任务都有效。正文描述机制与适用边界，不宣称复现上述项目。

## 官方实现与文档

- [Hugging Face Transformers 文档](https://huggingface.co/docs/transformers/index)：模型配置、Tokenizer、生成与部署接口。
- [Hugging Face Tokenizers 文档](https://huggingface.co/docs/tokenizers/index)：实际切分、编码和训练流程。
- [PyTorch 文档](https://docs.pytorch.org/docs/stable/index.html)：张量、自动微分、分布式与数值实现。
- [vLLM 文档](https://docs.vllm.ai/)：服务配置、支持的模型与运行条件。
- [Hugging Face Cache Explanation](https://huggingface.co/docs/transformers/cache_explanation)：逐层 KV、增量位置与有效历史。
- [vLLM Prefix Caching 设计](https://docs.vllm.ai/en/latest/design/prefix_caching/)：前缀块标识、引用与分配流程；属于具体引擎实现。
- [PyTorch FSDP 教程](https://docs.pytorch.org/tutorials/intermediate/FSDP_tutorial.html)：参数取回、梯度分发、分片更新与预取。
- [NCCL 集体操作](https://docs.nvidia.com/deeplearning/nccl/user-guide/docs/usage/collectives.html)：归约、拼接、分发与 rank 对应关系。
- [CUDA 异步执行](https://docs.nvidia.com/cuda/cuda-programming-guide/02-basics/asynchronous-execution.html)：stream、事件、传输与执行依赖。
- [SentencePiece 项目](https://github.com/google/sentencepiece)：Tokenization 工具。
- [GitHub Markdown 文档](https://docs.github.com/en/get-started/writing-on-github)：公式、Mermaid 与 Markdown 阅读方式。
- [MCP 规范](https://modelcontextprotocol.io/specification/latest)：工具与资源的协议约定；版本随规范演进。
- [LangGraph 文档](https://docs.langchain.com/oss/python/langgraph/overview)：状态、图编排和运行时机制的实现入口。
- [LangGraph 持久化](https://docs.langchain.com/oss/python/langgraph/persistence)：检查点与恢复的实现参考，不等于外部副作用的 exactly-once 保证。

官方文档会更新，使用具体接口时记录版本。论文年份按本表所链接预印本的首次发布年份，可能与会议发表年份不同。

## 怎样读论文更省力？

先看问题、架构图、输入输出和实验设置，再读公式。检查结论适用的模型规模、数据、硬件与负载，不把论文中的某个加速倍数当作所有环境的保证。

资料按专题使用，阅读关系见[知识地图](00-learning-roadmap.md)。不要求按论文列表逐篇完成。

## Compiler / Runtime 与 Kernel

| 官方资料或原始论文 | 支持的关系 | 对应正文 |
| --- | --- | --- |
| [PyTorch Compiler](https://docs.pytorch.org/docs/stable/torch.compiler.html) | Eager、捕获与编译的责任边界 | [28](28-ai-compiler-and-runtime.md) |
| [PyTorch Compiler FAQ](https://docs.pytorch.org/docs/stable/user_guide/torch_compiler/torch.compiler_faq.html) | TorchDynamo、AOTAutograd 与 TorchInductor 的组合 | [28](28-ai-compiler-and-runtime.md) |
| [PyTorch 2 论文](https://pytorch.org/assets/pytorch2-2.pdf) | Python 图捕获、IR 与代码生成设计 | [28](28-ai-compiler-and-runtime.md) |
| [Dynamic Shapes](https://docs.pytorch.org/docs/main/user_guide/torch_compiler/torch.compiler_dynamic_shapes.html) | 符号维度、guard 与重新编译边界 | [28](28-ai-compiler-and-runtime.md) |
| [Compile Time Caching](https://docs.pytorch.org/tutorials/recipes/torch_compile_caching_configuration_tutorial.html) | 图/编译产物缓存与冷启动 | [28](28-ai-compiler-and-runtime.md) |
| [Triton](https://triton-lang.org/main/index.html) | 分块 Kernel 语言与编译器 | [28](28-ai-compiler-and-runtime.md) |
| [CUDA Graphs](https://docs.nvidia.com/cuda/cuda-programming-guide/04-special-topics/cuda-graphs.html) | 定义、实例化、工作提交与重放 | [28](28-ai-compiler-and-runtime.md) |
| [CUDA Programming Guide](https://docs.nvidia.com/cuda/cuda-programming-guide/) | Kernel、内存与异步执行平台 | [27](27-compute-infrastructure.md)、[28](28-ai-compiler-and-runtime.md) |

FlashAttention 原论文入口见“现代架构与效率”，本库将其定位为 IO-aware Attention 算法与 Kernel 实现家族，不是通用 Compiler 或模型结构。

## Modern Parallelism 与恢复

| 官方资料或原始论文 | 支持的关系 | 对应正文 |
| --- | --- | --- |
| [DeviceMesh](https://docs.pytorch.org/docs/stable/distributed.html#devicemesh) | 逻辑设备轴、rank 组与物理映射 | [17](17-training-engineering.md)、[29](29-ai-platform-and-cluster-scheduling.md) |
| [DTensor](https://docs.pytorch.org/docs/stable/distributed.tensor.html) | Replicate / Shard / Partial 与布局转换 | [17](17-training-engineering.md) |
| [FSDP2 / fully_shard](https://docs.pytorch.org/docs/stable/distributed.fsdp.fully_shard.html) | 逐参数分片、取回与 TP 组合 | [17](17-training-engineering.md) |
| [PyTorch Tensor Parallel](https://docs.pytorch.org/tutorials/intermediate/TP_tutorial.html) | TP、SP 与 FSDP 组合的具体语境 | [17](17-training-engineering.md) |
| [PyTorch Context Parallel](https://docs.pytorch.org/tutorials/unstable/context_parallel.html) | 序列分片、pass-KV 与位置/通信约束；实现仍演进 | [17](17-training-engineering.md) |
| [Ring Attention](https://arxiv.org/abs/2310.01889)（2023） | 分块 K/V 轮转、全局 Attention 与通信重叠 | [17](17-training-engineering.md) |
| [Distributed Checkpoint](https://docs.pytorch.org/docs/stable/distributed.checkpoint.html) | 分片元数据、持久保存、拓扑变化与 reshard | [17](17-training-engineering.md) |
| [NCCL Collectives](https://docs.nvidia.com/deeplearning/nccl/user-guide/docs/usage/collectives.html) | 参数/梯度/激活交换的基本语义 | [17](17-training-engineering.md)、[27](27-compute-infrastructure.md) |

SP 命名在不同论文中不完全一致，应核对实际算法。DCP 的支持布局、存储 backend 与版本兼容范围以所用实现为准，不能将张量重分片推断成任意训练策略的无损迁移。

## Cluster Scheduling 与 Distributed Serving

| 官方资料或原始论文 | 支持的关系 | 对应正文 |
| --- | --- | --- |
| [Kubernetes GPU Scheduling](https://kubernetes.io/docs/tasks/manage-gpus/scheduling-gpus/) | GPU 资源请求、能力与节点选择 | [29](29-ai-platform-and-cluster-scheduling.md) |
| [Device Plugins](https://kubernetes.io/docs/concepts/extend-kubernetes/compute-storage-net/device-plugins/) | 设备健康、资源报告与容器访问 | [29](29-ai-platform-and-cluster-scheduling.md) |
| [Dynamic Resource Allocation](https://kubernetes.io/docs/concepts/resource-management/dynamic-resource-allocation/) | DeviceClass / Claim / Slice 与设备准备 | [29](29-ai-platform-and-cluster-scheduling.md) |
| [Topology Manager](https://kubernetes.io/docs/tasks/administer-cluster/topology-manager/) | CPU、设备与 NUMA locality；不等于全集群网络优化 | [29](29-ai-platform-and-cluster-scheduling.md) |
| [Gang Scheduling](https://kubernetes.io/docs/concepts/scheduling-eviction/gang-scheduling/) | 组级可调度性与启动条件；启用范围依版本 | [29](29-ai-platform-and-cluster-scheduling.md) |
| [Resource Quotas](https://kubernetes.io/docs/concepts/policy/resource-quotas/) / [Priority and Preemption](https://kubernetes.io/docs/concepts/scheduling-eviction/pod-priority-preemption/) | 租户限额、优先级和资源回收 | [29](29-ai-platform-and-cluster-scheduling.md) |
| [Gateway API Inference Extension](https://gateway-api-inference-extension.sigs.k8s.io/) / [InferencePool](https://gateway-api-inference-extension.sigs.k8s.io/api-types/inferencepool/) | 服务池、Gateway 与 Endpoint Picker 的职责 | [10](10-serving-and-distributed.md)、[29](29-ai-platform-and-cluster-scheduling.md) |
| [NVIDIA Dynamo KV-aware Routing](https://docs.nvidia.com/dynamo/dev/knowledge-base/concepts/system-architecture/kv-aware-routing) | 缓存事件、预计负载与 P/D 目标选择 | [10](10-serving-and-distributed.md) |
| [vLLM Disaggregated Prefill](https://docs.vllm.ai/en/latest/features/disagg_prefill/) | 独立引擎之间的 KV 交接实现入口 | [10](10-serving-and-distributed.md)、[18](18-inference-engineering.md) |
| [DistServe](https://arxiv.org/abs/2401.09670)（2024） | P/D 解耦、传输、放置与 SLO 条件 | [10](10-serving-and-distributed.md)、[30](30-ai-systems-performance.md) |
| [PagedAttention](https://arxiv.org/abs/2309.06180)（2023） | 分页、块引用与服务容量的关系 | [18](18-inference-engineering.md) |

选择 worker、传输 KV 和推进请求所有权是不同责任；正文中的可恢复交接表是设计合同，不冒称上述实现都提供相同状态机或流式 exactly-once 交付。

## Performance Model、排队与 SLO

| 官方资料或原始论文 | 支持的关系 | 对应正文 |
| --- | --- | --- |
| [vLLM Metrics](https://docs.vllm.ai/en/stable/design/metrics/) | TTFT、TPOT/ITL、请求与迭代的计时口径 | [30](30-ai-systems-performance.md) |
| [Roofline](https://escholarship.org/uc/item/78h8v7mr)（2009） | 算术强度、带宽与算力上限 | [30](30-ai-systems-performance.md) |
| [Amdahl 原始论文](https://doi.org/10.1145/1465482.1465560)（1967） | 固定工作量下局部加速与整体上限 | [30](30-ai-systems-performance.md) |
| [Little：A Proof for the Queuing Formula](https://doi.org/10.1287/opre.9.3.383)（1961） | 稳定系统平均数量、到达率与停留时间 | [30](30-ai-systems-performance.md) |

排队关系与 Roofline 是带前提的分析工具，不独立预测生产 P99、通用 GPU speedup 或真实可交付容量。正文以一致负载和质量/SLO 约束解释测量。

## Observability、Profiling 与故障定位

| 一手资料 | 支持的机制 | 正文 |
| --- | --- | --- |
| [PyTorch Profiler](https://docs.pytorch.org/docs/stable/profiler.html) | 框架算子、CPU/设备活动与采集开销 | [31](31-ai-systems-observability-and-debugging.md) |
| [CUDA 异步执行](https://docs.nvidia.com/cuda/cuda-programming-guide/02-basics/asynchronous-execution.html) | stream、event、提交与完成依赖 | [27](27-compute-infrastructure.md)、[31](31-ai-systems-observability-and-debugging.md) |
| [Nsight Systems User Guide](https://docs.nvidia.com/nsight-systems/UserGuide/index.html) | CPU/CUDA/复制与 NVTX 关联的系统时间线 | [31](31-ai-systems-observability-and-debugging.md) |
| [Nsight Compute Profiling Guide](https://docs.nvidia.com/nsight-compute/ProfilingGuide/index.html) | Kernel counter、replay 与测量扰动 | [31](31-ai-systems-observability-and-debugging.md) |
| [NCCL Troubleshooting](https://docs.nvidia.com/deeplearning/nccl/user-guide/docs/troubleshooting.html) / [Environment Variables](https://docs.nvidia.com/deeplearning/nccl/user-guide/docs/env.html) | 网络/系统检查与受控 debug 信息；选项依版本 | [31](31-ai-systems-observability-and-debugging.md) |
| [PyTorch Flight Recorder](https://docs.pytorch.org/tutorials/unstable/flight_recorder_tutorial.html) | 通信组、collective 序号与跨 rank 停滞分析 | [31](31-ai-systems-observability-and-debugging.md) |
| [Understanding CUDA Memory Usage](https://docs.pytorch.org/docs/stable/torch_cuda_memory) | allocator snapshot 与不可见的外部分配边界 | [31](31-ai-systems-observability-and-debugging.md) |
| [OpenTelemetry Context Propagation](https://opentelemetry.io/docs/concepts/context-propagation/) / [Traces](https://opentelemetry.io/docs/concepts/signals/traces/) | trace/span 关联与跨进程传播，不是 GPU profiler | [31](31-ai-systems-observability-and-debugging.md) |
| [vLLM Metrics](https://docs.vllm.ai/en/stable/design/metrics/) | 引擎计时、请求/迭代指标的具体实例 | [30](30-ai-systems-performance.md)、[31](31-ai-systems-observability-and-debugging.md) |

## Reasoning / RL Training Systems

| 原始论文 / 官方项目 | 支持的机制 | 正文 |
| --- | --- | --- |
| [HybridFlow](https://arxiv.org/abs/2409.19256)（2024） / [verl](https://github.com/verl-project/verl) | RL 数据流、训练/生成并行布局和资源组织的实现实例 | [08](08-post-training.md)、[17](17-training-engineering.md) |
| [AReaL](https://arxiv.org/abs/2505.24298)（2025） / [官方项目](https://github.com/areal-project/AReaL) | 异步生成/训练、陈旧轨迹与算法适配的实例 | [08](08-post-training.md)、[17](17-training-engineering.md) |

算法流程依据还包括前表 InstructGPT 与 DeepSeek-R1。论文/实现中的吞吐或质量结论有具体实验条件，不能推出所有异步 RL 都兼容任意旧样本或都有同等收益；本文不绑定某框架的默认参数。

## Inference-Time Reasoning Systems

| 原始论文 | 支持的机制与边界 | 正文 |
| --- | --- | --- |
| [Scaling LLM Test-Time Compute Optimally can be More Effective than Scaling Model Parameters](https://arxiv.org/abs/2408.03314)（2024） | 验证搜索、响应修订与按任务分配计算；效果依任务难度、模型及策略 | [13](13-multimodal-and-reasoning.md) |
| [Tree of Thoughts](https://arxiv.org/abs/2305.10601)（2023） | 中间状态分支、评估、扩展与回溯的实例；不是全部模型的内部结构 | [13](13-multimodal-and-reasoning.md) |
| [Self-Refine](https://arxiv.org/abs/2303.17651)（2023） | 生成、反馈和迭代修订；反馈仍可能错误 | [13](13-multimodal-and-reasoning.md) |
| [Let's Verify Step by Step](https://arxiv.org/abs/2305.20050)（2023） | Outcome / process supervision 与步骤评分研究；评分不等于普遍正确性证明 | [13](13-multimodal-and-reasoning.md) |

分支资源和生命周期表表达一般系统设计责任，不宣称上述论文都提供同一种 KV 管理、持久恢复或生产调度协议。训练时 RL 的策略更新入口见前表，第 14 章将资产、执行和生产请求接成完整路径。

## Training Data Engineering

| 一手资料 | 支持的机制 | 正文 |
| --- | --- | --- |
| [PyTorch Data](https://docs.pytorch.org/docs/stable/data.html) | Dataset/DataLoader、IterableDataset worker、DistributedSampler 的补齐/丢尾与 shuffle | [15](15-data-lifecycle.md) |
| [Hugging Face Dataset Streaming](https://huggingface.co/docs/datasets/stream) | shard、有限 buffer shuffle、interleave 与可恢复范围 | [15](15-data-lifecycle.md) |
| [Deduplicating Training Data Makes Language Models Better](https://aclanthology.org/2022.acl-long.577/)（预印本 2021） / [官方代码](https://github.com/google-research/deduplicate-text-datasets) | 重复与训练/评测泄漏的原始研究 | [15](15-data-lifecycle.md) |
| [DoReMi](https://arxiv.org/abs/2305.10429)（2023） | 数据域加权与训练 mixture 的研究实例 | [15](15-data-lifecycle.md) |
| [Rethinking Benchmark and Contamination with Rephrased Samples](https://arxiv.org/abs/2311.04850)（2023） | 仅精确匹配不能覆盖改写污染 | [15](15-data-lifecycle.md) |

2026-10-03 本轮补充并查阅上述 profiling、通信诊断、RL 系统与训练数据一手入口。官方实现支持和数据恢复精度可能变化；manifest/消费提交等正文表格是应明确的设计责任，不声称所有 loader 或框架天然提供完全相同的合同。未对全部历史外链重新逐一验证。

## 通用学习与生成家族及基础资料

| 资料 | 年份 | 对应主题 |
| --- | --- | --- |
| [Deep Learning](https://www.deeplearningbook.org/) | 2016 | 学习目标、表示、泛化与优化基础 |
| [scikit-learn 模型选择](https://scikit-learn.org/stable/model_selection.html) | 持续更新 | 数据划分、模型选择与评估 |
| [scikit-learn 常见问题](https://scikit-learn.org/stable/common_pitfalls.html) | 持续更新 | 预处理与数据泄漏 |
| [Auto-Encoding Variational Bayes](https://arxiv.org/abs/1312.6114) | 2013 | VAE 的编码、潜在分布与解码 |
| [Generative Adversarial Networks](https://arxiv.org/abs/1406.2661) | 2014 | 生成器与判别器的对抗学习 |
| [Denoising Diffusion Probabilistic Models](https://arxiv.org/abs/2006.11239) | 2020 | 扩散训练与采样 |
| [Latent Diffusion Models](https://arxiv.org/abs/2112.10752) | 2021 | 潜在空间中的生成与条件 |
| [Flow Matching for Generative Modeling](https://arxiv.org/abs/2210.02747) | 2022 | 概率路径与向量场学习 |
| [CLIP](https://arxiv.org/abs/2103.00020) | 2021 | 图文表示与对比学习 |

对应正文：[机器学习基础](23-machine-learning-foundations.md)、[生成模型](24-generative-models.md)、[多模态](13-multimodal-and-reasoning.md)。

## 资产、基础设施与安全

| 官方资料 | 支持的知识关系 |
| --- | --- |
| [Hugging Face 模型配置](https://huggingface.co/docs/transformers/main_classes/configuration) | 配置与模型资产 |
| [Hugging Face 聊天模板](https://huggingface.co/docs/transformers/chat_templating) | 消息序列与训练格式 |
| [CUDA 编程指南](https://docs.nvidia.com/cuda/cuda-programming-guide/) | 加速器执行与内存层级 |
| [NCCL](https://docs.nvidia.com/deeplearning/nccl/user-guide/docs/) | 集体通信与设备拓扑 |
| [GPUDirect RDMA](https://docs.nvidia.com/cuda/gpudirect-rdma/) | 网卡与 GPU 的直接路径及支持条件 |
| [GPUDirect Storage](https://docs.nvidia.com/gpudirect-storage/overview-guide/index.html) | 存储与 GPU 数据路径 |
| [Faiss](https://github.com/facebookresearch/faiss/wiki) | 向量索引、近似与压缩 |
| [MCP 2025-06-18 架构](https://modelcontextprotocol.io/specification/2025-06-18/architecture) | host、client、server 与连接能力 |
| [OWASP GenAI](https://genai.owasp.org/) | 提示注入、权限与应用安全 |
| [NIST AI RMF](https://www.nist.gov/itl/ai-risk-management-framework) | 系统风险与治理关系 |

对应正文：[模型生命周期](26-model-lifecycle.md)、[计算基础设施](27-compute-infrastructure.md)、[安全边界](25-ai-security.md)。这些入口的存在不代表所有平台组合都受支持，部署时仍须核对实际版本。

## 2026 年机制演进与官方案例

2026-10-21 查阅以下官方仓库、论文和发布说明，补充现有章节；覆盖 2—10 月的机制演进，重点包含近期 8—10 月资料，不声称每项都是本月新技术。动态仓库按核实日期描述，部署需另固定 commit、模型与环境版本；未运行这些模型、复现性能或逐一复查全部历史外链。

| 原始资料 | 日期 / 范围 | 支持的更新与章节 |
| --- | --- | --- |
| [Qwen3.5—3.8 官方仓库](https://github.com/QwenLM/Qwen3.8) | 2026；3.8 开放模型 8 月发布 | 混合状态、推理投入与历史 thinking，[06](06-modern-architectures.md)、[13](13-multimodal-and-reasoning.md) |
| [DeepSeek-V4.1-Flash](https://arxiv.org/abs/2609.19969)、[正文](https://arxiv.org/html/2609.19969v1) | 2026-09；架构与第 3.2 节 | CED、CSA2 与近似 bounded replay，[06](06-modern-architectures.md)、[18](18-inference-engineering.md) |
| [DeepInfer README](https://github.com/deepseek-ai/DeepInfer) | 2026-10-21 查阅 | 离线引擎与完整服务边界、异步调度、确定性条件，[10](10-serving-and-distributed.md)、[18](18-inference-engineering.md)、[28](28-ai-compiler-and-runtime.md) |
| [FlashMLA](https://github.com/deepseek-ai/FlashMLA)、[DeepGEMM](https://github.com/deepseek-ai/DeepGEMM)、[DeepEP](https://github.com/deepseek-ai/DeepEP)、[TileLang](https://github.com/tile-ai/tilelang) | 官方实现；具体版本各异 | 专用内核、通信与 DSL 的职责，[28](28-ai-compiler-and-runtime.md) |
| [DSpark](https://arxiv.org/abs/2607.05147)、[DeepSpec](https://github.com/deepseek-ai/DeepSpec) | 论文 2026-07；实现按查阅日 | 草稿训练、验证长度与负载，[18](18-inference-engineering.md) |
| [DSec](https://arxiv.org/abs/2609.22978) | 2026-09 | Agent 训练环境池与生命周期，[08](08-post-training.md)、[29](29-ai-platform-and-cluster-scheduling.md) |
| [Managed Agents](https://www.anthropic.com/engineering/managed-agents) | 2026-04-08 | Harness、Session、Sandbox 分离，[20](20-agent-runtime.md) |
| [长应用开发 Harness](https://www.anthropic.com/engineering/harness-design-long-running-apps) | 2026-03-24 | 交接、重置与模型适配，[21](21-agent-planning-and-memory.md) |
| [基础设施噪声与 Agent 评估](https://www.anthropic.com/engineering/infrastructure-noise) | 2026-02-05 | 环境配置与比较口径，[22](22-evaluation-and-production.md) |
| [非预期模型动作调查](https://www.anthropic.com/research/investigating-unintended-model-actions) | 2026-10-09；10-12 更正 | 环境阻塞与动作边界，[25](25-ai-security.md) |
| [Qwen3.8-LiveTranslate](https://qwen.ai/blog?id=qwen3.8-livetranslate) | 2026-09-18 | 音频文本交错与流式状态，[13](13-multimodal-and-reasoning.md) |
| [MiniMax M3.1 Flash](https://www.minimax.io/blog/minimax-m31f)、[Gemini 4 Argon 开发者发布](https://blog.google/innovation-and-ai/technology/developers-tools/build-with-gemini-4-argon/) | 2026-10-19 / 10-13 | 任务成本与发布边界案例；不推断内部架构，[30](30-ai-systems-performance.md) |
