# 快速开始

所有 Substrate 教程和操作指南都要求您在开发环境中构建和运行 Substrate 节点。为了帮助您快速设置工作环境，[Substrate开发者中心](https://github.com/substrate-developer-hub/)维护了几个模板供您使用。

[substrate-node-template](https://github.com/substrate-developer-hub/substrate-node-template/releases/tag/polkadot-v0.9.25)的开发者中心快照包括您开始使用一组核心功能所需的一切。

本快速入门假设您是第一次设置开发环境并希望尝试在本地计算机上运行单个区块链节点。

为简单起见，您将使用 Web 浏览器连接到本地节点并查找预定义示例帐户的余额。

## 在你开始之前

在开始之前，请验证以下内容：
- 您有 Internet 连接并可以访问本地计算机上的交互式 shell 终端。
- 您通常熟悉软件开发和使用命令行界面。
- 你已经安装了 Rust 编译器和工具链。

`rustup show`您可以通过运行命令检查是否安装了 Rust 。如果安装了 Rust，此命令会显示工具链和编译器的版本信息。如果未安装 Rust，则该命令不会返回任何输出。有关安装 Rust 的信息，请参阅[安装](https://docs.substrate.io/install/)。

## 构建节点模板
1. `polkadot-v0.9.25`通过运行以下命令，使用标签克隆节点模板存储库：

```shell
git clone --branch polkadot-v0.9.25 https://github.com/substrate-developer-hub substrate-node-template
```

2. 切换到克隆目录的根目录：
```shell
cd substrate-node-template
```

3. 编译节点模板：
```shell
cargo build --package node-template --release
```

由于涉及的软件包数量，编译节点可能需要几分钟时间。

## 启动节点

1. 通过运行以下命令验证您的节点是否已准备好使用并查看有关可用命令行选项的信息：

```shell
./target/release/node-template --help
```

使用信息显示了您可以用来执行以下操作的命令行选项：
- 启动节点
- 使用帐户
- 修改节点操作

2. Alice通过运行以下命令查看预定义开发帐户的帐户信息：

```shell
./target/release/node-template key inspect //Alice
```

该命令显示以下帐户信息：

```shell
Secret Key URI `//Alice` is account:
Network ID:        substrate 
Secret seed:       0xe5be9a5092b81bca64be81d212e7f2f9eba183bb7a90954f7b76361f6edb5c0a
Public key (hex):  0xd43593c715fdd31c61141abd04a99fd6822c8558854ccde39a5684e7a56da27d
Account ID:        0xd43593c715fdd31c61141abd04a99fd6822c8558854ccde39a5684e7a56da27d
Public key (SS58): 5GrwvaEF5zXb26Fz9rcQpDWS57CtERHpNehXCPcNoHGKutQY
SS58 Address:      5GrwvaEF5zXb26Fz9rcQpDWS57CtERHpNehXCPcNoHGKutQY
```

3. 通过运行以下命令以开发模式启动节点：

```shell
./target/release/node-template --dev
```

在开发模式下，链不需要任何对等计算机来完成区块​​。当节点启动时，终端会显示有关所执行操作的输出。如果您看到正在提议和完成区块的消息，则您有一个正在运行的节点。

```
... Idle (0 peers), best: #3 (0xcc78…5cb1), finalized #1 ...
... Starting consensus session on top of parent ...
... Prepared block for proposing at 4 (0 ms) ...
```

## 连接到节点

1. 使用 [JavaScript 和Polkadot-JS API](https://polkadot.js.org/docs/)创建一个简单的 HTML 文件以与区块链交互。

例如，创建一个`index.html`使用 JavaScript 和 HTML 的文件来：

- 将账户地址作为输入
- 使用 onClick 事件查找帐户余额
- 将帐户的余额显示为输出

此示例`index.html`提供了一个简单示例，说明如何使用 JavaScript、Polkadot-JS API 和 HTML 来获取账户余额。

2. 将示例代码复制并粘贴`index.html`到文本编辑器中的新文件中，然后将文件保存在本地计算机上。
3. `index.html`在网络浏览器中打开文件。
4. 将账户的 `SS58a address` 复制并粘贴到`Alice`输入字段中，然后点击获取余额。

## 停止节点

- 转到显示区块链操作的终端。
- 按停止本地区块链并清除所有状态ctrl-c。

## 下一步去哪里

在本快速入门中，您学习了如何在本地计算机上编译和运行单个区块链节点。要开始学习如何自定义其功能，请浏览以下资源：

- [建筑学](https://docs.substrate.io/fundamentals/architecture/)
- [运行时开发](https://docs.substrate.io/fundamentals/runtime-development/)
- [substrate中的rust](https://docs.substrate.io/fundamentals/rust-basics/)