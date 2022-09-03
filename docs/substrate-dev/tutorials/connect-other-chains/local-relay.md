# 准备一个本地平行链测试网
在本教程中，您将配置本地中继链并连接平行链模板以在本地测试环境中使用。

在你开始之前
在开始之前，请验证以下内容：

通过安装Rust 和 Rust 工具链，您已经为 Substrate 开发配置了环境。
您之前已完成构建本地区块链教程。
您知道如何为可信验证者的私有网络生成链规范，如添加可信节点中所述。
您通常熟悉Polkadot和平行链的架构。
教程目标
通过完成本教程，您将实现以下目标：

识别软件需求。
设置您的平行链构建环境。
准备中继链规格。
在本地启动中继链。
匹配版本很关键
您必须使用本教程中规定的确切版本。平行链与它们连接的中继链代码库紧密耦合，因为它们共享许多共同的依赖关系。在处理整个 Substrate 文档中的任何示例时，请务必将相应版本的 Polkadot 与任何其他软件一起使用。您必须与中继链升级保持同步，您的平行链才能继续成功运行。如果你不跟上中继链升级，你的网络很可能会停止生产区块。

文档示例版本控制
文档中的所有教程都经过测试可以使用：

波尔卡圆点v0.9.26
底物平行链模板polkadot-v0.9.26
Polkadot-JS 应用程序v0.116.2-34。通常预计托管的 Polkadot-JS 应用程序应该可以工作。如果您有问题，请在此标记的版本/提交中自己构建并运行此 UI。
搭建中继链节点
Polkadot 内置rococo-local网络配置的略微修改版本将用作本教程的中继链。
```shell
# Clone the Polkadot Repository, with correct version
git clone --depth 1 --branch release-v0.9.26 https://github.com/paritytech/polkadot.git

# Switch into the Polkadot directory
cd polkadot

# Build the relay chain Node
cargo b -r
```
编译节点可能需要 15 到 60 分钟才能完成。
```shell
# Check if the help page prints to ensure the node is built correctly
./target/release/polkadot --help
```
如果打印了帮助页面，说明你已经成功构建了一个 Polkadot 节点。

中继链规格
您将需要中继链网络的链规范。由于我们想使用本地测试网，可能需要自定义配置来设置验证器的开发或自定义密钥、启动节点地址等。

中继链运行的验证器节点必须比连接的平行链收集器的总数多一个。 为了测试这些通常必须硬编码到您的链规范中。例如，如果你想用一个收集器连接两个平行链，运行三个或更多中继链验证器节点，并确保它们都在你的链规范中指定。

无论您选择使用哪个链规范文件，我们都将chain-spec.json按照以下说明简单地引用该文件。您需要为您正在使用的链规范提供正确的路径。

预配置的链规格文件
本教程包含一个示例链规范文件，其中两个验证者中继链节点——Alice 和 Bob——作为权威。您可以在不修改本地测试网络和单个平行链的情况下使用此示例链规范。这对于注册单个平行链很有用：

普通 rococo-local 中继链规格
原始 rococo-local 中继链规格
您可以阅读和编辑纯链规范文件。但是，链规范文件必须收敛到 SCALE 编码的原始格式，然后才能用于启动节点。有关将链规范转换为使用原始格式的信息，请参阅自定义链规范指南。

示例链规范仅对具有两个验证器节点的单个平行链有效。如果您添加其他验证器，向中继链添加额外的平行链，或者想要使用自定义非开发密钥，则需要创建适合您需求的自定义链规范。

启动您的中继链
在开始平行链的区块生产之前，您需要启动一条中继链供它们连接。本节介绍如何使用双验证器原始链规范启动两个节点。启动其他节点的步骤类似。

启动alice验证器
```shell
# Start Relay `Alice` node
./target/release/polkadot \
--alice \
--validator \
--base-path /tmp/relay/alice \
--chain <path to spec json> \
--port 30333 \
--ws-port 9944
```
该命令中指定的端口（port）和websocket端口（ws-port）使用默认值，可以省略。但是，此处包含这些值是为了提醒您始终检查这些值。节点启动后，同一本地机器上的其他节点都不能使用这些端口。
```shell
🏷 Local node identity is: 12D3KooWGjsmVmZCM1jPtVNp6hRbbkGBK3LADYNniJAKJ19NUYiq
```
当节点启动时，您将看到几条日志消息，包括节点的Peer ID。请注意这一点，因为在将其他节点连接到它时将需要它。

启动bob验证器
启动第二个节点的命令与启动第一个节点的命令类似，但有一些重要区别。
```shell
./target/release/polkadot \
--bob \
--validator \
--base-path /tmp/relay-bob \
--chain <path to spec json> \
--bootnodes /ip4/<Alice IP>/tcp/30333/p2p/<Alice Peer ID> \
--port 30334 \
--ws-port 9945
```
请注意，此命令使用不同的基本路径 ( /tmp/relay-bob)、验证器密钥 ( --bob) 和端口 (30334和9945)。

启动第二个节点的命令还包括--bootnodes用于指定第一个节点的 IP 地址和对等标识符的命令行选项。如果您在单个本地计算机上运行整个网络，则该bootnodes选项不是绝对必要的，但是当使用与链规范中没有任何指定引导节点的非本地网络的连接时，该选项是必要的，就像rococo-我们正在使用的 custom-2-plain.json示例。

对于本教程，您的最终链规范文件名必须以开头，rococo否则节点将不知道要包含哪些运行时逻辑。

更多资源
手动构建和配置中继链是一项很棒的练习。但是，在您完成几次之后，您可能希望自动化该过程。有很多方法可以解决这个问题，这里有一些供参考：

parachain-launch是一个生成 docker compose 文件的脚本，允许您启动多个区块链节点的测试网。
zombienet是一个 CLI 工具，可让您生成临时 Polkadot/Substrate 网络并针对它们执行测试。
下一步
使用正在运行的中继链，您自然会想要将平行链连接到它！