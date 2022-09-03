# 交易格式

本文详细介绍了 Substrate 中已签名和未签名交易的数据结构。这对于了解交易池如何检查传入交易特别有用。平行链建设者会发现这对于自定义他们的交易格式以及编写需要遵守所选格式的客户端应用程序很有用。

外部通常包含一个签名，一些数据来描述外部是否通过了一些有效性检查，以及对托盘的引用和它打算用于的调用。这种格式为应用程序提供了一种方法，以确保满足并正确构建外部要求。

未检查：签名的交易需要一些验证检查才能被交易池接受。任何未经检查的外部都包含正在发送的数据的签名以及一些额外的数据。
已检查：根据定义不需要签名验证的固有外在因素。相反，它们携带有关外部数据来源和一些额外数据的信息。
不透明：用于外部尚未提交格式但仍可解码的情况。
额外数据可以是附加到交易或固有的有用的任何额外信息。例如，交易的随机数，区块作者的提示，或外部有效的时间。此信息由专门的扩展提供，可帮助确定外部数据的有效性和顺序，然后再将它们包含在一个块中。

一个签名的交易可能是这样构造的：
```
node_runtime::UncheckedExtrinsic::new_signed(
		function.clone(),                                      // some call
		sp_runtime::AccountId32::from(sender.public()).into(), // some sending account
		node_runtime::Signature::Sr25519(signature.clone()),   // the account's signature
		extra.clone(),                                         // the signed extensions
	)
```
交易是如何构建的
Substrate 通用地定义了它的事务格式，以允许开发人员实现自定义方式来定义有效的事务。然而，在使用 FRAME 构建的运行时（假设事务版本 4）中，必须通过提交以下编码数据来构建事务：
```
<signing account ID> + <signature> + <additional data>
```
提交签名交易时，通过签名构造签名：

实际调用，包括：

托盘的索引。
托盘中函数调用的索引。
目标函数调用所需的参数。
一些额外的信息，由交易的签名扩展验证：

这个事务的时代是什么，即这个调用在被丢弃之前应该在事务池中持续多久？
随机数，即该帐户发生了多少先前的交易？这有助于防止重放攻击或意外重复提交。
支付给区块生产者的小费金额，以帮助激励它将此交易包含在区块中。
然后，需要一些不属于已签名内容的附加数据，其中包括：

规范版本和交易版本。这可确保将事务提交到兼容的运行时。
创世哈希。这确保了交易对正确的链有效。
块哈希。这对应于检查点块的哈希，它使签名能够通过检查时代信息提供的块号来验证交易没有在错误的分叉上执行。
然后对 SCALE 编码的数据进行签名（即 ( call, extra, additional)），并以正确的顺序附加签名、额外数据和调用数据并进行 SCALE 编码，准备发送到将验证签名有效负载的节点。如果要签名的有效载荷长于 256 字节，则在签名之前对其进行哈希处理，以确保签名数据的大小不会超过特定大小。

这个过程可以分为以下几个步骤：

构造未签名的有效载荷。
创建签名有效负载。
签署有效载荷。
序列化签名的有效负载。
提交序列化交易。
就在被十六进制编码之前，外部编码被编码为以下字节序列：
```
[ 1 ] + [ 2 ] + [ 3 ] + [ 4 ]
```
在哪里：

[1]包含以下所有数据的紧凑编码长度（以字节为单位）。了解如何使用SCALE进行紧凑编码。
[2]是一个u8包含 1 个字节来指示事务是有符号还是无符号（1 位），以及编码的事务版本 ID（7 位）。
[3]如果存在签名，则此字段包含帐户 ID、SR25519 签名和一些额外数据。如果无符号，此字段包含 0 个字节。
[4]是编码的呼叫数据。这包括 1 个字节表示要调用的托盘，1 个字节表示在该托盘中进行的调用，然后是编码该调用预期的参数所需的字节数。
应用程序知道如何正确构建事务的方式由元数据接口提供。应用程序将知道如何使用元数据类型和事务格式正确编码事务。如果不需要对调用进行签名，则其中的第一位[2]将为 0，因此应用程序将知道不要尝试解码签名。

Polkadot JS 应用示例：

在这里，我们演示了一个详细的手动外部构造和提交从 Bob 到 Dave 的余额转移：Bob 将42 UNITs 发送给 Dave。

以模式启动节点模板--dev（请参阅快速入门指南了解如何设置）
导航到https://polkadot.js.org/apps/?rpc=ws%3A%2F%2F127.0.0.1%3A9944#/extrinsics
设置Bob为发件人并选择balances托盘和transfer(dest, value)呼叫
设置MultiAddress为Id和AccountID_ Dave_dest
设置value为42000000000000（这在节点模板的链规范42 UNIT中定义）
单击Submit Transaction按钮（右下角）并取消选中 签名并提交以生成默认的签名交易nonce = 0并Lifetime = 64进行检查

Bob to Dave `transfer` 42 unit
编码呼叫数据：0x050300306721211d5404bd9da88e0204360a1a9ab8b87c66c1bc2fcdd37f3c2222cc200b00a014e33226
编码调用哈希：0x26c333c22ec93ac431ee348168530b7d77e85d766f130af60890c0fd6ab20d5b
生成的签名交易调用哈希：0x450284008eaf04151687736326c9fea17e25fc5287613693c912909cb226aa4794f26a48018eeaeb6a3496444c08b5c3e10e0c5f94776774591504ef4ef26e3873799831285a1a7cbd8ba2babe6fba94ea3585bf20e46c80ce7baeb25b149529ece931478c45020c00050000306721211d5404bd9da88e0204360a1a9ab8b87c66c1bc2fcdd37f3c2222cc200b00a014e33226

Bob to Dave `transfer` 42 unit signed hash
在这里您可以复制Signed transaction数据以直接通过 RPC 提交，或在Developer-> Extrinsics->Decode部分进行检查。我们现在将使用此窗口提交交易并观察结果。

关闭authorize transaction卡片
单击Submit Transaction按钮（右下角）并保持签名和提交选中
导航到Developer->RPC Calls选项卡
在 RPC 选项卡中，您应该会看到author_submitAndWatchExtrinsic调用结果类似于：
```
{
  dispatchInfo: {
    weight: 159,200,000
    class: Normal
    paysFee: Yes
  }
  events: [
    {
      phase: {
        ApplyExtrinsic: 1
      }
      event: {
        method: Withdraw
        section: balances
        index: 0x0508
        data: {
          who: 5FHneW46xGXgs5mUiveU4sbTyGBzmstUspZC92UhjJM694ty
          amount: 125,000,147
        }
      }
      topics: []
    }
    {
      phase: {
        ApplyExtrinsic: 1
      }
      event: {
        method: NewAccount
        section: system
        index: 0x0003
        data: {
          account: 5DAAnrj7VHTznn2AWBemMuyBwZWs6FNFjdyVXUeYum3PTXFy
        }
      }
      topics: []
    }
    {
      phase: {
        ApplyExtrinsic: 1
      }
      event: {
        method: Endowed
        section: balances
        index: 0x0500
        data: {
          account: 5DAAnrj7VHTznn2AWBemMuyBwZWs6FNFjdyVXUeYum3PTXFy
          freeBalance: 42,000,000,000,000
        }
      }
      topics: []
    }
    {
      phase: {
        ApplyExtrinsic: 1
      }
      event: {
        method: Transfer
        section: balances
        index: 0x0502
        data: {
          from: 5FHneW46xGXgs5mUiveU4sbTyGBzmstUspZC92UhjJM694ty
          to: 5DAAnrj7VHTznn2AWBemMuyBwZWs6FNFjdyVXUeYum3PTXFy
          amount: 42,000,000,000,000
        }
      }
      topics: []
    }
    {
      phase: {
        ApplyExtrinsic: 1
      }
      event: {
        method: ExtrinsicSuccess
        section: system
        index: 0x0000
        data: {
          dispatchInfo: {
            weight: 159,200,000
            class: Normal
            paysFee: Yes
          }
        }
      }
      topics: []
    }
  ]
  status: {
    InBlock: 0x501c8f15883bb2b686fb5ea1ca35e99dace8bd6216bfc571a31d7088aea000f7
  }
}
```
导航到Network->Explorer选项卡
点击卡片右上角的打开balances.Transfer外部详细信息`<block number>-<extrinsic number>`
检查交易的链上细节
```
Bob to Dave `transfer` 42 unit result
```
点击#/extrinsics/decode/0x....链接打开上述Signed transaction数据的解码细节，注意它与我们提交的相同。因此，在提交外部数据之前或之后，此工具可用于解码和内省交易调用数据。

签名的扩展
Substrate 提供签名扩展SignedExtension的概念，以使用trait提供的附加数据扩展外部。

事务队列定期调用已签名的扩展，以在事务被放入就绪队列之前继续检查事务是否有效。这是验证交易不会在区块中失败的有用保护措施。它们通常用于强制执行验证逻辑，以保护交易池免受垃圾邮件和重放攻击。

在 FRAME 中，签名的扩展默认可以包含以下任何类型：

AccountId: 对发送者的身份进行编码。
Call：对要调度的托盘调用进行编码。该数据用于计算交易费用。
AdditionalSigned：处理任何附加数据以进入已签名的有效负载。这使得在分派事务之前附加任何自定义逻辑成为可能。
Pre: 对可以在调用调度之前传递到调度之后的信息进行编码。
FRAME 的系统托盘提供了一套开箱即用的实用工具。SignedExtensions

实际例子
验证交易的一个重要的签名扩展是CheckSpecVersion.
它为发送方提供了一种方法，可以将规范版本作为附加到交易的签名有效负载提供。由于规范版本在运行时已知，签名的扩展可以执行简单的检查来验证规范版本是否匹配。如果他们不这样做，则交易在放入交易池之前失败。

其他示例包括用于计算事务优先级的签名扩展。这些是：

CheckWeight0：为所有调度类设置优先级的值。
ChargeTransactionPayment：计算总体优先级，相应地修改优先级值。
优先级取决于调度类别以及发送者愿意支付的每重量小费或每长度小费（以更有限的为准）的数量。没有提示的事务使用最小提示值1进行优先级计算，以确保并非所有事务最终都具有 的优先级0。这样做的结果是较小的交易比较大的交易更受欢迎。

下一步去哪里
现在您已经了解了交易是如何构建的，您可能想要查看它们如何从交易池进展到运行时并添加到块中，或者如何使用使您能够离线提交交易或使用 REST API 的工具。

交易生命周期
交易、重量和费用
用于离线交易的 tx-wrapper
用于基于 REST 的事务的sidecar