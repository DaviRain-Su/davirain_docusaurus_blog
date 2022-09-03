# 构建本地区块链

正如您在[区块链基础知识](https://docs.substrate.io/fundamentals/blockchain-basics/)中所了解的，区块链由分散的计算机（称为节点）组成，以形成网络。

`Substrate`提供了一个灵活、开放和可扩展的开发环境，允许您设计和构建完全定制的区块链节点，以满足您的应用程序或业务模型需求。

## 开始使用

成为区块链开发人员的第一步是学习如何编译和启动单个本地区块链节点。在本教程中，您将使用节点模板构建并启动单节点区块链。

`Substrate`节点模板提供了一个可以在开发环境中本地运行的工作单节点区块链。节点模板包括几个预定义的组件（例如用户帐户和帐户余额），以便您可以尝试执行常见任务。无需对模板进行任何更改，您就可以运行生成块并允许交易的功能节点。

启动本地区块链节点后，本教程将说明如何使用 `Substrate` 前端模板查看有关区块链活动的信息并提交交易。

### 本教程适用于谁？

本教程提供了对 `Substrate` 的基本介绍，并准备了一个最小的工作开发环境，您可以使用它在其他教程中进一步探索。它适用于任何有兴趣了解 `Substrate` 和区块链开发的人。本教程假设您之前没有 `Substrate` 的经验或知识。您不需要任何编程或区块链经验即可完成本教程。这只是第一步，但希望它能激励您继续您的旅程。

### 完成本教程需要多少时间？

本教程需要编译 `Rust` 代码，大约需要一到两个小时才能完成。

## 在你开始之前

对于本教程，您将下载并使用工作代码。在开始之前，请验证以下内容：

- 您有良好的互联网连接并可以访问本地计算机上的 shell 终端。
- 您通常熟悉软件开发并使用命令行界面。
- 您通常熟悉区块链和智能合约平台。
- 您已经按照安装中的描述安装了 `Rust` 并设置了您的开发环境。

## 教程目标

通过完成本教程，您将实现以下目标：

- 编译节点模板并启动本地基于 `Substrate` 的区块链。
- 安装前端模板与本地区块链节点交互。
- 使用前端模板提交交易并查看结果。

## 编译一个 Substrate 节点

[Substrate 节点模板](https://github.com/substrate-developer-hub/substrate-node-template)提供了一个工作开发环境，以便您可以立即开始在 Substrate 上构建。

如果您已经在本地计算机上编译好节点模板，您可以跳过本节继续[启动本地节点](https://docs.substrate.io/tutorials/get-started/build-local-blockchain/#start-the-local-node)。

编译 `Substrate` 节点模板：

1. 在您的计算机上打开终端shell
2. 通过运行以下命令克隆节点模板存储库：

```shell
git clone https://github.com/substrate-developer-hub/substrate-node-template
```

3. 切换到节点模板目录的根目录并`polkadot-v0.9.26`通过运行以下命令检出分支：

```shell
cd substrate-node-template && git checkout polkadot-v0.9.26
```

4. 通过运行以下命令编译节点模板：

```shell
cargo build --release
```

您应该始终使用该`--release`标志来构建优化的工件。

## 启动本地节点

节点编译后，您就可以开始使用前端模板探索它的功能了。

启动本地 `Substrate` 节点：

1. 打开终端外壳。
2. 切换到编译 Substrate 节点模板的根目录。
3. 通过运行以下命令以开发模式启动节点：

```shell
./target/release/node-template --dev
```

`node-template`命令行选项指定您希望正在运行的节点如何操作。在这种情况下，该`--dev`选项指定节点使用预定义的`development`链规范在开发者模式下运行。默认情况下，当您按 `Control-c` 停止节点时，此选项还会删除所有活动数据，例如密钥、区块链数据库和网络信息。使用该`--dev`选项可确保您在任何时候停止和重新启动节点时都处于干净的工作状态。

4. 通过查看终端中显示的输出来验证您的节点是否已启动并成功运行。

终端应显示类似于此的输出：

```shell
2022-08-16 13:43:58 Substrate Node    
2022-08-16 13:43:58 ✌️  version 4.0.0-dev-de262935ede    
2022-08-16 13:43:58 ❤️  by Substrate DevHub <https://github.com/substrate-developer-hub>, 2017-2022    
2022-08-16 13:43:58 📋 Chain specification: Development
2022-08-16 13:43:58 🏷  Node name: limping-oatmeal-7460    
2022-08-16 13:43:58 👤 Role: AUTHORITY    
2022-08-16 13:43:58 💾 Database: RocksDb at /var/folders/2_/g86ns85j5l7fdnl621ptzn500000gn/T/substrate95LPvM/chains/dev/db/full    
2022-08-16 13:43:58 ⛓  Native runtime: node-template-100 (node-template-1.tx1.au1)
2022-08-16 13:43:58 🔨 Initializing Genesis block/state (state: 0xf6f5…423f, header-hash: 0xc665…cf6a)
2022-08-16 13:43:58 👴 Loading GRANDPA authority set from genesis on what appears to be first startup.
2022-08-16 13:43:59 Using default protocol ID "sup" because none is configured in the chain specs
2022-08-16 13:43:59 🏷  Local node identity is: 12D3KooWCu9uPCYZVsayaCKLdZLF8CmqiHkX2wHsAwSYVc2CxmiE
...
...
...
...
2022-08-16 13:54:26 💤 Idle (0 peers), best: #3 (0xcdac…26e5), finalized #1 (0x107c…9bae), ⬇ 0 ⬆ 0
```

如果之后的数字在`finalized`增加，则您的区块链正在生成新块并就它们所描述的状态达成共识。

我们将在后面的教程中探讨日志输出的详细信息。目前，只需要知道您的节点正在运行并生成块即可。

5. 保持显示节点输出的终端打开以继续。

## 安装前端模板

前端模板使用[ReactJS](https://reactjs.org/)呈现 Web 浏览器界面，使您能够与基于 `Substrate` 的区块链节点进行交互。您可以将此前端模板用作将来为您自己的项目创建用户界面的起点。

前端模板需要[Yarn](https://yarnpkg.com/)和[Node.js](https://nodejs.org/en/)。如果您没有这些工具，请先安装它们。

安装前端模板：

1. node通过运行以下命令检查是否安装在本地计算机上：

```shell
node --version
```

如果该命令未返回版本号，请按照您在[Node.js](https://nodejs.org/en/)网站node上使用的操作系统的说明下载并安装。版本至少应为v14才能运行前端模板。node

2. `yarn`通过运行以下命令检查是否安装在本地计算机上：

```shell
yarn --version
```

`yarn`版本至少应为`v3`才能运行前端模板。

如果该命令未返回版本号，请`yarn`通过运行以下命令下载并安装：

```shell
npm install -g yarn
```

通过运行以下命令克隆前端模板存储库：

```shell
git clone https://github.com/substrate-developer-hub/substrate-front-end-template
```

3. 通过运行以下命令切换到前端模板目录的根目录：

```shell
cd substrate-front-end-template
```

4. 通过运行以下命令安装前端模板的依赖项：

```shell
yarn install
```

## 启动前端模板

`Substrate` 前端模板由用户界面组件组成，使您能够与 `Substrate` 节点交互并执行一些常见任务。

要使用前端模板：

1. 在您的计算机上打开一个新的终端 shell，切换到安装前端模板的根目录。
2. 通过运行以下命令启动前端模板：

```shell
yarn start
```

在浏览器中打开`http://localhost:8000`以查看前端模板。

顶部有一个账户选择列表，用于在您想要执行链上操作时选择要使用的账户。模板的顶部还显示有关您连接到的链的信息。

![前端模板顶部](./assets/blockchain-top.avif)

您可能还注意到，前端模板显示了一个带有一些预定义账户的余额表，并且其中一些账户预配置了资金。您可以使用此示例数据来尝试转账等操作。

![预定义的账户和余额](./assets/balances.avif)

## 从账户转账

既然您在本地计算机上运行了一个区块链节点，并且您有一个可用于执行链上操作的前端模板，那么您就可以探索与区块链交互的不同方式了。

默认情况下，前端模板包含多个组件，可让您尝试不同的常见任务。对于本教程，您可以执行简单的转账操作，将资金从一个账户转移到另一个账户。

将资金转入账户：

1. 在余额表中，请注意预定义的帐户（例如 dave）没有与其关联的资金。

![找到一个零资金的账户](./assets/dave-account.avif)

在 `Balances` 表下，前端模板还显示一个`Transfer`组件。您使用此组件将资金从一个帐户转移到另一个帐户。

2. 复制并粘贴dave帐户的地址，以指定您将资金转移到的地址。

3. 指定至少1000000000000作为转账金额，然后点击**提交**。

![将资金转入账户](./assets/transfer-filled.avif)

4. 请注意，余额表中的值随转移而更新。

![显示更新的余额](./assets/balance-result.avif)

5. 检查**事件**组件以查看与您刚刚完成的传输相关的事件。

`Substrate` 区块链将异步操作的结果报告为事件，因此您可以使用事件组件查看作为传输的一部分执行的每个操作的详细信息。例如：


![记录为异步操作结果的事件](./assets/event-panel.avif)

6. 当交易完成并包含在区块中时，您会看到类似于以下内容的确认消息：

😉 完成。块哈希：`0xda7e9e935abf5a3a2fdb0a27d67cd7a69e628165b5827255af2635ba226411a4`

## 停止本地节点

传输成功后，您可以继续探索前端模板组件或停止本地 `Substrate` 节点您所做的状态更改。因为您`--dev`在启动节点时指定了该选项，所以停止本地节点会停止区块链并清除所有持久块数据，以便您下次启动节点时可以以干净的状态开始。

停止本地 `Substrate` 节点：

1. 返回到显示节点输出的终端 shell。
2. 按 `Control-c` 终止正在运行的进程。
3. 验证您的终端返回到`substrate-node-template`目录中的终端提示符。

## 下一步

恭喜！

在本教程中，您学习了：

- 如何使用节点模板启动一个工作的基于 `Substrate` 的区块链节点。
- 如何使用前端用户界面查看区块链节点并与之交互。
- 如何进行从一个帐户到另一个帐户的简单转账。
 
前端模板包括几个附加组件，供您在连接到本地开发节点时进行试验。您可以自行探索这些组件或在以下主题中了解更多信息：

如果您在本教程中遇到任何问题，请提交问题、提问或提供反馈。

- [提交问题](https://github.com/substrate-developer-hub/substrate-docs/issues/new/choose)
- [基板堆栈交换](https://substrate.stackexchange.com/)
- [建筑学](https://docs.substrate.io/fundamentals/architecture/)
- [网络和区块链](https://docs.substrate.io/fundamentals/node-and-network-types/)
- [模拟网络](https://docs.substrate.io/tutorials/get-started/simulate-network/)