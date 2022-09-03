# 访问 EVM 帐户

本教程说明了如何使用Frontier项目中的 crate 来构建与以太坊兼容的区块链，该区块链可以访问基于以太坊的账户并执行基于 Solidity 的智能合约。Frontier 项目的两个主要目标是使您能够执行以下操作：

使用本地 Substrate 节点运行未经修改的以太坊去中心化应用程序。
从以太坊主网络导入状态。
本教程使用预定义的节点模板来提供工作环境。该模板是使用Frontier 发布指南中的说明生成的。

如果你想为自己生成一个独立的模板，你可以使用node-template-release.sh模板生成脚本。如果您使用frontier存储库或模板生成脚本构建自己的节点，请注意 Frontier 使用自己的 Substrate crates 版本，您可能需要更新Cargo文件中的依赖项以匹配项目中的依赖项。

在你开始之前
在尝试本教程之前，您应该已完成以下 Substrate 教程：

构建本地区块链
将托盘添加到运行时
在自定义托盘中使用宏
从教程中，您应该熟悉如何执行以下任务：

启动一个 Substrate 区块链节点。
在运行时添加、移除和配置托盘。
通过使用 Polkadot-JS 或其他前端连接到节点来提交交易。
在开始本教程之前，您还应该熟悉以下内容：

以太坊核心概念和术语
以太坊虚拟机 (EVM) 基础知识
去中心化应用程序和智能合约
托盘设计原则
创世纪配置
开发链规范中frontier-node-template定义了一个创世区块，该创世区块已预先配置了该账户的 EVMalice账户。当您在开发模式下启动此节点时，EVM 帐户的alice资金是默认数量的 Ether。您将使用此帐户查看 EVM 帐户详细信息并调用以太坊智能合约。启动节点后，您将能够使用Polkadot-JS 应用程序查看alice.

编译 Frontier 节点
Frontier 节点模板提供了一个工作开发环境，以便您可以立即开始在 Substrate 上构建。

编译 Frontier 节点模板：

在您的计算机上打开终端外壳。
通过运行以下命令克隆节点模板存储库：
```shell
git clone https://github.com/substrate-developer-hub/frontier-node-template.git
```
通过运行以下命令切换到节点模板目录的根目录：
```shell
cd frontier-node-template
```
通过运行以下命令编译节点模板：
```shell
cargo build --release
```
连接到节点
节点编译后，您必须启动节点以开始探索预配置的 EVM 帐户。

启动本地 Substrate 节点：

如果需要，在本地计算机上打开终端 shell。
切换到你编译的根目录frontier-node-template。
通过运行以下命令以开发模式启动节点：
```shell
./target/release/frontier-template-node --dev
```
--dev命令行选项指定节点使用预定义的链规范运行，development其中包括预定义的 EVM 帐户alice和其他用于测试的帐户。

通过查看终端中显示的输出来验证您的节点是否已启动并成功运行。

终端应显示类似于此的输出：
```shell
2022-07-08 10:06:42 Frontier Node
2022-07-08 10:06:42 ✌️  version 0.0.0-1b6bff4-x86_64-macos
2022-07-08 10:06:42 ❤️  by Substrate DevHub <https://github.com/substrate-developer-hub>, 2021-2022
2022-07-08 10:06:42 📋 Chain specification: Development
2022-07-08 10:06:42 🏷  Node name: flippant-boat-0444
2022-07-08 10:06:42 👤 Role: AUTHORITY
...
```
使用Polkadot-JS 应用程序连接到本地节点。
单击设置，然后单击开发人员。


开发者设置
定义以下账户信息以创建 EVMAccount类型并使账户能够发送交易和检查区块。

要发送交易，您必须定义Address和LookupSource设置。
要检查块，您必须定义Transaction和Signature设置。
```json
{
  "Address": "MultiAddress",
  "LookupSource": "MultiAddress",
  "Account": {
     "nonce": "U256",
     "balance": "U256"
  },
  "Transaction": {
     "nonce": "U256",
     "action": "String",
     "gas_price": "u64",
     "gas_limit": "u64",
     "value": "U256",
     "input": "Vec<u8>",
     "signature": "Signature"
  },
  "Signature": {
     "v": "u64",
     "r": "H256",
     "s": "H256"
  }
}
```

单击保存。
使用 RPC 查询余额
为 EVM 账户配置设置后，您可以使用 Polkadot-JS 应用程序查看有关alice.

验证您的节点是否仍在运行并且Polkadot-JS 应用程序已连接到该节点。
单击Developer，然后选择RPC 调用。
在提交选项卡上，选择eth作为要调用的端点。
从要调用的函数列表中选择getBalance(address, number)。
为地址指定账户的 EVM 账户标识符alice。

预定义的帐户地址是0xd43593c715fdd31c61141abd04a99fd6822c8558。alice该帐户的地址是使用Substrate EVM 实用程序根据该帐户的公钥计算得出的。

单击提交 RPC 调用。

该调用应返回类似于以下内容的输出：

```shell
2: eth.getBalance: U256
340,282,366,920,938,463,463,374,607,431,768,210,955
```
部署智能合约
现在您已经了解了如何查询以太坊地址的余额，您可能想探索如何部署和调用以太坊智能合约并测试相关功能。本教程使用定义ERC-20 代币的Truffle示例合约。您还可以使用 Polkadot JS SDK 和Typescript创建 ERC-20 代币合约。

创建 ERC-20 合约。

为方便起见，您可以使用从MyToken.jsonbytecode中的代币合约编译而来的合约在Substrate 区块链上部署。

验证您的节点是否仍在运行并且Polkadot-JS 应用程序已连接到该节点。
单击Developer，然后选择Extrinsics。
选择ALICE开发账户作为提交交易的账户。
选择evm。
选择创建函数。
配置函数的参数。

为了这	指定这个
source	0xd43593c715fdd31c61141abd04a99fd6822c8558
init	原始bytecode十六进制值来自MyToken.json
value	0
gasLimit	4294967295
maxFeePerGas	100000000
您可以将可选参数留空。的值nonce将增加源帐户的已知随机数，从 开始0x0。根据您选择的功能，您可能需要删除未使用的参数。

点击提交交易。
单击签名并提交以授权交易。
查看智能合约
提交交易后，合约将部署在网络上，您可以使用Polkadot-JS 应用程序查看有关它的信息。

验证您的节点是否仍在运行并且Polkadot-JS 应用程序已连接到该节点。
单击网络，然后选择资源管理器。
点击evm.Created事件，验证新创建合约的地址是0x8a50db1e0f9452cfd91be8dc004ceb11cb08832f.


成功的合约创建事件
您还可以使用浏览器开发者工具中的控制台查看有关交易的详细信息。

因为 EVM 合约地址是由合约创建者的账户标识符和 nonce 决定的，所以部署合约的地址是使用众所周知的账户标识符0xd43593c715fdd31c61141abd04a99fd6822c8558和该账户的 nonce0x0来计算的alice。

单击Developer，然后选择Chain State。
选择evm 作为要查询的状态和accountCodes。
指定账户的账户标识符0xd43593c715fdd31c61141abd04a99fd6822c8558，alice注意账户代码为空（0x）
0x8a50db1e0f9452cfd91be8dc004ceb11cb08832f为您使用开发账户部署的合约指定合约地址，alice并注意合约账户代码是来自 Solidity 合约的字节码。
查看帐户存储
您部署的 ERC-20 合约基于OpenZeppelin ERC-20 实现。该合约包含一个构造函数，该构造函数生成最大数量的代币并将它们存储在与合约创建者关联的帐户中。

查询与智能合约关联的账户存储：

在以evm作为查询状态的Chain State 中，选择accountStorages。
指定 ERC-20 合约地址0x8a50db1e0f9452cfd91be8dc004ceb11cb08832f作为第一个参数。
指定要读取的存储槽作为第二个参数0x045c0350b9cf0df39c4b40400c965118df2dca5ce0fbcf0de4aafc099aea4a14。

地址的存储槽是使用Substrate EVM 实用程序根据槽 0 和帐户标识符计算的0xd43593c715fdd31c61141abd04a99fd6822c8558。

返回的值应该是0xffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff。

如果您在部署合约后检查alice账户余额，您会看到从账户中提取了一笔费用，并且getBalance(address, number)调用返回类似于以下的值：

340,282,366,920,938,463,463,374,603,530,233,757,803
转移代币
到目前为止，您只使用alice开发帐户。下一步是使用已部署的合约将代币转移到另一个帐户。

验证您的节点是否仍在运行并且Polkadot-JS 应用程序已连接到该节点。
单击Developer，然后选择Extrinsics。
选择ALICE开发账户作为提交交易的账户。
选择evm。
选择call以调用transfer(address, uint256)ERC-20 合约上的函数。
配置函数的参数。

为了这	指定这个
source	0xd43593c715fdd31c61141abd04a99fd6822c8558
target	0x8a50db1e0f9452cfd91be8dc004ceb11cb08832f
input	0xa9059cbb000000000000000000000000008eaf04151687736326c9fea17e25fc52876136930000000000000000000000000000000000000000000000000000000000000
value	0
gasLimit	4294967295
maxFeePerGas	100000000
source代表持有代币的账户。在这种情况下，是合约创建者source的 EVM 帐户。是从到转移代币alice的target合约地址。alicebob

该input参数是一个 EVM ABI 编码的函数调用，它指定执行传输 ( 0xa9059cbb) 的函数调用以及函数所需的参数。对于此函数，参数是bobEVM 帐户标识符 ( 0x8eaf04151687736326c9fea17e25fc5287613693) 和要转移的代币数量（221 或0xdd十六进制）。

本教程中的输入值是使用Remix Web IDE计算的。

点击提交交易。
单击签名并提交以授权交易。
验证令牌转移
提交交易后，合约将部署在网络上，您可以使用Polkadot-JS 应用程序查看有关它的信息。

验证您的节点是否仍在运行并且Polkadot-JS 应用程序已连接到该节点。
单击网络，然后选择资源管理器。
点击evm.Executed事件，验证执行合约的地址是0x8a50db1e0f9452cfd91be8dc004ceb11cb08832f.


成功执行事件
单击Developer，然后选择Chain State。
选择evm 作为要查询的状态和accountStorages。
检查合约地址0x8a50db1e0f9452cfd91be8dc004ceb11cb08832f和存储槽的存储0x045c0350b9cf0df39c4b40400c965118df2dca5ce0fbcf0de4aafc099aea4a14。

0xffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff22

如果您在部署合约后检查alice账户余额，您会看到从账户中提取了一笔费用，并且getBalance(address, number)调用返回类似于以下的值：

340,282,366,920,938,463,463,374,603,530,233,366,411

下一步去哪里
Moonbeam：以太坊兼容性
以太坊整合
EVM ABI 规范
ERC-20 代币标准