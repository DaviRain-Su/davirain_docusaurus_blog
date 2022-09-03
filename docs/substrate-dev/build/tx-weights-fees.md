# 交易、重量和费用

当交易执行或数据存储在链上时，活动会改变链的状态并消耗区块链资源。由于区块链可用的资源是有限的，因此管理链上操作如何使用它们很重要。除了在实际方面受到限制（例如存储容量）之外，区块链资源还代表了恶意用户的潜在攻击媒介。例如，恶意用户可能会尝试通过消息使网络过载，以阻止网络生成新块。为了保护区块链资源不被耗尽或过载，您需要管理它们的可用方式和使用方式。需要注意的资源包括：

内存使用情况
存储输入输出
计算
交易和区块大小
状态数据库大小
Substrate 为区块作者提供了多种方法来管理对资源的访问，并防止链的各个组件消耗过多的任何单一资源。阻止作者可用的两个最重要的机制是权重和交易费用。

权重用于管理验证块所需的时间。通常，权重用于表征在块主体中执行调用所花费的时间。通过控制块可以消耗的执行时间，权重设置了存储输入和输出以及计算的限制。

块允许的一些权重作为块的初始化和最终确定的一部分被消耗。权重也可用于执行强制性的固有外部调用。为了帮助确保块不会消耗太多执行时间——并防止恶意用户因不必要的调用而使系统过载——权重与交易费用结合使用。

交易费用为限制执行时间、计算和执行操作所需的调用次数提供了经济激励。交易费用还用于使区块链在经济上具有可持续性，因为它们通常应用于用户发起的交易，并在执行交易请求之前扣除。

费用如何计算
使用以下参数计算交易的最终费用：

基本费用：这是用户为交易支付的最低金额。它在运行时被声明为基本权重，并使用WeightToFee.
权重费用：与交易消耗的执行时间（输入输出和计算）成比例的费用。
长度费用：与交易的编码长度成比例的费用。
提示：增加事务优先级的可选提示，使其有更高的机会被包含在事务队列中。
基本费用和比例重量和长度费用构成包含费用。包含费用是交易被包含在一个区块中必须可用的最低费用。

使用交易支付托盘
交易支付托盘提供了计算包含费用的基本逻辑。

您还可以使用交易付款托盘来：

使用 .将重量值转换为基于货币类型的可扣除费用Config::WeightToFee。
通过定义一个乘数来更新下一个块的费用，基于上一个块结束时链的最终状态，使用Config::FeeMultiplierUpdate.
使用 . 管理交易费用的取款、退款和存款Config::OnChargeTransaction。
您可以在Transaction Payment文档中了解有关这些配置特征的更多信息。

您应该注意，交易费用在交易执行之前被提取。事务执行后，可以调整事务权重以反映事务使用的实际资源。如果交易使用的资源少于预期，则更正交易费用并存入调整后的交易费用。

仔细查看包含费用
计算最终费用的公式如下所示：
```
inclusion_fee = base_fee + length_fee + [targeted_fee_adjustment * weight_fee];
final_fee = inclusion_fee + tip;
```
在这个公式中，targeted_fee_adjustment是一个乘数，可以根据网络的拥塞情况调整最终费用。

从基础权重派生的base_fee包括签名验证等包含开销。
这length_fee是每字节的费用，乘以编码的外部长度。
费用使用weight_fee两个参数计算：

在ExtrinsicBaseWeight运行时声明并适用于所有外部变量的 。

`#[pallet::weight]`解释外部复杂性的注释。

要将权重转换为货币，运行时必须定义一个WeightToFee实现转换函数的结构体`Convert<Weight,Balance>`.

请注意，在调用外部发件人之前，会向外部发件人收取包含费用。即使交易在执行时失败，费用也会从发件人的余额中扣除。

余额不足的账户
如果一个账户没有足够的余额来支付包含费用并维持生存——即，足以支付包含费用并维持最低存续存款——那么你应该确保交易被取消，这样就不会扣除任何费用，并且事务没有开始执行。

Substrate 不强制执行此回滚行为。但是，这种情况很少发生，因为事务队列和块生成逻辑在将外部添加到块之前会执行检查以防止它发生。

费用乘数
包含费用公式始终会为相同的输入产生相同的费用。但是，权重可以是动态的，并且根据WeightToFee定义方式，最终费用可能包含一定程度的可变性。

为了解决这种可变性，交易支付托盘提供了FeeMultiplierUpdate可配置的参数。

默认更新函数受波卡网络启发，实现了有针对性的调整，其中定义了块权重的目标饱和水平。如果前一个区块更饱和，则费用会略有增加。同样，如果前一个区块的交易少于目标，则费用会减少少量。有关费用乘数调整的更多信息，请参阅Web3 研究页面。

有特殊要求的交易
包含费用必须在执行前可计算，因此只能代表固定的逻辑。一些交易需要使用其他策略来限制资源。例如：

债券是一种可能会在某些链上事件后退还或削减的费用。

例如，您可能希望要求用户放置债券以参与投票。如果选民企图恶意行为，债券可能会在公投结束时退还或被削减。

押金是以后可能退还的费用。

例如，您可能要求用户支付押金以执行使用存储的操作。如果后续操作释放了存储空间，则可以退还用户的押金。

销毁操作用于根据交易的内部逻辑支付交易费用。

例如，如果交易创建新的存储项目来支付增加的状态大小，则交易可能会烧掉发送方的资金。

限制使您能够对某些操作实施恒定或可配置的限制。

例如，默认的 Staking 托盘只允许提名人提名 16 名验证人，以限制验证人选举过程的复杂性。

需要注意的是，如果您向链查询交易费用，它只会返回包含费用。

默认重量注释
Substrate 中的所有可调度函数都必须指定权重。这样做的方法是使用基于注释的系统，该系统允许您组合数据库读/写权重的固定值和/或基于基准的固定值。最基本的示例如下所示：
```
#[pallet::weight(100_000)]
fn my_dispatchable() {
    // ...
}
```
请注意，它ExtrinsicBaseWeight会自动添加到声明的权重中，以考虑简单地将空的外部包含到块中的成本。

权重和数据库读/写操作
为了使权重注释独立于部署的数据库后端，它们被定义为一个常量，然后在表示可调度执行的数据库访问时在注释中使用：
```
#[pallet::weight(T::DbWeight::get().reads_writes(1, 2) + 20_000)]
fn my_dispatchable() {
    // ...
}
```
除了增加额外 20,000 次的其他操作之外，此可调度程序执行一次数据库读取和两次数据库写入。数据库访问通常是每次`#[pallet::storage]`访问块内声明的值时。但是，只计算唯一访问次数，因为在访问一个值后，它会被缓存，再次访问它不会导致数据库操作。那是：

相同值的多次读取计为一次读取。
相同值的多次写入算作一次写入。
多次读取相同的值，然后写入该值，算作一次读取和一次写入。
写后跟只读算作一次写。
调度班
调度分为三类：

Normal
Operational
Mandatory
如果分派未定义为Operational或Mandatory在权重注释中，则分派Normal默认标识为。您可以指定可调度使用另一个类，如下所示：
```
#[pallet::weight((100_000, DispatchClass::Operational))]fn my_dispatchable() {
    // ...
}
```
此元组表示法还允许您指定最终参数，该参数确定是否根据带注释的权重向用户收费。如果您未另行指定，Pays::Yes则假定：
```
#[pallet::weight(100_000, DispatchClass::Normal, Pays::No)]
fn my_dispatchable() {
    // ...
}
```
正常发货
此类中的调度表示正常的用户触发事务。这些类型的调度只消耗一个块的总重量限制的一部分。有关可用于正常调度的块的最大部分的信息，请参阅AvailableBlockRatio。正常调度被发送到事务池。

业务调度
与代表网络能力使用的正常调度不同，运营调度是提供网络能力的调度。操作调度可以消耗一个块的整个重量限制。他们不受AvailableBlockRatio. 此类中的调度具有最高优先级，并且免于支付length_fee.

强制派遣
强制调度包含在块中，即使它们导致块超过其重量限制。您只能对区块作者提交的固有交易使用强制调度类。此调度类旨在表示属于块验证过程的一部分的功能。因为无论函数权重如何，这些调度总是包含在一个块中，所以验证过程防止恶意节点滥用函数来制作有效但不可能很重的块是至关重要的。您通常可以通过确保：

执行的操作总是很轻松。
该操作只能包含在一个块中一次。
为了使恶意节点更难滥用强制调度，它们不能包含在返回错误的块中。这个调度类的存在是为了假设允许创建超重块比根本不允许创建任何块更好。

动态权重
除了纯粹的固定权重和常量外，权重计算还可以考虑可调度的输入参数。权重应该可以通过一些基本算术从输入参数中轻松计算：
```
#[pallet::weight(FunctionOf(
  |args: (&Vec<User>,)| args.0.len().saturating_mul(10_000),
  DispatchClass::Normal,
  Pays::Yes,
))]
fn handle_users(origin, calls: Vec<User>) {
    // Do something per user
}
```
发货后重量修正
根据执行逻辑，可调度函数消耗的权重可能比调度前规定的要少。为了更正重量，该函数声明了不同的返回类型并返回其实际重量：
```
#[pallet::weight(10_000 + 500_000_000)]
fn expensive_or_cheap(input: u64) -> DispatchResultWithPostInfo {
    let was_heavy = do_calculation(input);

    if (was_heavy) {
        // None means "no correction" from the weight annotation.
        Ok(None.into())
    } else {
        // Return the actual weight consumed.
        Ok(Some(10_000).into())
    }
}
```

定制费用
您还可以通过自定义权重函数或包含费用函数定义自定义费用系统。

自定义权重
您可以使用权重模块创建自定义权重计算类型，而不是使用默认权重注释。自定义权重计算类型必须实现以下特征：

`WeighData<T>`来确定发货的重量。
`ClassifyDispatch<T>`确定调度的类别。
`PaysFee<T>`确定快件的发件人是否支付费用。
然后，Substrate 将三个特征的输出信息捆绑到DispatchInfo结构中，并通过实现GetDispatchInfo所有Call变体和不透明的外部类型来提供它。这由系统和执行模块在内部使用。

ClassifyDispatch, WeighData, 和PaysFee是通用的 over T，它被解析为除原点之外的所有调度参数的元组。下面的示例说明了struct计算权重的方法，m * len(args)其中m是给定的乘数，args是所有调度参数的连接元组。在这个例子中，调度类是Operational如果交易的参数长度超过 100 字节，并且如果编码长度大于 10 字节将支付费用。
```
struct LenWeight(u32);
impl<T> WeighData<T> for LenWeight {
    fn weigh_data(&self, target: T) -> Weight {
        let multiplier = self.0;
        let encoded_len = target.encode().len() as u32;
        multiplier * encoded_len
    }
}

impl<T> ClassifyDispatch<T> for LenWeight {
    fn classify_dispatch(&self, target: T) -> DispatchClass {
        let encoded_len = target.encode().len() as u32;
        if encoded_len > 100 {
            DispatchClass::Operational
        } else {
            DispatchClass::Normal
        }
    }
}

impl<T> PaysFee<T> {
    fn pays_fee(&self, target: T) -> Pays {
        let encoded_len = target.encode().len() as u32;
        if encoded_len > 10 {
            Pays::Yes
        } else {
            Pays::No
        }
    }
}
```
权重计算器函数也可以强制转换为参数的最终类型，而不是将其定义为可以编码的模糊类型。代码大致如下所示：
```
struct CustomWeight;
impl WeighData<(&u32, &u64)> for CustomWeight {
    fn weigh_data(&self, target: (&u32, &u64)) -> Weight {
        ...
    }
}

// given a dispatch:
#[pallet::call]
impl<T: Config<I>, I: 'static> Pallet<T, I> {
    #[pallet::weight(CustomWeight)]
    fn foo(a: u32, b: u64) { ... }
}
```
在此示例中，CustomWeight只能与具有特定签名 的调度一起使用，(u32, u64)而LenWeight可以与任何东西一起使用，因为没有关于 的任何假设`<T>`。

自定义包含费用
以下示例说明了如何自定义包含费用。您必须在相应模块中配置适当的关联类型。
```
// Assume this is the balance type
type Balance = u64;

// Assume we want all the weights to have a `100 + 2 * w` conversion to fees
struct CustomWeightToFee;
impl Convert<Weight, Balance> for CustomWeightToFee {
    fn convert(w: Weight) -> Balance {
        let a = Balance::from(100);
        let b = Balance::from(2);
        let w = Balance::from(w);
        a + b * w
    }
}

parameter_types! {
    pub const ExtrinsicBaseWeight: Weight = 10_000_000;
}

impl frame_system::Config for Runtime {
    type ExtrinsicBaseWeight = ExtrinsicBaseWeight;
}

parameter_types! {
    pub const TransactionByteFee: Balance = 10;
}

impl transaction_payment::Config {
    type TransactionByteFee = TransactionByteFee;
    type WeightToFee = CustomWeightToFee;
    type FeeMultiplierUpdate = TargetedFeeAdjustment<TargetBlockFullness>;
}

struct TargetedFeeAdjustment<T>(sp_std::marker::PhantomData<T>);
impl<T: Get<Perquintill>> Convert<Fixed128, Fixed128> for TargetedFeeAdjustment<T> {
    fn convert(multiplier: Fixed128) -> Fixed128 {
        // Don't change anything. Put any fee update info here.
        multiplier
    }
}
```

下一步去哪里
您现在知道了权重系统是什么，它如何影响交易费用计算，以及如何为您的可调度调用指定权重。下一步是确定正确的权重以说明您的可调度执行的操作。您可以使用 Substrate基准测试函数和frame-benchmarking调用来使用不同的参数测试您的函数，并在最坏的情况下凭经验确定正确的权重。

基准
签名扩展
示例托盘的自定义重量
Web3 基金会研究