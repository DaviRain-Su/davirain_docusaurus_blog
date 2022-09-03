# 准备你的第一份合同
正如您在区块链基础知识中了解到的那样，去中心化应用程序最常被编写为智能合约。尽管 Substrate 主要是用于构建自定义区块链的框架和工具包，但它也可以为智能合约提供平台。本教程演示了如何构建一个基本的智能合约以在基于 Substrate 的链上运行。在本教程中，您将探索使用墨水！作为编写基于 Rust 的智能合约的编程语言。

在你开始之前
在开始之前，请验证以下内容：

您有良好的互联网连接并可以访问本地计算机上的 shell 终端。
您通常熟悉软件开发和使用命令行界面。
您通常熟悉区块链和智能合约平台。
您已经按照安装中的描述安装了 Rust 并设置了您的开发环境。
教程目标
通过完成本教程，您将实现以下目标：

了解如何创建智能合约项目。
使用墨水构建和测试智能合约！智能合约语言。
在本地 Substrate 节点上部署智能合约。
通过浏览器与智能合约交互。
更新你的 Rust 环境
对于本教程，您需要将一些 Rust 源代码添加到您的 Substrate 开发环境中。

要更新您的开发环境：

在您的计算机上打开终端外壳。
切换到编译 Substrate 节点模板的根目录。
通过运行以下命令更新您的 Rust 环境：
```shell
rustup component add rust-src --toolchain nightly
```
通过运行以下命令验证您是否安装了 WebAssembly 目标：
```shell
rustup target add wasm32-unknown-unknown --toolchain nightly
```
如果目标已安装并且是最新的，则该命令会显示类似于以下内容的输出：
```shell
info: component 'rust-std' for target 'wasm32-unknown-unknown' is up to date
```
安装 Substrate 合约节点
为了简化本教程，您可以下载适用于 Linux 或 macOS 的预编译 Substrate 节点。默认情况下，预编译的二进制文件包括智能合约的 FRAME 托盘。或者，您可以通过在本地计算机上contracts-node运行来手动构建预配置。cargo install contracts-node

在 macOS 或 Linux 上安装合约节点：

打开发布页面。
为您的本地计算机下载适当的压缩存档。
打开下载的文件并将内容提取到工作目录。
如果无法下载预编译的节点，可以使用类似如下的命令在本地编译：
```shell
cargo install contracts-node --git https://github.com/paritytech/substrate-contracts-node.git --tag <latest-tag> --force --locked
```
您可以在标签页面上找到要使用的最新标签。

安装额外的软件包
编译contracts-node包后，需要安装两个额外的包：

操作系统的 WebAssembly binaryen包，用于优化合约的 WebAssembly 字节码。
cargo-contract您将用于设置智能合约项目的命令行界面。
安装 WebAssembly 优化器
要安装binaryen包：

在您的计算机上打开终端外壳。
使用适合您操作系统的包管理器来安装包。

例如，在 Ubuntu 或 Debian 上，运行以下命令：
```shell
sudo apt install binaryen
```
在 macOS 上，运行以下命令：
```shell
brew install binaryen
```
对于其他操作系统，您可以binaryen直接从WebAssebly 版本下载该版本。

安装 cargo-contract 包
安装 WebAssemblybinaryen包后，您可以安装该cargo-contract包。该cargo-contract软件包提供了一个命令行界面，用于使用 ink 处理智能合约！语。

在您的计算机上打开终端外壳。
安装dylint-link，需要掉墨！合同，警告您以可能导致安全问题的方式使用 API 之类的事情。
```shell
cargo install dylint-link
```
cargo-contract通过运行以下命令进行安装：
```shell
cargo install cargo-contract --force
```
通过运行以下命令来验证安装并探索可用的命令：
```shell
cargo contract --help
```
创建一个新的智能合约项目
您现在已准备好开始开发新的智能合约项目。

为智能合约项目生成文件：

在您的计算机上打开终端外壳。
flipper通过运行以下命令创建一个名为的新项目文件夹：
```shell
cargo contract new flipper
```
通过运行以下命令切换到新的项目文件夹：
```shell
cd flipper/
```
通过运行以下命令列出目录的所有内容：
```shell
ls -al
```
您应该看到该目录包含以下文件：
```shell
-rwxr-xr-x   1 dev-doc  staff   285 Mar  4 14:49 .gitignore
-rwxr-xr-x   1 dev-doc  staff  1023 Mar  4 14:49 Cargo.toml
-rwxr-xr-x   1 dev-doc  staff  2262 Mar  4 14:49 lib.rs
```
与其他 Rust 项目一样，该Cargo.toml文件用于提供包依赖项和配置信息。该lib.rs文件用于智能合约业务逻辑。

探索默认项目文件
默认情况下，创建一个新的智能合约项目会为一个非常简单的合约生成一些模板源代码，该合约具有一个函数flip()——将布尔变量从 true 更改为 false，第二个函数get——获取布尔值的当前值。该lib.rs文件还包含两个函数，用于测试合约是否按预期工作。

随着您学习本教程的进展，您将修改入门代码的不同部分。在本教程结束时，您将拥有一个看起来像Flipper 示例的更高级的智能合约。

要浏览默认项目文件：

如果需要，在您的计算机上打开终端外壳。
如果需要，更改为flipper智能合约的项目文件夹：
在文本编辑器中打开Cargo.toml文件并查看合同的依赖关系。
如有必要，在该[dependencies]部分中修改scale和设置。scale-info
```toml
scale = { package = "parity-scale-codec", version = "3", default-features = false, features = ["derive"] }
scale-info = { version = "2", default-features = false, features = ["derive"], optional = true }
```
保存对文件的任何更改Cargo.toml，然后关闭文件。
在文本编辑器中打开lib.rs文件并查看为合约定义的函数。
测试默认合约
在lib.rs源代码文件的底部，有简单的测试用例来验证合约的功能。您可以使用链下测试环境测试此代码是否按预期运行。

测试合约：

如果需要，在您的计算机上打开终端外壳。
如果需要，请验证您是否位于flipper项目文件夹中。
通过运行以下命令，使用test子命令和nightly工具链执行合约的默认测试：flipper
```shell
cargo +nightly test
```
该命令应显示类似于以下内容的输出，以指示测试成功完成：
```shell
running 2 tests
test flipper::tests::it_works ... ok
test flipper::tests::default_works ... ok

test result: ok. 2 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out
```
建立合同
测试默认合约后，您就可以将此项目编译为 WebAssembly。

为这个智能合约构建 WebAssembly：

如果需要，在您的计算机上打开终端外壳。
验证您是否位于flipper项目文件夹中。
flipper通过运行以下命令编译智能合约：
```shell
cargo +nightly contract build
```
此命令为项目构建 WebAssembly 二进制flipper文件、包含合约应用程序二进制接口 (ABI) 的元数据文件以及.contract用于部署合约的文件。例如，您应该看到类似于以下的输出：
```shell
Original wasm size: 47.9K, Optimized: 22.8K

The contract was built in DEBUG mode.

Your contract artifacts are ready. You can find them in:
/Users/dev-doc/flipper/target/ink

- flipper.contract (code + metadata)
- flipper.wasm (the contract's code)
- metadata.json (the contract's metadata)
The `.contract` file can be used for deploying your contract to your chain.
```
metadata.json目录中的文件描述target/ink了您可以用来与此合约交互的所有接口。该文件包含几个重要部分：

该spec部分包括有关可以调用的函数（如构造函数和消息）、发出的事件以及可以显示的任何文档的信息。本节还包括一个selector包含函数名称的 4 字节散列的字段，用于将合约调用路由到正确的函数。
该storage部分定义了合约管理的所有存储项目以及如何访问它们。
该types部分提供了整个 JSON 其余部分中使用的自定义数据类型。
启动 Substrate 智能合约节点
如果您已成功安装substrate-contracts-node，您可以为您的智能合约启动一个本地区块链节点。

要启动预配置contracts-node：

如果需要，在您的计算机上打开终端外壳。
通过运行以下命令以本地开发模式启动contracts节点：
```shell
substrate-contracts-node --dev
```
您应该会在终端中看到类似于以下内容的输出：
```shell
2022-03-07 14:46:25 Substrate Contracts Node
2022-03-07 14:46:25 ✌️  version 0.8.0-382b446-x86_64-macos
2022-03-07 14:46:25 ❤️  by Parity Technologies <admin@parity.io>, 2021-2022
2022-03-07 14:46:25 📋 Chain specification: Development
2022-03-07 14:46:25 🏷  Node name: possible-plants-8517
2022-03-07 14:46:25 👤 Role: AUTHORITY
2022-03-07 14:46:25 💾 Database: RocksDb at /var/folders/2_/g86ns85j5l7fdnl621ptzn500000gn/T/substrateEdrJW9/chains/dev/db/full
2022-03-07 14:46:25 ⛓  Native runtime: substrate-contracts-node-100 (substrate-contracts-node-1.tx1.au1)
2022-03-07 14:46:25 🔨 Initializing Genesis block/state (state: 0xe9f1…4b89, header-hash: 0xa1b6…0194)
2022-03-07 14:46:25 👴 Loading GRANDPA authority set from genesis on what appears to be first startup.
2022-03-07 14:46:26 🏷  Local node identity is: 12D3KooWQ3P8BH7Z1C1ZoNSXhdGPCiPR7irRSeQCQMFg5k3W9uVd
2022-03-07 14:46:26 📦 Highest known block at #0
```

几秒钟后，您应该会看到块正在完成。

要与区块链交互，您需要连接到该节点。您可以通过浏览器打开Contracts UI连接到节点。

在 Web 浏览器中导航到Contracts UI ，然后单击Yes allow this application access。
选择本地节点。

<图像丢失>
部署合约
至此，您已完成以下步骤：

安装了本地开发的软件包。
flipper为智能合约生成 WebAssembly 二进制文件。
以开发模式启动本地节点。
通过 Contracts UI 前端连接到本地节点。
下一步是flipper在您的 Substrate 链上部署合约。

然而，在 Substrate 上部署智能合约与在传统智能合约平台上部署有点不同。对于大多数智能合约平台，您必须在每次进行更改时部署全新的智能合约源代码块。例如，标准 ERC20 代币已被部署到以太坊数千次。即使更改很小或仅影响某些初始配置设置，每次更改都需要完全重新部署代码。每个智能合约实例消耗相当于完整合约源代码的区块链资源，即使实际上没有更改任何代码。

在 Substrate 中，合约部署过程分为两个步骤：

将合约代码上传到区块链。
创建合约实例。
使用这种模式，您可以将像 ERC20 标准这样的智能合约的代码存储在区块链上一次，然后将其实例化任意次数。您不需要重复重新加载相同的源代码，因此您的智能合约不会消耗区块链上不必要的资源。

上传合约代码
在本教程中，您将使用 Contracts UI 前端flipper在 Substrate 链上部署合约。

上传智能合约源代码：

在 Web 浏览器中打开合同 UI 。
确认您已连接到本地节点。
单击添加新合同。
点击上传新合同代码。
选择用于创建合约实例的账户。

您可以选择任何现有帐户，包括预定义帐户，例如alice.

输入智能合约的描述性名称，例如 Flipper Contract。
浏览并选择或拖放flipper.contract包含捆绑的 Wasm blob 和元数据的文件到上传部分。


上传合同
单击下一步继续。
在区块链上创建实例
智能合约作为 Substrate 区块链上账户系统的扩展存在。当您创建此智能合约的实例时，Substrate 会创建一个新AccountId的来存储智能合约管理的任何余额并允许您与合约进行交互。

上传智能合约并单击Next后，Contracts UI 会显示有关智能合约内容的信息。

创建实例：

查看并接受智能合约初始版本的默认部署构造函数选项。
查看并接受默认的Max Gas Allowed。200000


创建智能合约的实例
单击下一步。

事务现在已排队。如果您需要进行更改，您可以单击返回来修改输入。


完成实例化
单击上传并实例化。

根据您使用的帐户，系统可能会提示您输入帐户密码。如果您使用预定义帐户，则无需提供密码。


成功部署智能合约实例
调用智能合约
现在您的合约已经部署在区块链上，您可以与它进行交互。默认的 Flipper 智能合约有两个功能-flip()并且get()- 您可以使用 Contracts UI 来试用它们。

获取（）函数
您将合约的初始值设置为实例化合约时flipper的值。您可以使用该函数来验证当前值为。valuefalseget()false

测试get()功能：

从帐户列表中选择任何帐户。

本合同没有限制谁可以发送get()请求。

从Message to Send列表中选择get(): bool 。
点击阅读。
验证false调用结果中是否返回了该值。

调用 get() 函数返回 false
翻转（）函数
该flip()函数将值从 更改false为true。

测试flip()功能：

从帐户列表中选择任何预定义的帐户。

该flip()函数是一个改变链状态的交易，需要一个有资金的账户来执行调用。因此，您应该选择具有预定义帐户余额的帐户，例如alice帐户。

从要发送的消息列表中选择翻转（） 。
单击呼叫。
在调用结果中验证事务是否成功。


交易成功
从Message to Send列表中选择get(): bool 。
点击阅读。
验证新值是否true在调用结果中。


get() 函数显示当前值为真
下一步
恭喜！

在本教程中，您学习了：

如何使用墨水创建新的智能合约项目！智能合约语言。
如何为简单的默认智能合约测试和构建 WebAssembly 二进制文件。
如何使用合约节点启动一个工作的基于 Substrate 的区块链节点。
如何通过连接到本地节点并上传和实例化合约来部署智能合约。
如何使用 Contracts UI 浏览器客户端与智能合约交互。
其他智能合约教程以您在本教程中所学的内容为基础，引导您更深入地了解合约开发的不同阶段。

您可以在以下主题中了解有关智能合约开发的更多信息：

开发智能合约
构建 ERC20 代币合约
智能合约故障排除