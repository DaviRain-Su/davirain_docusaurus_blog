# 为什么是substrate？

区块链开发很复杂。它涉及复杂的技术（包括高级密码学和分布式网络通信），您必须正确掌握这些技术，才能为应用程序在其上运行和用户信任提供安全平台。围绕规模、治理、互操作性和可升级性存在一些难题需要解决。复杂性为开发人员设置了很高的进入门槛。考虑到这一点，首先要回答的问题是：你想构建什么？

Substrate 并非完美适用于每个用例、应用程序或项目。然而，如果你想构建一个区块链，Substrate 可能是完美的选择：
- 针对非常具体的用例量身定制
- 能够与其他区块链连接和通信
- 使用预定义的可组合模块化组件进行定制
- 能够随着时间的推移随着升级而发展和变化

Substrate 是一个软件开发工具包 (SDK)，专门设计用于为您提供区块链所需的所有基本组件，因此您可以专注于制作使您的链独特和创新的逻辑。与其他分布式账本平台不同，Substrate 是：

- 灵活的
- 打开
- 可互操作
- 面向未来

### 灵活的

大多数区块链平台都有非常紧密耦合、固执己见的子系统，这些子系统很难解耦。基于另一个区块链的分叉的链中也存在风险，即那些不明显的耦合可能从根本上破坏区块链系统本身。

Substrate 是一个完全模块化的区块链框架，通过选择适合您项目的网络堆栈、共识模型或治理方法或创建您自己的组件，您可以使用明确解耦的组件组成一个链。

使用 Substrate，您可以部署为您的规范设计和构建的区块链，但它也可以随着您不断变化的需求而发展。

### 打开
所有 Substrate 架构和工具都可以在[开源许可](https://github.com/paritytech/substrate#license)下使用。Substrate 框架的核心组件使用开放协议，例如libp2p，jsonRPC同时使您能够决定要自定义多少区块链架构。Substrate 还拥有一个庞大、活跃且乐于助人的构建者社区，为生态系统做出贡献。社区的贡献增强了您可以在其发展过程中将其整合到您自己的区块链中的功能。

### 可互操作
大多数区块链平台提供了与其他区块链网络交互的有限能力。所有基于 Substrate 的区块链都可以通过[交叉共识消息(XCM)](https://wiki.polkadot.network/docs/learn-crosschain) 与其他区块链互操作。Substrate 可用于将链创建为独立的网络（单链），或与[中继链](https://wiki.polkadot.network/docs/learn-architecture#relay-chain)紧密耦合以作为[平行链](https://wiki.polkadot.network/docs/learn-parachains)共享其安全性。

### 面向未来
Substrate 被构建为可升级、可组合和可适应的。状态转换逻辑——Substrate 运行时——是一个自包含的 WebAssembly 对象。您的节点可以被赋予在特定条件下完全改变运行时本身的能力，从而在网络范围内引发运行时升级。因此，“无分叉”升级是可能的，因为在大多数情况下，节点无需任何操作即可使用这个新的运行时进行操作。随着时间的推移，您的网络的运行时协议可以无缝地，也许是彻底地随着用户的需求而发展。

## 下一步去哪里
Substrate 为构建区块链网络和区块链应用程序提供了一个独特而强大的框架。

- 您可以从预先配置的节点开始，并在没有代码的情况下启动一个单独的链。
- 您可以使用 Substrate 构建一个独立于任何其他区块链运行的自定义单链。
- 您可以将您的 Substrate 链耦合到 Polkadot 或 Kusama 等中继链。

根据您的背景和兴趣，探索以下资源以了解更多信息。


### 告诉我
- [区块链基础知识](https://docs.substrate.io/fundamentals/blockchain-basics/)
- [建筑学](https://docs.substrate.io/fundamentals/architecture/)
- [运行时开发](https://docs.substrate.io/fundamentals/runtime-development/)
- [网络和区块链](https://docs.substrate.io/fundamentals/node-and-network-types/)
- [安装](https://docs.substrate.io/install/)

### 引导我
- [构建本地区块链](https://docs.substrate.io/tutorials/get-started/build-local-blockchain/)
- [模拟网络](https://docs.substrate.io/tutorials/get-started/simulate-network/)
- [添加受信任的验证者](https://docs.substrate.io/tutorials/get-started/trusted-network/)

如果您更喜欢直接探索代码，您可以在Substrate Playground中开始构建并查阅API 参考 (Rustdocs)以获取有关 Substrate 中的 Rust crate 的详细信息。