# 运行时存储

运行时存储允许您在区块链中存储数据，这些数据在块之间持久保存，并且可以从运行时逻辑中访问。存储应该是区块链运行时开发人员最关心的问题之一。设计良好的存储系统可以减少网络中节点的负载，从而最终降低区块链参与者的开销成本。换句话说，区块链运行时存储的基本原则是尽量减少其使用。

Substrate 公开了一组分层的、模块化的存储 API，允许运行时开发人员做出最适合他们的存储决策。本文档旨在提供有关 Substrate 的运行时存储接口的信息和最佳实践。

存储物品
在 Substrate 中，任何托盘都可以引入新的存储项目，这些项目将成为区块链状态的一部分。这些存储项可以是简单的单值项，也可以是更复杂的存储映射。您选择实现的存储项类型完全取决于它们在运行时逻辑中的预期角色。

FRAME 的Storage模块让运行时开发人员可以访问 Substrate 的灵活存储 API，它可以支持任何可由SCALE 编解码器编码的值。这些包括：

存储值- 用于存储任何单个值，例如u64.
存储映射- 用于存储键值映射，例如帐户到余额。
Storage Double Map - 用作具有两个键的存储映射的实现，以提供有效删除具有公共第一个键的所有条目的能力。
Storage N Map - 用于存储任意数量的key的映射，可以作为基础构建Triple Storage Map、Quadruple Storage Map等。
存储价值
这种类型的存储项应该用于运行时视为单个单元的值。这可以是单个原始值、单个struct或单个相关项的集合。如果存储项用于存储项列表，则运行时开发人员应该注意他们使用的列表的大小。大型列表会产生存储成本，就像 large structs. 此外，在您的运行时迭代一个大列表可能会导致超过块生产时间。如果主权链发生这种情况，区块链将减速。如果平行链发生这种情况，区块链将停止生成块并停止运行。

尽管将相关项目包装在共享struct中是减少存储读取次数的极好方法，但在某些时候，对象的大小将开始产生可能超过存储读取优化的成本。阅读基准测试以了解如何优化执行时间。

有关 Storage Value 公开的方法的完整列表，请参阅 Storage Value 文档。

存储图
地图数据结构非常适合管理其元素将被随机访问的项目集，而不是按顺序对它们进行整体迭代。Substrate 中的存储映射被实现为键值映射，它提供与传统哈希映射类似的接口 以实现随机查找。为了给运行时工程师更多的控制权，Substrate 允许开发人员选择最适合他们用例的哈希算法来生成地图的键。这在关于散列算法的部分中进行了介绍。

有关 Storage Map 公开的方法的完整列表，请参阅 Storage Map 文档。

双存储图
双存储映射与单存储映射非常相似，只是它们包含两个键，这对于使用公共键查询值很有用。

N 存储图
N Storage Maps 也非常类似于它的兄弟，即 Storage Maps 和 Double Storage Maps，但能够保存任意数量的键。

要在 FRAMEv2 中指定 N 存储映射中的键，NMapKey在声明StorageNMap.

有关使用 N Storage Map 的语法的更多详细信息，请参阅N Storage Map 文档。

迭代存储映射
Substrate Storage Maps 的键和值是可迭代的。由于地图通常用于跟踪无限的数据集（例如帐户余额），因此在运行时不小心对其进行迭代可能会导致无法及时生成块。此外，由于访问映射的元素比访问本地列表的元素需要更多的数据库读取，因此在执行时间方面，映射迭代的成本明显高于列表迭代。

一般来说，Substrate 专注于根据原则和最佳实践进行编程，而不是硬性的对错规则。此处的信息旨在帮助您了解Substrate 的所有存储功能以及如何以尊重其设计原则的方式使用它们。例如，在运行时迭代存储映射既不是对也不是错——然而，就最佳实践而言，避免它会被认为是一种更好的方法。

Substrate 的 Iterable Storage Map 接口定义了以下方法：

iter()- 不按特定顺序枚举地图中的所有元素。如果您在执行此操作时更改地图，您将得到未定义的结果。看：

IterableStorageMap
IterableStorageDoubleMap
IterableStorageNMap.
drain()- 从地图中删除所有元素并以不特定的顺序遍历它们。如果您在执行此操作时向地图添加元素，您将获得未定义的结果。看：

IterableStorageMap
IterableStorageDoubleMap
IterableStorageNMap
translate()- 使用提供的函数来翻译地图的所有元素，没有特定的顺序。要从地图中删除元素，请None从翻译函数返回。看：

IterableStorageMap
IterableStorageDoubleMap
IterableStorageNMap
申报存储项目
运行时存储项目是`#[pallet::storage]` 在任何基于 FRAME 的托盘中创建的。以下是声明四种不同类型的存储项的示例：
```
#[pallet::storage]
type SomePrivateValue<T> = StorageValue<_, u32, ValueQuery>;

#[pallet::storage]
#[pallet::getter(fn some_primitive_value)]
pub(super) type SomePrimitiveValue<T> = StorageValue<_, u32, ValueQuery>;

#[pallet::storage]
pub(super) type SomeComplexValue<T: Config> = StorageValue<_, T::AccountId, ValueQuery>;

#[pallet::storage]
#[pallet::getter(fn some_map)]
pub(super) type SomeMap<T: Config> = StorageMap<_, Blake2_128Concat, T::AccountId, u32, ValueQuery>;

#[pallet::storage]
pub(super) type SomeDoubleMap<T: Config> = StorageDoubleMap<_, Blake2_128Concat, u32, Blake2_128Concat, T::AccountId, u32, ValueQuery>;

#[pallet::storage]
#[pallet::getter(fn some_nmap)]
pub(super) type SomeNMap<T: Config> = StorageNMap<
    _,
    (
        NMapKey<Blake2_128Concat, u32>,
        NMapKey<Blake2_128Concat, T::AccountId>,
        NMapKey<Twox64Concat, u32>,
    ),
    u32,
    ValueQuery,
>;
```
请注意，映射的存储项指定将使用的散列算法。

QueryKindTrait
传递给存储项的实现QueryKindTrait决定了当存储中没有值时应该如何处理存储。使用OptionQuery时，当没有存储值时，该get方法将返回None。使用ValueQuery，当没有存储值时，该get方法将返回使用OnEmpty泛型配置的值。对于需要配置特定默认值的情况，建议使用ValueQuery.

能见度
在上面的例子中，除此之外的所有存储项SomePrivateValue都通过pub关键字公开。区块链存储始终从运行时外部公开可见；Substrate 存储项目的可见性仅影响运行时中的其他托盘是否能够访问存储项目。

吸气剂方法
该`#[pallet::getter(..)]`宏提供了一个可选get扩展，可用于为包含该存储项的模块上的存储项实现 getter 方法；该扩展将所需的 getter 函数名称作为参数。如果你省略了这个可选的扩展，你仍然可以访问存储项的值，但是你将无法通过在模块上实现的 getter 方法来访问；相反，您将需要使用存储项目的get方法。

可选扩展只影响从getterSubstrate 代码中访问存储项的方式——您将始终能够查询运行时的存储以获取存储项的值。

下面是一个示例，它实现了一个为名为some_value的存储值命名的 getter 方法SomeValue。这个托盘现在可以访问Self::some_value()除了方法之外的SomeValue::get()方法：
```
#[pallet::storage]
#[pallet::getter(fn some_value)]
pub(super) type SomeValue = StorageValue<_, u64, ValueQuery>;
```
默认值
Substrate 允许您指定在未设置存储项的值时返回的默认值。虽然默认值实际上并不占用运行时存储，但运行时逻辑在执行过程中会看到这个值。

这是在存储中指定默认值的示例：
```
#[pallet::type_value]
pub(super) fn MyDefault<T: Config>() -> T::Balance { 3.into() }
#[pallet::storage]
pub(super) type MyStorageValue<T: Config> =
    StorageValue<Value = T::Balance, QueryKind = ValueQuery, OnEmpty = MyDefault<T>>;
```
请注意，为了使每个存储字段更加清晰，上面的语法是声明存储项的非缩写版本。

访问存储项目
使用 Substrate 构建的区块链公开了可用于查询运行时存储的远程过程调用 (RPC) 服务器。您可以使用Polkadot JS等软件库轻松地从您的代码与 RPC 服务器交互并访问存储项。Polkadot JS 团队还维护Polkadot Apps UI，这是一个功能齐全的 Web 应用程序，用于与基于 Substrate 的区块链交互，包括查询存储。

散列算法
Substrate 中存储映射的一个新特性是它们允许开发人员指定将用于生成映射键的散列算法。用于封装散列逻辑的 Rust 对象称为“散列器”。广义上讲，Substrate 开发人员可用的哈希器可以用两种方式描述：（1）它们是否是加密的；(2) 它们是否产生透明的输出。

为了完整起见，下面描述了非透明哈希算法的特征，但请记住，对于基于 FRAME 的区块链，不推荐使用任何不产生透明输出的哈希算法。

加密哈希算法
密码散列算法使我们能够构建工具，使操纵散列算法的输入以影响其输出变得极其困难。例如，即使输入是数字 1 到 10，加密散列算法也会产生广泛分布的输出。当用户能够影响存储映射的密钥时，使用加密散列算法至关重要。不这样做会创建一个攻击向量，使恶意行为者很容易降低区块链网络的性能。应使用加密哈希算法生成其密钥的映射示例是用于跟踪帐户余额的映射。在这种情况下，使用加密散列算法非常重要，这样攻击者就无法通过向顺序帐号进行许多小额转账来轰炸您的系统。如果没有适当的加密散列算法，这将创建一个不平衡的存储结构，从而影响性能。阅读更多关于 Subsrate 中的常见哈希器的信息常见的 Substrate 哈希器。

加密哈希算法比它们的非加密算法更复杂和资源密集，这就是为什么运行时工程师了解它们的适当用法以充分利用 Substrate 提供的灵活性很重要的原因。

透明散列算法
透明散列算法是一种可以轻松发现和验证用于生成给定输出的输入的算法。在 Substrate 中，散列算法通过将算法的输入连接到其输出而变得透明。这使得用户检索密钥的原始未散列值并在他们愿意时验证它（通过重新散列）变得微不足道。Substrate 的创建者已弃用在基于 FRAME 的运行时中使用非透明哈希器，因此提供此信息主要是为了完整性。事实上，如果您想访问可迭代的地图 功能，则有必要使用透明散列算法。

常见的 Substrate 哈希器
下表列出了 Substrate 中使用的一些常见哈希，并表示那些是加密的和那些是透明的：

哈希器	密码学	透明的
布莱克2 128 康卡特	X	X
TwoX 64 连续		X
身份		X
身份散列器封装了一个散列算法，其输出等于其输入（身份函数）。仅当起始密钥已经是加密哈希时才应使用这种类型的哈希。

创世纪配置
Substrate 的运行时存储 API 包括初始化区块链创世块中的存储项的功能。创世存储配置 API 公开了许多初始化存储的机制，所有这些机制都在`#[pallet::genesis_config]`. GenesisConfig数据类型在属性下定义，属性`#[pallet::genesis_config]`用于`#[pallet::genesis_build]`构建创世配置。

要使用托盘的创世配置功能，您必须 Config在将托盘添加到运行时时包含该元素。通知运行时的所有GenesisConfig托盘类型都将聚合GenesisConfig为该运行时的单一类型，该类型实现BuildStoragetrait。例如，在node_template_runtime::GenesisConfig结构中，此类型的每个属性都对应于GenesisConfig运行时托盘中具有Config元素的一个。最终，运行时GenesisConfig通过ChainSpectrait暴露出来。

有关使用 Substrate 创世存储配置功能的完整具体示例，请参阅Substrate 代码库随附的链规范中的 Society 托盘存储的创世配置。继续阅读以获取有关这些功能的更详细说明。

genesis_config
该`#[pallet::genesis_config]`宏提供了一个扩展，它将向托盘的GenesisConfig数据类型添加一个属性。该属性的值将用作您链的创世块中存储项的初始值。扩展采用一个参数，该config参数将确定数据类型属性的名称——如果提供了扩展GenesisConfig，则此参数是可选的。get

下面是一个示例，它演示了如何使用具有名为 Storage Value 的config扩展来为 Storage Value 的托盘MyVal创建一个以数据类型命名的属性init_val。GenesisConfig然后在一个示例中使用此属性，该示例演示使用GenesisConfig类型在您的链的创世块中设置存储值的初始值。

在my_pallet/src/lib.rs：
```
#[pallet::genesis_config]
pub struct GenesisConfig<T: Config> {
		pub init_val: u64,
	}
```
在chain_spec.rs：
```
GenesisConfig {
    my_pallet: MyPalletConfig {
        init_val: 221u64 + SOME_CONSTANT_VALUE,
    },
}
```
genesis_build
该`#[pallet::genesis_build]`属性允许您定义genesis_configuration托盘本身的构建方式（这使您可以访问托盘的私有功能）。

这是一个示例，演示如何使用`#[pallet::genesis_config]`和`#[pallet::genesis_build]`设置存储项的初始值。在这种情况下，该示例涉及两个存储项：一个表示成员帐户 ID 列表，另一个指定列表中的特殊成员（主要成员）。

在my_pallet/src/lib.rs：
```
#[pallet::genesis_config]
struct GenesisConfig {
    members: Vec<T::AccountId>,
    prime: T::AccountId,
}

#[pallet::genesis_build]
impl<T: Config> GenesisBuild<T> for GenesisConfig {
    fn build(&self) {
        Pallet::<T>::initialize_members(&self.members);
        SomeStorageItem::<T>::put(self.prime);
    }
}
```
在chain_spec.rs：
```
GenesisConfig {
    my_pallet: MyPalletConfig {
        members: LIST_OF_IDS,
        prime: ID,
    },
}
```
您还可以使用genesis_build来定义GenesisConfig不绑定到特定存储项的属性。如果您希望在您的托盘中调用一个设置多个存储项目的私有帮助函数，或者调用在您的托盘中包含的其他一些托盘中定义的函数，这可能是可取的。例如，使用一个名为 的虚构私有函数intitialize_members，它看起来像：

在my_pallet/src/lib.rs：
```
#[pallet::genesis_config]
struct GenesisConfig {
    members: Vec<T::AccountId>,
    prime: T::AccountId,
}

#[pallet::genesis_build]
impl<T: Config> GenesisBuild<T> for GenesisConfig {
    fn build(&self) {
        Pallet::<T>::initialize_members(&config.members);
        SomeStorageItem::<T>::put(self.prime);
    }
}
```
在chain_spec.rs：
```
GenesisConfig {
    my_pallet: MyPalletConfig {
        members: LIST_OF_IDS,
        prime: ID,
    },
}
```
最佳实践
Substrate 旨在提供一个灵活的框架，允许您构建适合您需求的区块链。但是，Substrate 代码库遵循许多最佳实践，以促进创建安全、高性能和可长期维护的区块链网络。以下部分概述了使用 Substrate 存储的最佳实践，并描述了激励他们的重要首要原则。

存储什么
请记住，区块链运行时存储的基本原则是尽量减少其使用。只有共识关键数据应该存储在您的运行时中。如果可能，请使用散列等技术来减少必须存储的数据量。例如，Substrate 的许多治理功能——例如 Democracy Pallet 的功能允许网络参与者对可调度调用的哈希propose进行投票，该调用的大小始终是有界的，而调用本身的长度可能是无限的。在运行时升级的情况下尤其如此，其中可调度调用将整个运行时 Wasm blob 作为其参数。因为这些治理机制是在链上实现的，就给定提案的状态达成共识所需的所有信息也必须存储在链上——这包括正在投票的内容。然而，通过将链上提案绑定到其哈希值，Substrate 的治理机制允许以将与提案相关的所有数据延迟到其被批准后的方式完成此操作。这意味着存储不会浪费在失败的提案上。

一旦提案通过，有人可以发起实际的可调度调用（包括其所有参数），该调用将被散列并与提案中的散列进行比较。使用散列来最小化存储在链上的数据的另一种常见模式是将与IPFS中的对象关联的原图像存储；这意味着只有 IPFS 位置（大小有界的哈希）需要存储在链上。

哈希只是可用于控制运行时存储大小的一种机制。另一个机制的一个例子是bounds。

先验证，最后写
Substrate 不会在外部调度之前缓存状态。相反，它在调用更改时直接应用更改。如果外部失败，任何状态更改都将持续存在。因此，在确定满足所有先决条件之前不要进行任何存储突变，这一点很重要。一般而言，可能导致存储变异的代码块应按如下结构构成：
```
{
  // all checks and throwing code go here

  // ** no throwing code below this line **

  // all event emissions & storage writes go here
}
```
不要使用运行时存储来存储逻辑原子操作的上下文中的中间或临时数据或操作失败时不需要的数据。这并不意味着不应使用运行时存储来跟踪需要多个原子操作的正在进行的操作的状态，就像来自 Utility 托盘的多重签名功能一样。在这种情况下，运行时存储用于跟踪可调度调用上的签名者，即使给定调用可能永远不会收到足够的签名来实际调用。在这种情况下，每个签名都被认为是正在进行的多重签名操作中的一个原子事件；在满足与该签名相关的所有先决条件之前，不会存储记录单个签名所需的数据。

创建边界
创建存储项大小的界限是控制运行时存储使用的一种非常有效的方法，并且在整个 Substrate 代码库中重复使用。一般来说，任何大小由用户操作决定的存储项目都应该有一个界限。上述Multisig 托盘中的多重签名功能就是这样一个示例。在这种情况下，与多签名操作相关联的签名者列表由多签名参与者提供。因为这个签署人名单是达成共识所必需的关于多重签名操作的状态，它必须存储在运行时。但是，为了让运行时开发人员能够控制这些列表可能占用多少存储空间，实用工具托盘要求用户配置此数字的界限，该界限将作为先决条件包含在内，然后再将任何内容写入存储。

下一步去哪里
查看一些涵盖各种存储主题的指南：

使用存储映射铸造基本代币。
操作方法：创建存储结构
存储价值
存储映射
StorageDoubleMap
StorageNMap