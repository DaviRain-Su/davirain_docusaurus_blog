# 特权呼叫和来源

可调度函数使用运行时源来检查调用的来源。

原始起源
Substrate 定义了三个可在运行时托盘中使用的原始来源：
```
pub enum RawOrigin<AccountId> {
	Root,
	Signed(AccountId),
	None,
}
```
Root：系统级别的起源。这是最高权限级别，可以认为是运行时起源的超级用户。
签名：交易来源。这是由某个链上账户的私钥签名的，包括签名者的账户标识符。这允许运行时验证调度的来源，并随后向关联帐户收取交易费用。
无：缺乏起源。这需要验证者同意或由要包含的模块验证。这种起源类型本质上更复杂，因为它旨在绕过某些运行时机制。这种原始类型的一个示例用例是允许验证器将数据直接插入到块中。
原始呼叫
您可以在运行时中使用任何来源构造调用。例如：
```
// Root
proposal.dispatch(system::RawOrigin::Root.into())

// Signed
proposal.dispatch(system::RawOrigin::Signed(who).into())

// None
proposal.dispatch(system::RawOrigin::None.into())
```
您可以查看Sudo 模块的源代码以了解其实际实现。

自定义来源
除了三种核心来源类型之外，运行时开发人员还可以定义自定义来源。这些可以用作运行时中特定模块的函数内部的授权检查，或者围绕运行时请求的源定义自定义访问控制逻辑。

自定义源允许运行时开发人员根据其运行时逻辑指定有效的源。例如，可能需要将某些功能的访问限制为特殊的自定义来源，并仅授权来自集体成员的调度调用。使用自定义来源的优势在于，它为运行时开发人员提供了一种配置特权访问的方式，而不是对运行时的调度调用。

下一步
学到更多
了解如何在`#[pallet::call]`宏中使用原点。
例子
查看Sudo 托盘以了解它如何允许用户使用Root和Signed来源进行呼叫。
查看时间戳托盘以了解它如何验证带有None来源的呼叫。
查看集体托盘以了解它如何构建自定义Member来源。
查看我们创建和使用自定义来源的方法。
参考
访问 RawOriginenum的参考文档。