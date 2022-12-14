# 区块链基础知识

区块链是一个去中心化的分类账，它在一系列区块中记录信息。块中包含的信息是一组有序的指令，可能会导致状态发生变化。

在区块链网络中，单个计算机（称为节点）相互通信以形成分散的点对点（P2P）网络。没有中央机构来控制网络，通常，参与区块生产的每个节点都会存储组成规范链的区块的副本。

在大多数情况下，用户通过提交可能导致状态更改的请求与区块链进行交互，例如，更改文件所有者或将资金从一个帐户转移到另一个帐户的请求。这些交易请求被传播到网络上的其他节点，并由区块作者组装成一个区块。为了确保链上数据的安全和链上的持续进展，节点使用某种形式的共识来就每个区块中的数据状态和执行的交易顺序达成一致。

## 什么是区块链节点？

概括地说，所有区块链节点都需要以下核心组件：
- 作为交易结果记录的状态变化的数据存储。
- 点对点网络，用于节点之间的分散式通信。
- 防止恶意活动并确保链的持续进展的共识方法。
- 用于~~订购和处理~~传入交易的逻辑。
- 用于生成块的哈希摘要以及用于签署和验证与交易相关的签名的密码学。

由于构建区块链所需的核心组件所涉及的复杂性，大多数区块链项目从现有区块链代码库的完整副本开始，以便开发人员可以修改现有代码以添加新功能，而不是从头开始编写所有内容。例如，比特币存储库被分叉以创建 `Litecoin`、`ZCash`、`Namecoin` 和 `Bitcoin Cash`。同样，以太坊存储库被分叉以创建 `Quorum`、`POA 网络`、`KodakCoin` 和 `Musicoin`。

然而，大多数区块链平台的设计不允许修改或定制。因此，通过分叉构建新的区块链具有严重的局限性，包括原始区块链代码固有的可扩展性等限制。在探索 Substrate 如何缓解与其他区块链项目相关的许多限制之前，了解大多数区块链共享的一些常见属性非常重要。通过了解大多数区块链的运作方式，您将更好地了解 Substrate 如何为构建最适合您需求的区块链提供替代方案和功能。

## 状态转换和冲突

区块链本质上是一个[状态机](https://en.wikipedia.org/wiki/Finite-state_machine)。在任何时间点，区块链都有一个当前的内部状态。随着~~入站交易~~的执行，它们会导致状态发生变化，因此区块链必须从当前状态转换到新状态。但是，可能有多个有效的转换会导致不同的未来状态，并且区块链必须选择一个可以商定的单一状态转换。要在转换后就状态达成一致，区块链中的所有操作都必须是确定性的。为了使链顺利进行，大多数节点必须就所有状态转换达成一致，包括：
- 链的初始状态，称为创世状态或创世块。
- 由记录在每个块中的已执行事务导致的一系列状态转换。
- 要包含在链中的块的最终状态。
在集中式网络中，中央机构可以在相互排斥的状态转换之间进行选择。例如，配置为主要权限的服务器可能会按照它看到的顺序记录状态转换的更改，或者在发生冲突时使用加权过程在竞争备选方案之间进行选择。在去中心化网络中，节点以不同的顺序查看交易，因此它们必须使用更精细的方法来选择交易并在冲突的状态转换之间进行选择。

区块链用来将交易批处理成块并选择哪个节点可以向链提交块的方法称为区块链的共识模型或共识算法。最常用的共识模型称为工作量证明共识模型。使用工作量证明共识模型，首先完成计算问题的节点有权向链提交块。

为了让区块链具有容错性并提供一致的状态视图，即使某些节点受到恶意行为者或网络中断的影响，一些共识模型要求至少三分之二的节点始终就状态达成一致。这三分之二的多数确保了网络是容错的，并且可以承受一些网络参与者的不良行为，无论该行为是故意的还是偶然的。

## 区块链经济学

所有区块链都需要资源——处理器、内存、存储和网络带宽——来执行操作。参与网络的计算机——产生区块的节点——将这些资源提供给区块链用户。这些节点创建了一个分布式、去中心化的网络，可以满足参与者社区的需求。

为了支持社区并使区块链可持续发展，大多数区块链都要求用户以交易费用的形式为他们使用的网络资源付费。支付交易费用需要用户身份与持有某种类型资产的账户相关联。区块链通常使用代币来代表账户中资产的价值，网络参与者通过交易所在链外购买代币。然后网络参与者可以存入代币以使他们能够支付交易费用。

## 区块链治理

一些区块链允许网络参与者提交和投票影响网络运营或区块链社区的提案。通过提交和对提案进行投票——全民投票——区块链社区可以确定区块链在本质上是民主的过程中如何发展。然而，链上治理相对较少，为了参与，区块链可能需要用户在账户中维持大量代币权益或被选为其他用户的代表。

## 在区块链上运行的应用程序

在区块链上运行的应用程序（通常称为去中心化应用程序或 dApp）通常是使用前端框架编写的 Web 应用程序，但具有用于更改区块链状态的后端智能合约。

智能合约是在区块链上运行并在特定条件下代表用户执行交易的程序。开发人员可以编写智能合约以确保以编程方式执行的交易的结果被记录下来并且不会被篡改。然而，仅通过智能合约，开发人员无法访问某些底层区块链功能——例如共识、存储或交易层——而是遵守链的固定规则和限制。智能合约开发人员通常会接受这些限制作为一种折衷方案，以缩短开发时间并减少需要做出的核心设计决策。

## 下一步去哪里

所有区块链都有一些共同的特征。Substrate - 虽然不是区块链本身 - 是区块链构建者的工具包，具有用于创建自定义区块链的模块化组件框架。使用 Substrate，您可以使用常见的区块链组件（如存储、共识和密码学）并将它们组合起来以按原样使用它们提供的功能或修改它们以适应您的项目目的。

您可以浏览以下资源以了解更多信息。

### 告诉我
- [基础知识](https://docs.substrate.io/fundamentals/)
- [为什么是substrate？](https://docs.substrate.io/fundamentals/why-substrate/)
- [建筑学](https://docs.substrate.io/fundamentals/architecture/)
- [网络和区块链](https://docs.substrate.io/fundamentals/node-and-network-types/)

### 引导我

- [构建本地区块链](https://docs.substrate.io/tutorials/get-started/build-local-blockchain/)
- [模拟网络](https://docs.substrate.io/tutorials/get-started/simulate-network/)
- [添加可信节点](https://docs.substrate.io/tutorials/get-started/trusted-network/)

如果您更喜欢直接探索代码，您可以在[Substrate Playground](https://substrate.io/developers/playground/)中开始构建或查阅 API 参考以获取有关您使用的 Rust crates 的详细信息。