# 建筑学

如[区块链基础](https://docs.substrate.io/fundamentals/blockchain-basics/)中所述，区块链依赖于分散的计算机网络（称为节点），它们相互通信。

由于节点是任何区块链的核心组件，因此了解 Substrate 节点的独特之处非常重要，包括默认提供的核心服务和库，以及如何自定义和扩展节点以适应不同的项目目标。

## 高级概述
在去中心化网络中，所有节点都充当请求数据的客户端和响应数据请求的服务器。在概念上和程序上，Substrate 架构按照类似的路线划分操作职责。下图以简化的形式说明了这种职责分离，以帮助您可视化架构以及 Substrate 如何为构建区块链提供模块化框架。

![高级概述](./assets/sub-arch-1.avif)

在高层次上，Substrate 节点提供了一个包含两个主要元素的分层环境：
- 处理网络活动的外部节点，例如对等点发现、管理事务请求、与对等点达成共识以及响应 RPC 调用。
- 包含执行区块链状态转换功能的所有业务逻辑的运行时。

## 外节点
外部节点负责在运行时之外发生的活动。例如，外层节点负责处理对等点的发现、管理交易池、与其他节点通信以达成共识，以及应答来自外界的 RPC 调用或浏览器请求。

外部节点处理的一些最重要的活动涉及以下组件：
- [存储](https://docs.substrate.io/fundamentals/state-transitions-and-storage/)：外部节点使用简单高效的键值存储层来保持 Substrate 区块链的演化状态。
- [点对点网络](https://docs.substrate.io/fundamentals/node-and-network-types/)：外部节点使用[libp2p](https://libp2p.io/)网络堆栈的 Rust 实现与其他网络参与者进行通信。
- [共识](https://docs.substrate.io/fundamentals/consensus/)：外部节点与其他网络参与者通信，以确保他们就区块链的状态达成一致。
- [远程过程调用 (RPC) API](https://docs.substrate.io/build/custom-rpc/)：外部节点接受入站 HTTP 和 WebSocket 请求，以允许区块链用户与网络进行交互。
- 遥测：外部节点通过嵌入式[Prometheus](https://prometheus.io/)服务器收集并提供对节点指标的访问。
- [执行环境](https://docs.substrate.io/build/build-process/)：外部节点负责选择执行环境——WebAssembly 或原生 Rust——供运行时使用，然后将调用调度到选定的运行时。
执行这些任务通常需要外部节点向运行时查询信息或向运行时提供信息。这种通信是通过调用专门的[运行时 API](https://docs.substrate.io/reference/runtime-apis/)来处理的。

运行
运行时确定交易是有效还是无效，并负责处理区块链状态转换功能的变化。

因为运行时执行它接收到的函数，所以它控制如何将事务包含在块中，以及如何将块返回到外部节点以进行 gossiping 或导入到其他节点。本质上，运行时负责处理链上发生的所有事情。它也是构建 Substrate 区块链节点的核心组件。

Substrate 运行时旨在编译为[WebAssembly](https://docs.substrate.io/reference/glossary/#webassembly-wasm) (Wasm)字节码。该设计决策可实现：
- 支持无叉升级
- 多平台兼容性
- 运行时有效性检查
- 中继链共识机制的验证证明

与外部节点如何向运行时提供信息类似，运行时使用专门的[主机函数](https://paritytech.github.io/substrate/master/sp_io/index.html)与外部节点或外部世界进行通信。

## 轻客户端节点

轻客户端或轻节点是 Substrate 节点的简化版本，仅提供运行时和当前状态。轻节点使用户能够使用浏览器、浏览器扩展、移动设备或台式计算机直接连接到 Substrate 运行时。使用轻客户端节点，您可以使用用 Rust、JavaScript 或其他语言编写的 RPC 端点连接到 WebAssembly 执行环境，以读取块头、提交交易和查看交易结果。

## 下一步去哪里

现在您已经了解了 Substrate 架构和核心节点组件，请探索以下主题以了解更多信息。

- [网络和区块链](https://docs.substrate.io/fundamentals/node-and-network-types/)
- [交易和区块基础](https://docs.substrate.io/fundamentals/transaction-types/)
- [交易生命周期](https://docs.substrate.io/fundamentals/transaction-lifecycle/)
- [状态转换和存储](https://docs.substrate.io/fundamentals/state-transitions-and-storage/)
- [运行时 API](https://docs.substrate.io/reference/runtime-apis/)
