# 添加可信节点

本教程说明了如何使用一组私有验证器来启动一个小型的独立区块链网络。

正如您在区块链基础知识中所了解的，所有区块链都要求网络中的节点在任何特定时间点就数据状态达成一致，这种关于状态的一致称为共识。

Substrate 节点模板使用权威证明共识模型，也称为权威轮或Aura共识。Aura 共识协议将区块生产限制在授权账户的轮换列表中。授权账户——权威——以循环方式创建块，通常被认为是网络中受信任的参与者。

这种共识模型提供了一种简单的方法来为有限数量的参与者启动单独的区块链。在本教程中，您将看到如何生成授权节点参与网络所需的密钥，如何配置和与其他授权帐户共享有关网络的信息，以及如何使用一组经过批准的验证器启动网络.

在你开始之前
在开始之前，请验证以下内容：

通过安装Rust 和 Rust 工具链，您已经为 Substrate 开发配置了环境。
您已完成构建本地区块链并在本地安装了 Substrate 节点模板。
您已使用模拟网络中所述的预定义帐户在单台计算机上启动节点。
您通常熟悉软件开发和使用命令行界面。
您通常熟悉区块链和智能合约平台。
教程目标
通过完成本教程，您将实现以下目标：

生成用作网络授权的密钥对。
创建自定义链规范文件。
启动一个私有的两节点区块链网络。
生成您的帐户和密钥
在模拟网络中，您使用预定义的帐户和密钥启动了对等节点。在本教程中，您将为网络中的验证器节点生成自己的密钥。重要的是要记住，区块链网络中的每个参与者都负责生成唯一的密钥。

密钥生成选项
有几种方法可以生成密钥。例如，您可以使用`node-template`子命令、独立的子密钥命令行程序、`Polkadot-JS` 应用程序或第三方密钥生成实用程序来生成密钥对。

尽管您可以使用预定义的密钥对来完成本教程，但您永远不会在生产环境中使用这些密钥。本教程不使用预定义的密钥或更安全的`subkey`程序，而是说明如何使用 `Substrate` 节点模板和key子命令生成密钥。

使用节点模板生成本地密钥
作为最佳实践，当您为生产区块链生成密钥时，您应该使用从未连接到互联网的气隙计算机。至少，在生成任何打算在不受您控制的公共或私有区块链上使用的密钥之前，您应该断开互联网连接。

但是，对于本教程，您可以使用`node-template`命令行选项在本地生成随机密钥并保持与 Internet 的连接。

要使用节点模板生成密钥：

在您的计算机上打开终端外壳。
切换到编译 Substrate 节点模板的根目录。
通过运行以下命令生成随机密钥短语和密钥：
```shell
./target/release/node-template key generate --scheme Sr25519 --password-interactive
```
为生成的密钥键入密码。

该命令生成密钥并显示类似于以下内容的输出：
```shell
Secret phrase:  pig giraffe ceiling enter weird liar orange decline behind total despair fly
Secret seed:       0x0087016ebbdcf03d1b7b2ad9a958e14a43f2351cd42f2f0a973771b90fb0112f
Public key (hex):  0x1a4cc824f6585859851f818e71ac63cf6fdc81018189809814677b2a4699cf45
Account ID:        0x1a4cc824f6585859851f818e71ac63cf6fdc81018189809814677b2a4699cf45
Public key (SS58): 5CfBuoHDvZ4fd8jkLQicNL8tgjnK8pVG9AiuJrsNrRAx6CNW
SS58 Address:      5CfBuoHDvZ4fd8jkLQicNL8tgjnK8pVG9AiuJrsNrRAx6CNW
```
aura您现在拥有用于生成用于一个节点的块的 Sr25519 密钥。在此示例中，帐户的 Sr25519 公钥是`5CfBuoHDvZ4fd8jkLQicNL8tgjnK8pVG9AiuJrsNrRAx6CNW`。

使用您刚刚生成的帐户的密码短语使用 Ed25519 签名方案派生密钥。

例如，运行类似于以下的命令：
```shell
./target/release/node-template key inspect --password-interactive --scheme Ed25519 "pig giraffe ceiling enter weird liar orange decline behind total despair fly"
```
键入用于生成密钥的密码。

该命令显示类似于以下内容的输出：
```shell
Secret phrase `pig giraffe ceiling enter weird liar orange decline behind total despair fly` is account:
Secret seed:       0x0087016ebbdcf03d1b7b2ad9a958e14a43f2351cd42f2f0a973771b90fb0112f
Public key (hex):  0x2577ba03f47cdbea161851d737e41200e471cd7a31a5c88242a527837efc1e7b
Public key (SS58): 5CuqCGfwqhjGzSqz5mnq36tMe651mU9Ji8xQ4JRuUTvPcjVN
Account ID:        0x2577ba03f47cdbea161851d737e41200e471cd7a31a5c88242a527837efc1e7b
SS58 Address:      5CuqCGfwqhjGzSqz5mnq36tMe651mU9Ji8xQ4JRuUTvPcjVN
```
您现在拥有 Ed25519 密钥，用于完成grandpa用于一个节点的块。在此示例中，帐户的 Ed25519 公钥是5CuqCGfwqhjGzSqz5mnq36tMe651mU9Ji8xQ4JRuUTvPcjVN.

生成第二组密钥
对于本教程，专用网络仅包含两个节点，因此您需要两组密钥。您有几个选项可以继续本教程：

您可以将密钥用于预定义帐户之一。
您可以使用本地计算机上的不同身份重复上一节中的步骤，以生成第二个密钥对。
您可以派生一个子密钥对来模拟本地计算机上的第二个身份。
您可以招募其他参与者来生成加入您的私有网络所需的密钥。
出于说明目的，本教程中使用的第二组键是：

Sr25519：5EJPj83tJuJtTVE2v7B9ehfM7jNT44CBFaPWicvBwYyUKBS6 用于aura.
Ed25519：5FeJQsfmbbJLTH1pvehBxrZrT5kHvJFj84ZaY5LK7NU87gZS 为grandpa.
创建自定义链规范
生成用于区块链的密钥后，您就可以使用这些密钥对创建自定义链规范，然后与称为验证器的受信任网络参与者共享您的自定义链规范。

为了使其他人能够参与您的区块链网络，请确保他们生成自己的密钥。收集网络参与者的密钥后，您可以创建自定义链规范来替换local链规范。

为简单起见，您在本教程中创建的自定义链规范是local链规范的修改版本，它说明了如何创建双节点网络。如果您拥有所需的密钥，您可以按照相同的步骤将更多节点添加到网络中。

修改本地链规范
您可以修改预定义的链规范，而不是编写一个全新的local链规范。

要基于本地规范创建新的链规范：

在您的计算机上打开终端外壳。
切换到编译 Substrate 节点模板的根目录。
local将链规范导出到customSpec.json通过运行以下命令命名的文件：
```shell
./target/release/node-template build-spec --disable-default-bootnode --chain local > customSpec.json
```

如果您`customSpec.json`在文本编辑器中打开该文件，您会看到它包含多个字段。`cargo build --release`这些字段之一是您使用该命令构建的运行时的 WebAssembly (Wasm) 二进制文件。由于 WebAssembly (Wasm) 二进制文件是一个大 blob，因此您可以预览第一行和最后几行以查看需要更改的字段。

`customSpec.json`通过运行以下命令预览文件中的前几个字段：
```shell
head customSpec.json
```
该命令显示文件中的第一个字段。例如：
```jsone
{
 "name": "Local Testnet",
 "id": "local_testnet",
 "chainType": "Local",
 "bootNodes": [],
 "telemetryEndpoints": null,
 "protocolId": null,
 "properties": null,
 "consensusEngine": null,
 "codeSubstitutes": {},
```
customSpec.json通过运行以下命令预览文件中的最后一个字段：
```shell
tail -n 80 customSpec.json
```
此命令显示 Wasm 二进制字段后面的最后部分，包括运行时中使用的几个托盘的详细信息，例如sudo和balances托盘。

`customSpec.json`在文本编辑器中打开文件。
修改该name字段以将此链规范标识为自定义链规范。

例如：
```
"name": "My Custom Testnet",
```
修改aura字段以通过为每个网络参与者添加 Sr25519 SS58 地址密钥来指定有权创建块的节点。
```json
"aura": { "authorities": [
 "5CfBuoHDvZ4fd8jkLQicNL8tgjnK8pVG9AiuJrsNrRAx6CNW", "5CXGP4oPXC1Je3zf5wEDkYeAqGcGXyKWSRX2Jm14GdME5Xc5"
 ]
},
```

修改该grandpa字段以通过为每个网络参与者添加 Ed25519 SS58 地址密钥来指定有权完成区块的节点。
```json
"grandpa": {
   "authorities": [
     [
       "5CuqCGfwqhjGzSqz5mnq36tMe651mU9Ji8xQ4JRuUTvPcjVN",
       1
     ],
     [
       "5DpdMN4bVTMy67TfMMtinQTcUmLhZBWoWarHvEYPM4jYziqm",
       1
     ]
   ]
 },
```
authorities请注意，该部分中的字段有两个数据值grandpa。第一个值是地址键。第二个值用于支持加权投票。在此示例中，每个验证者的权重为1票。

保存更改并关闭文件。
添加验证器
如您所见，您可以通过修改aura和grandpa部分来添加和更改链规范中的权限地址。您可以使用此技术添加任意数量的验证器。

添加验证器：

修改该aura部分以包括Sr25519地址。
修改该grandpa部分以包含Ed25519地址和投票权重。
确保为每个验证器使用唯一的密钥。如果两个验证器具有相同的密钥，它们会产生冲突的块。

有关使用密钥对和签名的更多信息，请参阅公钥加密。

将链规范转换为原始格式
使用验证器信息准备链规范后，必须将其转换为原始规范格式才能使用。原始链规范包含与未转换规范相同的信息。但是，原始链规范还包含编码的存储密钥，节点用于引用其本地存储中的数据。分发原始链规范可确保每个节点使用正确的存储密钥存储数据。

要将链规范转换为使用原始格式：

在您的计算机上打开终端外壳。
切换到编译 Substrate 节点模板的根目录。
通过运行以下命令将customSpec.json链规范转换为带有文件名的原始格式：`customSpecRaw.json`
```shell
./target/release/node-template build-spec --chain=customSpec.json --raw --disable-default-bootnode > customSpecRaw.json
```
与他人共享链规范
如果您正在创建一个私有区块链网络以与其他参与者共享，请确保只有一个人创建链规范并与`customSpecRaw.json`网络中的所有其他验证者共享该规范的最终原始版本（例如文件）。

因为 Rust 编译器生成的优化的 WebAssembly 二进制文件在确定性上无法重现，所以每个生成 Wasm 运行时的人都会生成稍微不同的 Wasm blob。为了确保确定性，区块链网络中的所有参与者必须使用完全相同的原始链规范文件。有关此问题的更多信息，请参阅在我们的 Rust Wasm 构建中寻找非确定性错误。

准备启动专用网络
将自定义链规范分发给所有网络参与者后，您就可以启动自己的私有区块链了。这些步骤类似于您在启动第一个区块链节点中执行的步骤。但是，如果您按照本教程中的步骤操作，则可以将多台计算机添加到您的网络中。

要继续，请验证以下内容：

您已经为至少两个权限帐户生成或收集了帐户密钥。
您已更新您的自定义链规范，以包含用于块生产 `( aura)` 和块完成 `( grandpa)` 的密钥。
您已将自定义链规范转换为原始格式，并将原始链规范分发给参与私有网络的节点。
如果您已完成这些步骤，您就可以启动私有区块链中的第一个节点了。

启动第一个节点
作为私有区块链网络的第一个参与者，您负责启动第一个节点，称为`bootnode`。

启动第一个节点：

在您的计算机上打开终端外壳。
切换到编译 Substrate 节点模板的根目录。
通过运行类似于以下的命令，使用自定义链规范启动第一个节点：
```shell
./target/release/node-template \
  --base-path /tmp/node01 \
  --chain ./customSpecRaw.json \
  --port 30333 \
  --ws-port 9945 \
  --rpc-port 9933 \
  --telemetry-url "wss://telemetry.polkadot.io/submit/ 0" \
  --validator \
  --rpc-methods Unsafe \
  --name MyNode01 \
  --password-interactive
```
请注意您用于启动节点的以下命令行选项：

命令行选项指定与此第一个节点关联的链的`--base-path`自定义位置。
命令行`--chain`选项指定自定义链规范。
`--validator`命令行选项表明该节点是链的权威。
`--rpc-methods` Unsafe命令行选项允许您使用不安全的通信模式继续本教程，因为您的区块链未在生产环境中使用。
`--name`命令行选项使您能够在遥测 UI 中为您的节点提供一个人类可读的名称。
此命令还使用您自己的密钥而不是预定义的帐户来启动节点。由于您没有使用具有已知密钥的预定义帐户，因此您需要在单独的步骤中将密钥添加到密钥库。

查看节点操作信息
本地节点启动后，终端shell中会显示所执行的操作信息。在该终端中，验证您是否看到类似于以下内容的输出：
```shell
2021-11-03 15:32:14 Substrate Node
2021-11-03 15:32:14 ✌️  version 3.0.0-monthly-2021-09+1-bf52814-x86_64-macos
2021-11-03 15:32:14 ❤️  by Substrate DevHub <https://github.com/substrate-developer-hub>, 2017-2021
2021-11-03 15:32:14 📋 Chain specification: My Custom Testnet
2021-11-03 15:32:14 🏷 Node name: MyNode01
2021-11-03 15:32:14 👤 Role: AUTHORITY
2021-11-03 15:32:14 💾 Database: RocksDb at /tmp/node01/chains/local_testnet/db
2021-11-03 15:32:14 ⛓  Native runtime: node-template-100 (node-template-1.tx1.au1)
2021-11-03 15:32:15 🔨 Initializing Genesis block/state (state: 0x2bde…8f66, header-hash: 0x6c78…37de)
2021-11-03 15:32:15 👴 Loading GRANDPA authority set from genesis on what appears to be first startup.
2021-11-03 15:32:15 ⏱  Loaded block-time = 6s from block 0x6c78abc724f83285d1487ddcb1f948a2773cb38219c4674f84c727833be737de
2021-11-03 15:32:15 Using default protocol ID "sup" because none is configured in the chain specs
2021-11-03 15:32:15 🏷 Local node identity is: 12D3KooWLmrYDLoNTyTYtRdDyZLWDe1paxzxTw5RgjmHLfzW96SX
2021-11-03 15:32:15 📦 Highest known block at #0
2021-11-03 15:32:15 〽️ Prometheus exporter started at 127.0.0.1:9615
2021-11-03 15:32:15 Listening for new connections on 127.0.0.1:9945.
2021-11-03 15:32:20 💤 Idle (0 peers), best: #0 (0x6c78…37de), finalized #0 (0x6c78…37de), ⬇ 0 ⬆ 0
```
请注意以下信息：

输出表明正在使用的链规范是您使用`--chain`命令行选项创建和指定的自定义链规范。
输出表明该节点是一个权限，因为您使用`--validator`命令行选项启动了该节点。
输出显示使用块哈希初始化的创世块`(state: 0x2bde…8f66, header-hash: 0x6c78…37de)`。
输出指定您的节点的本地节点身份。在此示例中，节点标识为`12D3KooWLmrYDLoNTyTYtRdDyZLWDe1paxzxTw5RgjmHLfzW96SX`。
输出指定用于节点的IP 地址`127.0.0.1`是本地主机。
这些值适用于这个特定的教程示例。输出中的值将特定于您的节点，您必须将节点的值提供给其他网络参与者以连接到引导节点。

现在您已经使用自己的密钥成功启动了验证器节点并记下了节点身份，您可以继续下一步。但是，在将密钥添加到密钥库之前，请按 Control-c 停止节点。

将密钥添加到密钥库
启动第一个节点后，尚未生成任何块。下一步是将两种类型的密钥添加到网络中每个节点的密钥库中。

对于每个节点：

添加aura权限密钥以启用块生产。
添加grandpa权限密钥以启用块完成。
有几种方法可以将密钥插入密钥库。对于本教程，您可以使用key子命令插入本地生成的密钥。

要将密钥插入密钥库：

在您的计算机上打开终端外壳。
切换到编译 Substrate 节点模板的根目录。
通过运行类似于以下的命令插入aura从子命令生成的密钥：key
```shell
./target/release/node-template key insert --base-path /tmp/node01 \
  --chain customSpecRaw.json \
  --scheme Sr25519 \
  --suri <your-secret-seed> \
  --password-interactive \
  --key-type aura
```
替换为您在使用节点模板生成本地密钥`<your-secret-seed>`中生成的第一个密钥对的密钥短语或密钥种子。

在本教程中，秘密短语是`pig giraffe ceiling enter weird liar orange decline behind total despair fly`，因此`--suri`命令行选项指定将密钥插入密钥库的字符串。

例如：

`--suri "pig giraffe ceiling enter weird liar orange decline behind total despair fly"`
您还可以从指定的文件位置插入密钥。有关可用命令行选项的信息，请运行以下命令：
```shell
./target/release/node-template key insert --help
```
键入用于生成密钥的密码。
通过运行类似于以下的命令插入grandpa从子命令生成的密钥：key
```shell
./target/release/node-template key insert \
  --base-path /tmp/node01 \
  --chain customSpecRaw.json \
  --scheme Ed25519 \
  --suri <your-secret-key> \
  --password-interactive \
  --key-type gran
```
替换为您在使用节点模板生成本地密钥`<your-secret-seed>`中生成的第一个密钥对的密钥短语或密钥种子。

在本教程中，秘密短语是`pig giraffe ceiling enter weird liar orange decline behind total despair fly`，因此`--suri`命令行选项指定将密钥插入密钥库的字符串。例如：
```shell
--suri "pig giraffe ceiling enter weird liar orange decline behind total despair fly"
```
键入用于生成密钥的密码。
node01通过运行以下命令验证您的密钥是否在密钥库中：
```shell
ls /tmp/node01/chains/local_testnet/keystore
```
该命令显示类似于以下内容的输出：
```shell
617572611441ddcb22724420b87ee295c6d47c5adff0ce598c87d3c749b776ba9a647f04
6772616e1441ddcb22724420b87ee295c6d47c5adff0ce598c87d3c749b776ba9a647f04
```
将密钥添加到 `/tmp/node01` 下第一个节点的密钥库后，可以使用之前在启动第一个节点中使用的命令重新启动节点。

允许其他参与者加入
您现在可以使用`--bootnodes`和`--validator`命令行选项允许其他验证者加入网络。

将第二个验证器添加到专用网络：

在第二台计算机上打开终端外壳。
切换到编译 Substrate 节点模板的根目录。
通过运行类似于以下的命令启动第二个区块链节点：
```shell
./target/release/node-template \
  --base-path /tmp/node02 \
  --chain ./customSpecRaw.json \
  --port 30334 \
  --ws-port 9946 \
  --rpc-port 9934 \
  --telemetry-url "wss://telemetry.polkadot.io/submit/ 0" \
  --validator \
  --rpc-methods Unsafe \
  --name MyNode02 \
  --bootnodes /ip4/127.0.0.1/tcp/30333/p2p/12D3KooWLmrYDLoNTyTYtRdDyZLWDe1paxzxTw5RgjmHLfzW96SX \
  --password-interactive
```
此命令使用base-path和命令name行validator选项将此节点标识为专用网络的第二个验证器。`--chain`命令行选项指定要使用的链规范文件。对于网络中的所有验证器，此文件必须相同。请务必为`--bootnodes`命令行选项设置正确的信息。特别是，请确保您已从网络中的第一个节点指定本地节点标识符。如果您没有设置正确的bootnode标识符，您会看到如下错误：
```shell
The bootnode you want to connect to at ... provided a different peer ID than the one you expect: ...
```
通过运行类似于以下的命令添加aura从子命令生成的密钥：key
```shell
./target/release/node-template key insert --base-path /tmp/node02 \
  --chain customSpecRaw.json \
  --scheme Sr25519 \
  --suri <second-participant-secret-seed> \
  --password-interactive \
  --key-type aura
```
替换为您在生成第二个密钥对`<second-participant-secret-seed>`中生成的密码短语或密码种子。启用区块生产需要密钥类型。aura

键入用于生成密钥的密码。
通过运行类似于以下的命令，grandpa将从子命令生成的密钥添加到本地密钥库：key
```shell
./target/release/node-template key insert --base-path /tmp/node02 \
  --chain customSpecRaw.json \
  --scheme Ed25519 --suri <second-participant-secret-seed> \
  --password-interactive \
  --key-type gran
```
替换为您在生成第二个密钥对`<second-participant-secret-seed>`中生成的密码短语或密码种子。启用块最终确定需要密钥类型。gran

区块最终确定需要至少三分之二的验证者将他们的密钥添加到各自的密钥库中。因为这个网络在链规范中配置了两个验证器，所以只有在第二个节点添加了它的密钥之后才能开始区块终结。

键入用于生成密钥的密码。
node02通过运行以下命令验证您的密钥是否在密钥库中：
```shell
ls /tmp/node02/chains/local_testnet/keystore
```
该命令显示类似于以下内容的输出：
```shell
617572611a4cc824f6585859851f818e71ac63cf6fdc81018189809814677b2a4699cf45
6772616e1a4cc824f6585859851f818e71ac63cf6fdc81018189809814677b2a4699cf45
```
Substrate 节点在插入grandpa密钥后需要重新启动，因此您必须在看到区块完成之前关闭并重新启动节点。

按 Control-c 关闭节点。
通过运行以下命令重新启动第二个区块链节点：
```shell
./target/release/node-template \
  --base-path /tmp/node02 \
  --chain ./customSpecRaw.json \
  --port 30334 \
  --ws-port 9946 \
  --rpc-port 9934 \
  --telemetry-url 'wss://telemetry.polkadot.io/submit/ 0' \
  --validator \
  --rpc-methods Unsafe \
  --name MyNode02 \
  --bootnodes /ip4/127.0.0.1/tcp/30333/p2p/12D3KooWLmrYDLoNTyTYtRdDyZLWDe1paxzxTw5RgjmHLfzW96SX \
  --password-interactive
```
在两个节点都将其密钥添加到各自的密钥库（位于 和 下）`/tmp/node01`并`/tmp/node02`重新启动后，您应该会看到相同的创世块和状态根哈希。

您还应该看到每个节点都有一个对等节点（1 peers），并且它们产生了一个区块提议（best: #2 (0xe111…c084)）。几秒钟后，您应该会看到两个节点上的新块都已完成。

下一步
您现在已经了解了如何与受信任的参与者一起启动私有区块链。

在本教程中，您学习了：

如何启动和停止对等区块链节点。
如何生成自己的密钥对。
如何创建使用您生成的密钥的自定义链规范。
如何将验证器添加到使用您的自定义链规范的专用网络。
要了解有关本教程中介绍的主题的更多信息，请参阅以下部分：

帐户、地址和密钥
链条规格
膏我
密码学
