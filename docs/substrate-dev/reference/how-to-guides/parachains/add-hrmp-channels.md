# 添加 HRMP 频道
在本指南中，您将学习如何向平行链 B 发出请求以接受具有平行链 A 的通道。在Polkadot wiki中阅读有关 HRMP 通道的更多信息。

通道是单向的

一般流程是有兴趣打开通道的平行链之一必须向它希望打开通道的平行链发出请求。然后，另一个平行链需要接受这个打开通道的请求，这意味着通道默认是单向的。对于双向通信，我们需要以相反的方式打开另一个通道。

频道只有在接收者确认后才能打开，并且只能在会话更改时打开。

出于本指南的目的，我们假设我们有：

中继链
带有 ParaId 2000 的平行链 A
具有 ParaId 3000 的平行链 B
平行链 A 动作
在中继链上，准备以下调用：

hrmp.hrmpInitOpenChannel(
   recipient: 3000                    //the other parachain you want to open the channel with
   proposedMaxCapacity: 1000          // specifies how many messages can be in the channel at once
   proposed_max_message_size: 102400  //specifies the maximum size of the messages
)
请注意，这些数字受中继链的配置限制。

设置好所需参数后，保存编码后的调用数据。例如，在 Rococo 中此通话的编码通话数据为：0x1700b80b0000e803000000900100.

在平行链上，现在我们必须编写一条 XCM 消息来通知中继链我们要使用平行链 B 打开一个通道。

该消息应类似于以下内容（使用上一步中的编码呼叫数据）：

polkadotXcm.send(
   dest: V1
       parents: 1
       interior: Here
   message: V2
       XcmV2Instruction: WithdrawAsset
           id: Concrete
               parents: 0
               interior: Here
           fun: Fungible
               Fungible: 1_000_000_000_000
       XcmV2Instruction: BuyExecution
           id: Concrete
               parents: 0
               interior: Here
           fun: Fungible
               Fungible: 1_000_000_000_000
           weightLimit: Unlimited
       XcmV2Instruction: Transact
           originType: Native
           requireWeightAtMost: 4_000_000_000
               encoded: 0x1700b80b0000e803000000900100 // our hrmpInitOpenChannel encoded call data

)
请记住，您应该在编写消息时考虑到活动的 XCM 配置，这只是一个示例。

平行链 B 动作
到目前为止，平行链 A 已经完成了它的工作：向平行链 B 发送请求。现在这个请求必须被平行链 B 接受。

这个过程会不断重复，但这次我们将不得不编码一个不同的调用。

在中继链上，提交以下外部代码并保存编码后的调用数据：

hrmp.hrmpAcceptOpenChannel(
   sender: 2000
)
Rococo 上的编码呼叫数据为：0x1701d0070000

现在在平行链 B 上，我们可以使用我们最后一条 XCM 消息作为基础来编写这条消息，并将其作为中继链上的调用执行（使用上一步中的编码调用数据）。

polkadotXcm.send(
   dest: V1
       parents: 1
       interior: Here
   message: V2
       XcmV2Instruction: WithdrawAsset
           id: Concrete
               parents: 0
               interior: Here
           fun: Fungible
               Fungible: 1_000_000_000_000
       XcmV2Instruction: BuyExecution
           id: Concrete
               parents: 0
               interior: Here
           fun: Fungible
               Fungible: 1_000_000_000_000
           weightLimit: Unlimited
       XcmV2Instruction: Transact
           originType: Native
           requireWeightAtMost: 4_000_000_000
               encoded: 0x1701d0070000 // our hrmpAcceptOpenChannel encoded call data

)
请记住，您应该在编写消息时考虑到活动的 XCM 配置，这只是一个示例。

就是这样 - 通道已被接受并将保持打开状态，这样从平行链 A 到平行链 B 的通信现在可以流动。要使其成为双向通道，您需要打开另一个通道，从平行链 B 到平行链 A。您可以按照本指南中的相同步骤从 B 发出请求并从 A 接受请求来完成此操作。

