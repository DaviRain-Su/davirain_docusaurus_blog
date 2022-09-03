# 连接本地平行链

在本教程中，您会将平行链连接到本地中继链。

教程目标
通过完成本教程，您将实现以下目标：

ParaID在中继链上为您的平行链注册一个。
在中继链上开始生产平行链。
在你开始之前
在开始之前，请验证以下内容：

你已经完成了准备本地平行链测试网教程，并且拥有一个正在运行的本地网络，其中包含不少于两个验证器。
您已将准备本地平行链测试网中定义的特定软件版本合并到平行链中。例如，如果您使用的是Polkadotrelease-v0.9.26，请使用平行链模板polkadot-v0.9.26的版本。
构建平行链模板
本教程使用Substrate 平行链模板在您的本地中继链上启动平行链。平行链模板类似于单链开发中使用的节点模板，所以应该比较熟悉。稍后，您将使用此平行链模板作为开发您自己的平行链的起点。

在新的终端窗口中：
```shell
# Clone the parachain template with the correct Polkadot version
git clone --depth 1 --branch polkadot-v0.9.26 https://github.com/substrate-developer-hub/substrate-parachain-template

# Switch into the parachain template directory
cd substrate-parachain-template

# Build the parachain template collator
cargo b -r
```
编译节点可能需要 15 到 60 分钟。同时，在正在运行的中继链上预留一个ParaID准备，而不是ParaID你获得的。

预订 ParaID
要连接到任何中继链，您必须首先ParaID为您的平行链预留一个。之所以称为 a，是ParaID因为相同的标识符可以用作平行链 ID 或平行线程 ID，具体取决于您当前的中继链插槽状态。您必须有足够的资金来预订 ParaID。请参阅您的目标中继链以确定所需的令牌数量。

中继链负责分配所有ParaIDs 并简单地从所有非公益平行2000链的链开始递增。这些链使用不同的方法来分配s。ParaID

本练习说明了如何保留一个ParaID使用Polkadot-JS 连接到本地中继链的应用程序。在Network > Parachains子页面下，单击Parathreads选项卡并使用+ ParaId按钮。


预留一个“ParaID”
请注意，用于保留 的帐户ParaID也必须是使用 this 的来源ParaID。在您提交此交易并registrar.Reserved使用您的 ) 看到成功事件后paraID，您可以使用保留的 ID 启动您的平行链或平行线程。您现在可以使用保留的ParaID( ParaID 2000) 生成平行链所需的文件。

自定义平行链规范
您的链规范必须为您的平行链设置正确的保留ParaID。

请参阅配置自定义链规范的操作指南以获取更深入的说明以生成普通规范、修改它并生成原始规范。

我们首先生成一个普通的规范：
```shell
# Assumes that `rococo-local` is in `node/chan_spec.rs` as the relay you registered with
./target/release/parachain-template-node build-spec --disable-default-bootnode > rococo-local-parachain-plain.json
```
默认ParaID来自1000Cumulus，因此您必须根据上述保留为您的平行链正确设置它ParaID。假设您保留ParaID的是2000，您将打开rococo-local-parachain-plain.json并修改两个字段：
```json
  // --snip--
  "para_id": 2000, // <--- your already registered ID
  // --snip--
      "parachainInfo": {
        "parachainId": 2000 // <--- your already registered ID
      },
  // --snip--
```
然后生成从修改后的普通链规范派生的原始链规范：
```shell
./target/release/parachain-template-node build-spec --chain rococo-local-parachain-plain.json --raw --disable-default-bootnode > rococo-local-parachain-2000-raw.json
```
保存和分发您的原始规范
您必须有足够的资金来预订 ParaID。请参阅您的目标中继链以确定所需的令牌数量。

中继链负责分配所有ParaIDs 并简单地从所有非公益平行2000链的链开始递增。这些链使用不同的方法来分配s。ParaID

本练习说明了如何保留一个ParaID使用Polkadot-JS 连接到本地中继链的应用程序。在Network > Parachains子页面下，单击Parathreads选项卡并使用+ ParaId按钮。


预留一个“ParaID”
请注意，用于保留 的帐户ParaID也必须是使用 this 的来源ParaID。在您提交此交易并registrar.Reserved使用您的 ) 看到成功事件后paraID，您可以使用保留的 ID 启动您的平行链或平行线程。

获取 Wasm 运行时验证函数
中继链还需要特定于平行链的运行时验证逻辑来​​验证平行链块。平行链收集器节点也有一个命令来生成这个 Wasm blob：
```shell
./target/release/parachain-template-node export-genesis-wasm --chain rococo-local-parachain-2000-raw.json > para-2000-wasm
```
生成平行链创世状态
要注册平行链，中继链需要知道平行链的创世状态。整理者节点可以将该状态导出到文件。转到您的平行链模板文件夹，以下命令将创建一个文件，其中包含平行链的整个创世状态，十六进制编码：
```shell
./target/release/parachain-template-node export-genesis-state --chain rococo-local-parachain-2000-raw.json > para-2000-genesis
```
不允许预创区块

此运行时和状态仅适用于平行链的创世块。您不能将具有任何先前状态的平行链连接到中继链。所有平行链必须从中继链上的区块 0 开始。最终，迁移基于 Substrate 构建的单链的区块历史有望成为可能，但短期内不会计划此功能。

有关如何创建平行链模板以及如何将链的逻辑（而不是其历史或状态迁移）转换为平行链的详细信息，请参阅将单链转换为平行链的指南。

启动收集器节点
我们现在可以启动一个收集器节点。请注意，我们需要在命令的后半部分提供用于启动中继链节点的相同中继链链规范。使用以下命令启动整理器：
```shell
./target/release/parachain-template-node \
--alice \
--collator \
--force-authoring \
--chain rococo-local-parachain-2000-raw.json \
--base-path /tmp/parachain/alice \
--port 40333 \
--ws-port 8844 \
-- \
--execution wasm \
--chain <relay chain raw chain spec> \
--port 30343 \
--ws-port 9977
```
关于这个命令，首先要注意的是，在 lone 之前传递了--几个参数，在它之后传递了更多参数。平行链收集器包含实际收集器节点以及嵌入式中继链节点。之前的参数--是针对整理者的，之后的参数--是针对嵌入式中继链节点的。

我们给整理者一个基本路径和端口，就像我们之前为中继链节点所做的那样。

如果您为第二条平行链再次执行这些指令，请记住更改特定于整理者的值。您必须使用相同的中继链规范文件，但ParaID要绑定到的第二个平行链收集器使用不同的基本路径和端口号。

目前没有嵌入中继链节点的平行链节点无法运行，但预计非收集节点最终将成为可能。

此时，您的整理者日志应如下所示：
```shell
2021-05-30 16:57:39 Parachain Collator Template
2021-05-30 16:57:39 ✌️  version 3.0.0-acce183-x86_64-linux-gnu
2021-05-30 16:57:39 ❤️  by Anonymous, 2017-2021
2021-05-30 16:57:39 📋 Chain specification: Local Testnet
2021-05-30 16:57:39 🏷 Node name: Alice
2021-05-30 16:57:39 👤 Role: AUTHORITY
2021-05-30 16:57:39 💾 Database: RocksDb at /tmp/parachain/alice/chains/local_testnet/db
2021-05-30 16:57:39 ⛓  Native runtime: template-parachain-1 (template-parachain-0.tx1.au1)
2021-05-30 16:57:41 Parachain id: Id(2000)
2021-05-30 16:57:41 Parachain Account: 5Ec4AhPUwPeyTFyuhGuBbD224mY85LKLMSqSSo33JYWCazU4
2021-05-30 16:57:41 Parachain genesis state: 0x0000000000000000000000000000000000000000000000000000000000000000000a96f42b5cb798190e5f679bb16970905087a9a9fc612fb5ca6b982b85783c0d03170a2e7597b7b7e3d84c05391d139a62b157e78786d8c082f29dcf4c11131400
2021-05-30 16:57:41 Is collating: yes
2021-05-30 16:57:41 [Parachain] 🔨 Initializing Genesis block/state (state: 0x0a96…3c0d, header-hash: 0xd42b…f271)
2021-05-30 16:57:41 [Parachain] ⏱  Loaded block-time = 12s from block 0xd42bb78354bc21770e3f0930ed45c7377558d2d8e81ca4d457e573128aabf271
2021-05-30 16:57:43 [Relaychain] 🔨 Initializing Genesis block/state (state: 0xace1…1b62, header-hash: 0xfa68…cf58)
2021-05-30 16:57:43 [Relaychain] 👴 Loading GRANDPA authority set from genesis on what appears to be first startup.
2021-05-30 16:57:44 [Relaychain] ⏱  Loaded block-time = 6s from block 0xfa68f5abd2a80394b87c9bd07e0f4eee781b8c696d0a22c8e5ba38ae10e1cf58
2021-05-30 16:57:44 [Relaychain] 👶 Creating empty BABE epoch changes on what appears to be first startup.
2021-05-30 16:57:44 [Relaychain] 🏷 Local node identity is: 12D3KooWBjYK2W4dsBfsrFA9tZCStb5ogPb6STQqi2AK9awXfXyG
2021-05-30 16:57:44 [Relaychain] 📦 Highest known block at #0
2021-05-30 16:57:44 [Relaychain] 〽️ Prometheus server started at 127.0.0.1:9616
2021-05-30 16:57:44 [Relaychain] Listening for new connections on 127.0.0.1:9945.
2021-05-30 16:57:44 [Parachain] Using default protocol ID "sup" because none is configured in the chain specs
2021-05-30 16:57:44 [Parachain] 🏷 Local node identity is: 12D3KooWADBSC58of6ng2M29YTDkmWCGehHoUZhsy9LGkHgYscBw
2021-05-30 16:57:44 [Parachain] 📦 Highest known block at #0
2021-05-30 16:57:44 [Parachain] Unable to listen on 127.0.0.1:9945
2021-05-30 16:57:44 [Parachain] Unable to bind RPC server to 127.0.0.1:9945. Trying random port.
2021-05-30 16:57:44 [Parachain] Listening for new connections on 127.0.0.1:45141.
2021-05-30 16:57:45 [Relaychain] 🔍 Discovered new external address for our node: /ip4/192.168.42.204/tcp/30334/ws/p2p/12D3KooWBjYK2W4dsBfsrFA9tZCStb5ogPb6STQqi2AK9awXfXyG
2021-05-30 16:57:45 [Parachain] 🔍 Discovered new external address for our node: /ip4/192.168.42.204/tcp/30333/p2p/12D3KooWADBSC58of6ng2M29YTDkmWCGehHoUZhsy9LGkHgYscBw
2021-05-30 16:57:48 [Relaychain] ✨ Imported #8 (0xe60b…9b0a)
2021-05-30 16:57:49 [Relaychain] 💤 Idle (2 peers), best: #8 (0xe60b…9b0a), finalized #5 (0x1e6f…567c), ⬇ 4.5kiB/s ⬆ 2.2kiB/s
2021-05-30 16:57:49 [Parachain] 💤 Idle (0 peers), best: #0 (0xd42b…f271), finalized #0 (0xd42b…f271), ⬇ 2.0kiB/s ⬆ 1.7kiB/s
2021-05-30 16:57:54 [Relaychain] ✨ Imported #9 (0x1af9…c9be)
2021-05-30 16:57:54 [Relaychain] ✨ Imported #9 (0x6ed8…fdf6)
2021-05-30 16:57:54 [Relaychain] 💤 Idle (2 peers), best: #9 (0x1af9…c9be), finalized #6 (0x3319…69a2), ⬇ 1.8kiB/s ⬆ 0.5kiB/s
2021-05-30 16:57:54 [Parachain] 💤 Idle (0 peers), best: #0 (0xd42b…f271), finalized #0 (0xd42b…f271), ⬇ 0.2kiB/s ⬆ 0.2kiB/s
2021-05-30 16:57:59 [Relaychain] 💤 Idle (2 peers), best: #9 (0x1af9…c9be), finalized #7 (0x5b50…1e5b), ⬇ 0.6kiB/s ⬆ 0.4kiB/s
2021-05-30 16:57:59 [Parachain] 💤 Idle (0 peers), best: #0 (0xd42b…f271), finalized #0 (0xd42b…f271), ⬇ 0 ⬆ 0
2021-05-30 16:58:00 [Relaychain] ✨ Imported #10 (0xc9c9…1ca3)
```
您应该看到您的收集人节点作为独立节点运行，并且其中继节点作为对等节点与已经运行的中继链验证节点连接。

请注意，如果您没有看到嵌入式中继链与本地中继链节点对等，请尝试禁用防火墙或添加bootnodes带有中继节点地址的参数。

它还没有开始创作平行链区块。当收集人实际在中继链上注册时，将开始创作。

平行链注册
有几种方法可以将平行链注册到中继链。对于测试，使用sudo是最常见的。

注册使用sudo
我们已经启动了中继链，并且我们的平行链整理器已经准备就绪。现在我们必须在中继链上注册平行链。在生产网络中，这通常通过平行链拍卖来完成。但是对于本教程，我们将使用sudo调用来完成。

注册交易
交易可以通过 Polkadot-JS Apps UI在中继链节点上提交。有多种选择可以解决这个问题，我们可以选择以下任何一种。请注意，此处的所有选项都取决于paraID保留- 因此请务必先执行此操作。

如果您正在运行一个具有两个以上验证器的网络，您可以通过相同的界面添加更多平行链，并相应地调整参数。

选项1：paraSudoWrapper.sudoScheduleParaInitialize
此选项完全绕过插槽租赁机制，以载入平行链或平行线程，以便paraID在下一个中继链会话开始时保留。这是进行测试的最简单和最快的方法。这些注册平行链所需的文件包括在您的链规范中设置的详细信息，这些详细信息必须明确针对正确的中继链并使用正确的ParaID- 在这种情况下，rococo（而不是rococo-local在本教程中使用）。

转到Polkadot Apps UI，连接到你的中继链。
通过转到Developer->sudo页面在中继链上执行 sudo extrinsic。
选择paraSudoWrapper->sudoScheduleParaInitialize(id, genesis)作为外部类型，如下所示。

平行链注册-sudo.png
在外部参数中，指定：

设置id: ParaId为 2,000
genesisHead：上传文件para-2000-genesis（来自上一步）
validationCode：上传文件para-2000-wasm（来自上一步）
将parachain: Bool选项设置为是
如果成功，此调度将发出sudo.Sudid事件，可在中继链浏览器页面中查看。

选项 2：slots.forceLease
这是生产中使用的更正式的注册流程（除了使用 sudo 强制槽租用）：您使用保留它paraID的相同帐户注册您的保留，或者如果您愿意，可以使用sudo外部registrar.forceRegister帐户。

在这里，我们将使用sudo来授予自己一个租约。你应该有一个板载的平行线程：


parathread-onboarding.png
转到Polkadot Apps UI，连接到你的中继链。
通过转到Developer->sudo页面在中继链上执行 sudo extrinsic。
选择slots->forceLease(para, leaser, amount, period_begin, period_end)作为外部类型，如下所示。

forceLease.png
请务必将开始时间段设置为您希望开始的时段。0在测试中，如果您启动了一个新的中继链，这很可能是已经活跃的插槽。将其延长到您希望测试此平行链的时间可能是最好的，除非您想测试入职和离职周期。在这种情况下，然后选择对 a 有间隙的槽租约paraID就可以了。一旦完全启动并在块生产开始后，您应该会看到：


平行链主动租赁.png
块生产
一旦注册成功并且下一个中继链时代开始，整理者应该开始生产平行链块（也称为整理） ！

可能还要等一下！在等待下一个纪元开始时请耐心等待。这是准备本地平行链测试网教程rococo-custom-2-raw.json中包含的示例的 10 个块。

最后，collat​​or 应该开始生成如下日志消息：
```shell
# Notice the relay epoch change! Only then do we start parachain collating!
#
2021-05-30 17:00:04 [Relaychain] 💤 Idle (2 peers), best: #30 (0xfc02…2a2a), finalized #28 (0x10ff…6539), ⬇ 1.0kiB/s ⬆ 0.3kiB/s
2021-05-30 17:00:04 [Parachain] 💤 Idle (0 peers), best: #0 (0xd42b…f271), finalized #0 (0xd42b…f271), ⬇ 0 ⬆ 0
2021-05-30 17:00:06 [Relaychain] 👶 New epoch 3 launching at block 0x68bc…0605 (block slot 270402601 >= start slot 270402601).
2021-05-30 17:00:06 [Relaychain] 👶 Next epoch starts at slot 270402611
2021-05-30 17:00:06 [Relaychain] ✨ Imported #31 (0x68bc…0605)
2021-05-30 17:00:06 [Parachain] Starting collation. relay_parent=0x68bcc93d24a31a2c89800a56c7a2b275fe9ca7bd63f829b64588ae0d99280605 at=0xd42bb78354bc21770e3f0930ed45c7377558d2d8e81ca4d457e573128aabf271
2021-05-30 17:00:06 [Parachain] 🙌 Starting consensus session on top of parent 0xd42bb78354bc21770e3f0930ed45c7377558d2d8e81ca4d457e573128aabf271
2021-05-30 17:00:06 [Parachain] 🎁 Prepared block for proposing at 1 [hash: 0xf6507812bf60bf53af1311f775aac03869be870df6b0406b2969784d0935cb92; parent_hash: 0xd42b…f271; extrinsics (2): [0x1bf5…1d76, 0x7c9b…4e23]]
2021-05-30 17:00:06 [Parachain] 🔖 Pre-sealed block for proposal at 1. Hash now 0x80fc151d7ccf228b802525022b6de257e42388ec7dc3c1dd7de491313650ccae, previously 0xf6507812bf60bf53af1311f775aac03869be870df6b0406b2969784d0935cb92.
2021-05-30 17:00:06 [Parachain] ✨ Imported #1 (0x80fc…ccae)
2021-05-30 17:00:06 [Parachain] Produced proof-of-validity candidate. block_hash=0x80fc151d7ccf228b802525022b6de257e42388ec7dc3c1dd7de491313650ccae
2021-05-30 17:00:09 [Relaychain] 💤 Idle (2 peers), best: #31 (0x68bc…0605), finalized #29 (0xa6fa…9e16), ⬇ 1.2kiB/s ⬆ 129.9kiB/s
2021-05-30 17:00:09 [Parachain] 💤 Idle (0 peers), best: #0 (0xd42b…f271), finalized #0 (0xd42b…f271), ⬇ 0 ⬆ 0
2021-05-30 17:00:12 [Relaychain] ✨ Imported #32 (0x5e92…ba30)
2021-05-30 17:00:12 [Relaychain] Moving approval window from session 0..=2 to 0..=3
2021-05-30 17:00:12 [Relaychain] ✨ Imported #32 (0x8144…74eb)
2021-05-30 17:00:14 [Relaychain] 💤 Idle (2 peers), best: #32 (0x5e92…ba30), finalized #29 (0xa6fa…9e16), ⬇ 1.4kiB/s ⬆ 0.2kiB/s
2021-05-30 17:00:14 [Parachain] 💤 Idle (0 peers), best: #0 (0xd42b…f271), finalized #0 (0xd42b…f271), ⬇ 0 ⬆ 0
2021-05-30 17:00:18 [Relaychain] ✨ Imported #33 (0x8c30…9ccd)
2021-05-30 17:00:18 [Parachain] Starting collation. relay_parent=0x8c30ce9e6e9867824eb2aff40148ac1ed64cf464f51c5f2574013b44b20f9ccd at=0x80fc151d7ccf228b802525022b6de257e42388ec7dc3c1dd7de491313650ccae
2021-05-30 17:00:19 [Relaychain] 💤 Idle (2 peers), best: #33 (0x8c30…9ccd), finalized #30 (0xfc02…2a2a), ⬇ 0.7kiB/s ⬆ 0.4kiB/s
2021-05-30 17:00:19 [Parachain] 💤 Idle (0 peers), best: #1 (0x80fc…ccae), finalized #0 (0xd42b…f271), ⬇ 0 ⬆ 0
2021-05-30 17:00:22 [Relaychain] 👴 Applying authority set change scheduled at block #31
2021-05-30 17:00:22 [Relaychain] 👴 Applying GRANDPA set change to new set [(Public(88dc3417d5058ec4b4503e0c12ea1a0a89be200fe98922423d4334014fa6b0ee (5FA9nQDV...)), 1), (Public(d17c2d7823ebf260fd138f2d7e27d114c0145d968b5ff5006125f2414fadae69 (5GoNkf6W...)), 1)]
2021-05-30 17:00:22 [Relaychain] 👴 Imported justification for block #31 that triggers command Changing authorities, signaling voter.
2021-05-30 17:00:24 [Relaychain] ✨ Imported #34 (0x211b…febf)
2021-05-30 17:00:24 [Parachain] Starting collation. relay_parent=0x211b3c53bebeff8af05e8f283d59fe171b7f91a5bf9c4669d88943f5a42bfebf at=0x80fc151d7ccf228b802525022b6de257e42388ec7dc3c1dd7de491313650ccae
2021-05-30 17:00:24 [Parachain] 🙌 Starting consensus session on top of parent 0x80fc151d7ccf228b802525022b6de257e42388ec7dc3c1dd7de491313650ccae
2021-05-30 17:00:24 [Parachain] 🎁 Prepared block for proposing at 2 [hash: 0x10fcb3180e966729c842d1b0c4d8d2c4028cfa8bef02b909af5ef787e6a6a694; parent_hash: 0x80fc…ccae; extrinsics (2): [0x4a6c…1fc6, 0x6b84…7cea]]
2021-05-30 17:00:24 [Parachain] 🔖 Pre-sealed block for proposal at 2. Hash now 0x5087fd06b1b73d90cfc3ad175df8495b378fffbb02fea212cc9e49a00fd8b5a0, previously 0x10fcb3180e966729c842d1b0c4d8d2c4028cfa8bef02b909af5ef787e6a6a694.
2021-05-30 17:00:24 [Parachain] ✨ Imported #2 (0x5087…b5a0)
2021-05-30 17:00:24 [Parachain] Produced proof-of-validity candidate. block_hash=0x5087fd06b1b73d90cfc3ad175df8495b378fffbb02fea212cc9e49a00fd8b5a0
2021-05-30 17:00:24 [Relaychain] 💤 Idle (2 peers), best: #34 (0x211b…febf), finalized #31 (0x68bc…0605), ⬇ 1.0kiB/s ⬆ 130.1kiB/s
2021-05-30 17:00:24 [Parachain] 💤 Idle (0 peers), best: #1 (0x80fc…ccae), finalized #0 (0xd42b…f271), ⬇ 0 ⬆ 0
2021-05-30 17:00:29 [Relaychain] 💤 Idle (2 peers), best: #34 (0x211b…febf), finalized #32 (0x5e92…ba30), ⬇ 0.2kiB/s ⬆ 0.1kiB/s
2021-05-30 17:00:29 [Parachain] 💤 Idle (0 peers), best: #1 (0x80fc…ccae), finalized #0 (0xd42b…f271), ⬇ 0 ⬆ 0
2021-05-30 17:00:30 [Relaychain] ✨ Imported #35 (0xee07…38a0)
2021-05-30 17:00:34 [Relaychain] 💤 Idle (2 peers), best: #35 (0xee07…38a0), finalized #33 (0x8c30…9ccd), ⬇ 0.9kiB/s ⬆ 0.3kiB/s
2021-05-30 17:00:34 [Parachain] 💤 Idle (0 peers), best: #1 (0x80fc…ccae), finalized #1 (0x80fc…ccae), ⬇ 0 ⬆ 0
2021-05-30 17:00:36 [Relaychain] ✨ Imported #36 (0xe8ce…4af6)
2021-05-30 17:00:36 [Parachain] Starting collation. relay_parent=0xe8cec8015c0c7bf508bf3f2f82b1696e9cca078e814b0f6671f0b0d5dfe84af6 at=0x5087fd06b1b73d90cfc3ad175df8495b378fffbb02fea212cc9e49a00fd8b5a0
2021-05-30 17:00:39 [Relaychain] 💤 Idle (2 peers), best: #36 (0xe8ce…4af6), finalized #33 (0x8c30…9ccd), ⬇ 0.6kiB/s ⬆ 0.1kiB/s
2021-05-30 17:00:39 [Parachain] 💤 Idle (0 peers), best: #2 (0x5087…b5a0), finalized #1 (0x80fc…ccae), ⬇ 0 ⬆ 0
```
区块敲定
中继链跟踪每个平行链的最新块（头部）。当一个中继链区块最终确定时，每个完成验证过程的平行链区块也将最终确定。这就是 Polkadot 为其平行链实现池化、共享安全性的方式！

您可以通过单击应用程序 UI 中的网络>平行链来查看已注册的平行链及其最新数据。


平行链摘要-screenshot.png
与你的平行链交互
启动和注册平行链的全部意义在于我们可以向平行链提交交易并与之交互。我们终于准备好在我们的平行链上提​​交外部数据了！

与应用程序 UI 连接
我们已经将 Apps UI 连接到中继链节点。现在我们还可以连接到平行链收集器。在新的浏览器窗口中打开另一个应用程序 UI 实例，并将其连接到适当的端点。如果到目前为止您已经按照本教程进行操作，您可以在以下位置连接到平行链节点：

https://polkadot.js.org/apps/?rpc=ws%3A%2F%2Flocalhost%3A8844#/

提交外在
您可以进行一些简单的代币转账，以确保平行链正常运行。您还可以通过转到Extrinsics页面，选择System托盘和remark外部来进行一些链上备注。

如果交易按预期进行，你就有了一个有效的平行链。

恭喜！

继续阅读以获取更多可选材料。

跨链消息传递（XCMP）
将平行链连接到公共中继链的定义特征是能够在所有连接的链之间进行通信。此功能领域处于最前沿的开发阶段，不包含在本教程中。

要了解有关 XCMP 的更多信息，请参阅XCMP 上的 Polkadot wiki。

链条清洗
您的唯一收集者是平行链区块链数据的唯一归宿，因为您的整个平行链网络上只有一个节点。中继链仅存储平行链标头信息。 如果平行链数据丢失，您将无法恢复该链。 但是，在测试中，您可能需要从头开始。

要从中继链中清除您的平行链数据，您需要注销并重新注册平行链收集器。只是清除中继和平行链并重新开始创世可能在测试中可能更容易。您可以通过运行以下命令清除所有链的所有链数据：
```shell
# Purge the collator(s)
./target/release/parachain-template-node purge-chain \
  --base-path <your collator DB path set above>

# Purge the validator(s)
polkadot purge-chain \
  --base-path <your relay chain DB path set above>
```
然后再次从空白处注册。

下一步
在Polkadot Wiki上了解有关整理者的更多信息。
向您的平行链网络添加更多平行链节点。
非常严格地测试您的本地平行链网络。
通过获取Rococo 测试网插槽将您的平行链连接到 rococo 。
请参阅将单链转换为平行链的指南，以将链的逻辑（非状态或运行链迁移）转换为平行链。