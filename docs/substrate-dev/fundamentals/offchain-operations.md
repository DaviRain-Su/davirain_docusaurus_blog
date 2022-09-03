# 链下运营

在许多用例中，您可能希望在更新链上状态之前在不使用链上资源的情况下从链下源查询数据或处理数据。合并链下数据的传统方式涉及连接到预言机以提供来自某些传统来源的数据。尽管使用预言机是处理链下数据源的一种方法，但预言机可以提供的安全性、可扩展性和基础设施效率存在限制。

为了使链下数据集成更加安全和高效，Substrate 通过以下功能支持链下操作：

链下工作者是一个组件子系统，可以执行长时间运行且可能不确定的任务，例如：

网站服务请求
数据的加密、解密和签名
随机数生成
CPU密集型计算
链上数据的枚举或聚合 链下工作人员使您能够将可能需要更多时间才能执行的任务移出块处理管道。任何可能花费比允许的最大块执行时间更长的任务都是链下处理的合理候选者。
链下存储是 Substrate 节点本地的存储，可以由链下工作人员和链上逻辑访问：

链下工作人员对链下存储具有读写访问权限。
链上逻辑通过链下索引具有写访问权限，但没有读访问权限。链下存储允许不同的工作线程相互通信，并存储不需要在整个网络上达成共识的特定于用户或特定于节点的数据。
链下索引是一项可选服务，它允许运行时独立于链下工作人员直接写入链下存储。链下索引为链上逻辑提供临时存储，补充链上状态。
链下工作者
Offchain workers run in their own Wasm execution environment outside of the Substrate runtime. This separation of concerns makes sure that block production is not impacted by long-running offchain tasks. However, because offchain workers are declared in the same code as the runtime, they can easily access on-chain state for their computations.


Offchain workers
Off-chain workers have access to extended APIs for communicating with the external world:

Ability to submit transactions—either signed or unsigned—to the chain to publish computation results.
A fully-featured HTTP client allowing the worker to access and fetch data from external services.
Access to the local keystore to sign and verify statements or transactions.
An additional, local key-value database shared between all offchain workers.
用于随机数生成的安全本地熵源。
访问节点的精确本地时间。
睡眠和恢复工作的能力。
请注意，来自链下工作人员的结果不受常规交易验证的约束。因此，您应该确保链下操作包括一种验证方法，以确定哪些信息进入链中。例如，您可以通过实施投票、平均或检查发件人签名的机制来验证链下交易。

在每个区块导入期间都会产生链下工作人员。但是，它们不会在初始区块链同步期间执行。

链下存储
链下存储始终位于 Substrate 节点的本地，不与任何其他区块链节点在链上共享，也不需达成共识。您可以使用具有读写访问权限的链下工作线程或使用链下索引通过链上逻辑访问存储在链下存储中的数据。

因为在每个区块导入期间都会产生一个链下工作线程，所以在任何给定时间都可以有多个链下工作线程运行。与任何多线程编程环境一样，当链下工作线程访问链下存储以确保数据一致性时，有一些实用程序可以互斥锁链下存储。

链下存储充当了链下工作线程相互通信以及链下和链上逻辑之间通信的桥梁。它也可以使用远程过程调用 (RPC) 读取，因此它适合存储无限增长的数据的用例，而不会过度消耗链上存储。

链下索引
在区块链的上下文中，存储通常与链上状态有关。然而，链上状态是昂贵的，因为它必须被同意并填充到网络中的多个节点。因此，您不应该使用链上存储来存储历史数据或用户生成的数据（这些数据会随着时间的推移无限增长）。

为了满足访问历史数据或用户生成数据的需求，Substrate 使用链下索引提供对链下存储的访问。链下索引允许运行时直接写入链下存储，而无需使用链下工作线程。--enable-offchain-indexing您可以通过使用命令行选项启动 Substrate 节点来启用此功能来持久化数据。

与链下工作者不同，每次处理一个块时，链下索引都会填充链下存储。通过在每个块中填充数据，链下索引确保数据始终保持一致，并且对于启用索引运行的每个节点来说都是完全相同的。

下一步去哪里
既然您已经熟悉了链下工作者、链下存储和链下索引如何使您能够处理未存储在链上的数据，您可能想要探索以下链下工作者的示例以及如何在运行时开发中使用它们：

示例：链下工作者
示例：提交交易
示例：使用 HTTP 请求获取数据
示例：链下存储
示例：链下索引