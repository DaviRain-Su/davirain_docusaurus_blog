# 交易和区块基础

在本文中，您将了解可以创建的不同类型的事务以及如何在运行时中使用它们。从广义上讲，交易决定了进入区块链中的块的数据。通过了解如何使用不同的事务类型，您将更好地准备好选择适合您需要的类型。

## 什么是交易？
通常，交易提供了一种机制来更改可以包含在块中的状态。Substrate 中有三种不同的事务类型：

- 签署的交易
- 未签名的交易
- 固有事务

在 Substrate 中，所有三种事务类型通常被更广泛地称为外部事务。术语外部通常用于表示源自运行时之外的任何信息。

但是，出于实际目的，独立考虑每种事务类型并确定每种类型最适用的场景更为有用。

### 签署的交易
签名交易必须包括发送入站请求以执行某些运行时调用的帐户的签名。通常，使用提交请求的帐户的私钥对请求进行签名。在大多数情况下，提交请求的账户也会支付一笔交易费用。但是，交易费用和交易处理的其他元素取决于运行时逻辑的定义方式。

签名交易是最常见的交易类型。例如，假设您有一个带有一定数量令牌的帐户。如果您想将代币转移给 Alice，您可以调用`pallet_balances::Call::transfer`余额托盘中的函数。因为您的帐户用于进行此调用，所以您的帐户密钥用于签署交易。作为请求者，您通常负责支付费用以处理您的请求。或者，您还可以提示区块作者以赋予您的交易更高的优先级。

### 未签名的交易
未签名的交易不需要签名，也不包含有关谁提交交易的任何信息。

对于未签名的交易，没有经济上的威慑力来防止垃圾邮件或重放攻击。您必须定义验证未签名交易的条件以及保护网络免受滥用和攻击所需的逻辑。由于未签名交易需要自定义验证，因此此交易类型比已签名交易消耗更多资源。

该`pallet_im_online::Call::heartbeat`函数使用未签名的交易来使验证节点能够向网络发送信号以指示节点在线。此函数只能由在网络中注册为验证者的节点调用。该函数包括验证节点是否为验证者的内部逻辑，允许节点使用未签名的交易调用该函数以避免支付任何费用。

### 固有事务
固有交易（有时称为固有交易）是一种特殊类型的未签名交易。通过这种类型的交易，区块创作节点可以直接将信息添加到区块中。固有交易只能由调用它们的块创作节点插入到块中。通常，这种类型的事务不会传到其他节点或存储在事务队列中。假设使用固有事务插入的数据是有效的，无需特定验证。

例如，如果区块创作节点将时间戳插入区块，则无法证明时间戳是准确的。相反，验证者可能会根据时间戳是否在他们自己的系统时钟的某个可接受范围内来接受或拒绝该块。

例如，该`pallet_timestamp::Call::now`函数使块创作节点能够在节点生成的每个块中插入当前时间戳。类似地，该`paras_inherent::Call::enter`函数使平行链收集者节点能够向其中继链发送中继链期望的验证数据。

## 什么是block？
在 Substrate 中，一个块由一个标头和一个交易数组组成。标头包含以下属性：

- 块高度
- 父哈希
- 事务根
- 状态根
- 消化

所有事务都捆绑在一起，作为一个系列，按照运行时中的定义执行。您将在[事务生命周期](https://docs.substrate.io/fundamentals/transaction-lifecycle/)中了解有关事务排序的更多信息。交易根是本系列的加密摘要。此加密摘要有两个目的：
- 它可以防止在构建和分发标头之后对一系列事务进行任何更改。
- 它使轻客户端能够简洁地验证任何给定的交易是否存在于仅给定头信息的块中。

## 下一步去哪里

现在您已熟悉交易类型和构成区块的信息，请探索以下主题以了解更多信息。

- [交易生命周期](https://docs.substrate.io/fundamentals/transaction-lifecycle/)
- [状态转换和存储](https://docs.substrate.io/fundamentals/state-transitions-and-storage/)
- [交易、重量和费用](https://docs.substrate.io/build/tx-weights-fees/)
- [交易格式](https://docs.substrate.io/reference/transaction-format/)
- [block参考](https://paritytech.github.io/substrate/master/sp_runtime/traits/trait.Block.html)