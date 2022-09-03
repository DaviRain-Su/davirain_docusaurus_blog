# 连接到中继链
本指南逐步介绍了如何将平行链连接到中继链。

本指南说明：

如何获得一个ParaID
如何注册平行链
如何获得平行链插槽
启动平行链需要一系列步骤，以确保一旦中继链上的插槽得到保护，中继链确切地知道平行链的运行时逻辑是什么。为了实现这一点，您需要之前已成功生成ParaIDgenesis 状态和 Wasm 运行时 blob。成功注册您的平行链后，您将能够获得一个平行链插槽（虽然在测试中sudo，并通过拍卖和众贷进行生产）并开始生产区块。

如果您是平行链开发新手，那么准备本地平行链测试网是最好的起点。本指南是一个快速参考，省略了执行这些步骤时需要考虑的重要细节。

预订一个ParaID
首先，您需要ParaID为特定中继链执行引用平行链/平行线程的任何操作。例如，用于提供 Wasm blob 或创世状态、为 XCM 创建通往其他平行链的通道、启动众筹等。

导航到应用程序 UI 中的Network->Parachains部分。如果在端口 9944 上运行带有 ws 的节点，这将是： https ://polkadot.js.org/apps/?rpc=ws%3A%2F%2F127.0.0.1%3A9944#/parachains/parathreads

预留一个“ParaID”
转到Parathread选项卡并单击+ParaID（平行链始终以带有 的平行线程开始ParaID）
预留ParaID. 此操作需要特定于中继链的存款。
记下您的ParaID注册帐户。这ParaID是您要连接的中继链以及保留它的帐户所独有的。该标识符在后面的步骤中是必需的，一般不能在不同的中继链之间复用。

在接下来的步骤中，假设您使用 Cumulus，因此具有parachain-collator为您的收集器节点生成的二进制文件的正确命令。

自定义平行链规范
您的平行链必须ParaID在您的链规范中配置正确。请参阅配置自定义链规范的操作指南以获取更深入的说明以生成普通规范、修改它并生成原始规范。

我们首先生成一个普通的规范：
```
# Assumes that `rococo-local` is in `node/chan_spec.rs` as the relay you registered with
./target/release/parachain-collator build-spec --disable-default-bootnode > rococo-local-parachain-plain.json
```
默认ParaID来自1000Cumulus，因此您必须根据上述保留为您的平行链正确设置它ParaID。假设您保留ParaID的是2000，您将打开rococo-local-parachain-plain.json并修改两个字段：
```
  // --snip--
  "para_id": <your `ParaID`> , // <--- = your already registered ID
  // --snip--
      "parachainInfo": {
        "parachainId": <your `ParaID`> // <--- = your already registered ID
      },
  // --snip--
```
然后生成从修改后的普通链规范派生的原始链规范：
```
./target/release/parachain-collator build-spec --chain parachain-plain.json --raw --disable-default-bootnode > parachain-raw.json
```
保存和分发您的原始规范
如果您打算让其他人连接到您的网络，他们必须为您的网络生成一次相关的链规范并分发给您的对等方。他们自己无法可靠地生产，需要从单一来源获取。这源于 Wasm 运行时编译方式的不确定性问题。

链规范通常存在于一个/chain-specs文件夹中，该文件夹发布在您节点的代码库中供其他人使用。例如：

Polkadot 将这些中继链规格 包括在node/service/chain-specs
Cumulus 将这些平行链规范 包含在chain-specs
在继续之前将此原始链规范提交到您的源中是一种很好的做法。

获取 Wasm 运行时验证函数
中继链还需要特定于平行链的运行时验证逻辑来​​验证平行链块。平行链收集器节点也有一个命令来生成这个 Wasm blob：
```
./target/release/parachain-collator export-genesis-wasm --chain parachain-raw.json > para-wasm
```
生成平行链创世状态
要注册平行链，中继链需要知道平行链的创世状态。整理者节点可以将该状态导出到文件。转到您的平行链模板文件夹，以下命令将创建一个文件，其中包含平行链的整个创世状态，十六进制编码：
```
./target/release/parachain-collator export-genesis-state --chain parachain-raw.json > para-genesis
```
启动整理器
请注意，我们需要提供与目标中继链相同的中继链规格！--在类似于以下的命令中将其替换为分隔符后的正确文件：
```
parachain-collator \
--alice \
--collator \
--force-authoring \
--chain parachain-raw.json \
--base-path /tmp/parachain/alice \
--port 40333 \
--ws-port 8844 \
-- \
--execution wasm \
--chain <relay chain spec json> \
--port 30343 \
--ws-port 9977
```
您应该看到您的收集器正在运行并与已经运行的中继链节点对等。它还没有开始创作平行链区块。当收集人实际在中继链上注册时，将开始创作。

平行链注册
根据您的目标中继链和那里的权限，您可以选择注册。通常，您将使用平行链拍卖和众贷sudo进行测试和生产使用。

本指南目前仅涵盖 sudo 测试用例。

注册押金计算
或者，您可以在函数中计算 Polkadot 运行时的存款计算的确切公式：
```
pub const fn deposit(items: u32, bytes: u32) -> Balance {}
```
它位于`Polkadotruntime/<RELAY CHAIN>/src/constants.rs`的文件中。

注册使用sudo
我们已经启动了中继链，并且我们的平行链整理器已经准备就绪。现在我们必须在中继链上注册平行链。在生产网络中，这通常会在 Polkadot 和 Kusama 上完成，但在本教程中，我们将使用sudocall 来完成。

选项1：paraSudoWrapper.sudoScheduleParaInitialize
转到Polkadot 应用程序 UI，连接到您的中继链。
通过转到Developer->sudo页面在中继链上执行 sudo extrinsic。
选择paraSudoWrapper->sudoScheduleParaInitialize(id, genesis)作为外部类型，如下所示。

平行链注册-sudo.png
在外部参数中，指定ParaID要使用的正确和文件。
如果成功，此调度将发出sudo.Sudid事件，可在中继链浏览器页面中查看。

选项 2：slots.forceLease
转到Polkadot 应用程序 UI，连接到您的中继链。
通过转到Developer->sudo页面在中继链上执行 sudo extrinsic。
选择slots->forceLease(para, leaser, amount, period_begin, period_end)作为外部类型，如下所示。

forceLease.png
请务必将 设置为period_begin您要开始使用的插槽。例如，如果您在测试环境中从头开始，则开始时间段很可能是已经处于活动状态的 slot 0。通常，您应该将 设置period_end为超出您为测试平行链预留的时间。但是，如果您想测试入职和离职周期，您应该选择对ParaID. 在完全载入和块生产开始后，您应该看到：


平行链主动租赁.png
区块生产和最终确定
一旦注册成功并且新的中继链时代已经开始，整理者应该开始生产平行链块（又名整理） ！

可能还要等一下！在等待新纪元开始时要有耐心。

Network > Parachains您可以在应用程序 UI的选项卡上跟踪注册了哪些平行链以及它们的最新头部数据。


平行链摘要-screenshot.png
例子
准备本地平行链测试网教程
