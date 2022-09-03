# 帧宏

Substrate 使用自定义的Rust 宏来生成代码并从您为运行时实现的托盘聚合逻辑。这些运行时宏允许您专注于运行时逻辑，而不是花时间编码和解码链上变量或复制基本区块链开发所需的代码。

本节概述了 Rust 中可用的宏类型，并重点介绍了特定 FRAME 宏在运行时开发中的使用方式。

## 宏基础
在计算机编程中，宏是封装了要执行的预设指令序列的代码行。作为编写代码的代码，宏使您能够抽象重复操作并简化您需要编写的代码。使用宏，您可以隐式声明复杂的数据结构。

在 Rust 中，宏可以是声明性宏或过程宏。

声明性宏使您能够声明模式并将表达式的结果与模式进行比较，然后根据模式是否匹配执行任何其他代码行。声明式宏在 Rust 编程中被广泛使用。

过程宏类似于函数。与声明性宏中的模式匹配不同，过程宏将代码作为输入，对输入执行一些指令集，并生成代码作为输出。有三种类型的过程宏：

自定义派生宏使您能够为给定类型定义和重用特征的实现。该derive宏对于定义必须满足特定特征的自定义运行时类型的实现特别有用。
类属性宏使您能够创建新属性以生成代码。
类函数宏使您能够定义像函数调用一样操作以生成代码的宏。
因为宏在编译器解释它们包含的代码行之前被扩展，它们可以定义复杂的数据结构和操作。FRAME 宏利用不同类型的宏来为通常是复杂的代码块提供快捷抽象。但是，宏提供的抽象可能会使运行时代码有些难以遵循。

要了解有关 Substrate 运行时中使用的 FRAME 宏的更多信息，您可以安装和使用cargo-expand。安装 cargo-expand 后，您可以使用该cargo expand命令显示运行时使用的宏中包含的代码的结果。

## FRAME 支持和系统宏
Substrate 原语和 FRAME 都依赖于不同宏的集合。本节概述了 FRAME 支持和 FRAME 系统库中提供的宏。在大多数情况下，这些宏提供了其他托盘所依赖的框架，您应该熟悉它们在运行时逻辑中的使用方式和位置。在概述之后，本节将介绍您作为运行时开发人员最有可能使用的特定宏。

### FRAME 支持宏
crate 提供了运行时中使用的frame_support许多最重要的声明性、派生、类属性和类函数宏。您应该从 crate 中熟悉的一些重要宏frame_support包括以下内容：

construct_runtime 用于从您已实现的托盘列表中构建运行时。
match_types 用于创建一个实现Containstrait 的类型，其语法类似于matches!.
parameter_types 用于创建Gettrait 的新实现。
有关frame_supportcrate 中的宏的更多信息，请参阅宏、派生宏和属性宏的 Rust 文档。

### FRAME 系统宏
frame_systemcrate 使用宏来定义提供对核心数据类型和共享实用程序的访问的原语。这些原语和相关宏构成了外部节点和运行时中许多节点操作的基础，并充当其他托盘与 Substrate 框架交互的基础层。

您应该从 crate 中熟悉的一些重要的原语和宏frame_system包括以下内容：
- sp_core
	- map 用于从数组初始化键值集合。
	- RuntimeDebug用于调试运行时。
	- 有关sp_core类似函数的宏的更多信息，请参阅宏。
- sp_runtime
	- bounded_btree_map 用于btree-map从给定的文字构建有界。
	- bounded_vec 用于vec从给定的文字构建有界。
	- impl_opaque_keys用于实现OpaqueKeys描述的数据结构。
	- parameter_types用于创建Gettrait 的新实现。
有关sp_runtime类似函数的宏的更多信息，请参阅宏。有关sp_runtime派生宏的信息，请参阅派生宏。

- sp_api
	- decl_runtime_apis 用于将指定的特征声明为运行时 API。
	- impl_runtime_apis 用于将指定的特征实现标记为运行时 API。
有关sp_api类似函数的宏的更多信息，请参阅宏。

- sp_std
	- if_std 用于指示仅应在std启用功能集时运行的代码。
	- map 用于从数组初始化键值集合。
	- vec 用于创建包含参数的向量。
有关sp_std类似函数的宏的更多信息，请参阅宏。

- sp_version
	- create_apis_vec 用于创建 API 声明向量。
	- create_runtime_str用于创建RuntimeString常量。
	- runtime_version 用作接受运行时版本声明并生成具有等效内容的自定义 WebAssembly 部分的属性。
您将看到这些 crate 中的许多在运行时和Cargo.toml节点模板的节点文件中列为依赖项。

## 用于组合托盘的宏
如构建定制托盘中所述，大多数 FRAME 托盘由一组通用部分组成。

宏使构建这些部分中的每一个都更加模块化和可扩展。本节介绍可用的宏以及如何使用它们来构建您的自定义运行时。

### #[托盘]
`#[pallet]`需要宏来声明托盘。该属性宏是托盘模块（mod pallet）的属性。在该pallet模块中，`#[pallet]`宏用作附加`#[pallet::*]`宏的入口点，这些宏描述用于识别托盘所需特定物品的属性。例如，一个托盘通常包括一组类型、函数和特征实现，这些实现由`construct_runtime!`宏聚合以构建运行时。
```
#[pallet]
pub mod pallet {
...
}
```
在模块内部，宏解析具有属性的项目`#[pallet::*]`。有些`#[pallet::*]`属性是必需的，有些是可选的。

您可以使用 from these crates 自动从frame_support和crates导入系统级类型。例如：`frame_systempallet_prelude`
```
#[pallet]
pub mod pallet {
		use frame_support::pallet_prelude::*;
		use frame_system::pallet_prelude::*;
		...
}
```
该`#[pallet]`宏类似于派生宏，因为它通过读取输入来扩展托盘类型和特征实现。在大多数情况下，宏不会修改任何输入。然而，有一些特定的场景——与派生宏不同——这个宏会修改它的输入。

宏将在以下情况下修改输入：

如果泛型被替换为类型

例如，如果在宏中将项目的内部类型`pub struct Pallet<..>(_)`替换为`pallet::storage`实现该StorageInstance特征的类型，则会发生这种情况。

如果函数或数据结构发生变化

例如，如果`pallet::type_value`宏将函数项更改为 struct 和 trait 实现，就会发生这种情况。

如果用户未提供文档

例如，如果没有提供文档，宏`pallet::pallet`会修改输入以在`struct Pallet<T>(_);`项目上方添加文档。

### #[托盘::配置]
#[pallet::config]需要宏来定义托盘使用的通用数据类型。

此宏提供作为托盘系统级Config特征一部分的常量。

此宏的 Config 特征必须定义为Config包含系统级`frame_system::Config`特征的常规特征定义。该定义可以包括其他顶级特征和 where 子句。例如：
```
#[pallet::config]
pub trait Config: frame_system::Config + $optionally_some_other_supertraits
$optional_where_clause
{
...
}
```
要绕过frame_system::Config要求，您可以使用属性`#[pallet::disable_frame_system_supertrait_check]`。例如：
```
#[pallet::config]
#[pallet::disable_frame_system_supertrait_check]
pub trait Config: pallet_timestamp::Config {}
```
### #[托盘::常量]
`#[pallet::constant]`宏在宏内部为traitConfig提供`#[pallet::config]`运行时所需的类型和属性，并生成关联的元数据。

此宏将有关托盘中使用的常量的信息添加到运行时元数据，包括：

常量名
关联类型的名称
常数值
Get::get()常量返回的值
例如，您可以使用`#[pallet::constant]`添加type MyGetParam到元数据：
```
#[pallet::config]
pub trait Config: frame_system::Config {
	#[pallet::constant] // puts attributes in metadata
	type MyGetParam: Get<u32>;
}
```
### ##[托盘::extra_constants]
该`#[pallet::extra_constants]`宏使您能够向元数据添加常量。

例如，您可以声明一个返回生成值的函数。然后，您可以使用 `#[pallet::extra_constants]`宏将生成值的信息添加到元数据中：
```
#[pallet::extra_constants]
impl<T: Config> Pallet<T> {
  //Example function using extra_constants
  fn example_extra_constants() -> u128 { 4u128 }
}
```
### #[托盘::托盘]
`#[pallet::pallet]`宏需要声明供宏使用的托盘数据结构占位符`construct_runtime!`。这个宏必须定义为一个名为 Pallet 的结构，它具有泛型类型并且没有where子句。

例如：
```
#[pallet::pallet]
pub struct Pallet<T>(_);
```
如果您提供属性宏，则此宏可以生成Store特征以包含每个存储项目的关联类型 。#[pallet::generate_store($vis trait Store)]

例如：
```
#[pallet::pallet]
#[pallet::generate_store(pub(super) trait Store)]
pub struct Pallet<T>(_);
```
有关使用存储和此宏的更多信息，请参阅添加到定义中的宏扩展。`struct Pallet<T>`

`#[pallet::without_storage_info]`
该`#[pallet::without_storage_info]`宏使您能够定义没有固定尺寸的托盘存储项目。

默认情况下，所有托盘存储项都需要实现traits::StorageInfoTrait，以便所有键和值类型都具有基于pallet_prelude::MaxEncodedLen属性中定义的边界的固定大小。平行链开发需要此大小限制来估计有效性证明 (PoV) blob 的大小。

`#[pallet::without_storage_info]`如果您需要对整个托盘进行无限存储，属性宏允许您覆盖默认行为。要使用它，请将`#[pallet::without_storage_info]`属性添加到托盘结构中，如下所示：
```
#[pallet::pallet]
#[pallet::generate_store(pub(super) trait Store)]
#[pallet::without_storage_info]
pub struct Pallet<T>(_);
```
请注意，仅`#[pallet::without_storage_info]`当您需要使货盘中的所有存储项目不受限制时，才应使用宏。如果您只需要特定存储项的未定义存储，则可以使用`#[pallet::unbounded]`属性宏来覆盖固定大小约束。

因为该`#[pallet::without_storage_info]`宏适用于托盘中的所有存储项目，所以您应该只在测试或开发环境中使用它。您永远不应该`#[pallet::without_storage_info]`在生产环境中使用属性宏。

有关使用存储和此宏的更多信息，请参阅添加到定义中的宏扩展。`struct Pallet<T>`

### #[托盘::无界]
属性宏使您能够将`#[pallet::unbounded]`特定存储项声明为无界。默认情况下，所有托盘存储项目都需要具有固定尺寸。您可以使用此属性宏来覆盖特定存储项目的默认要求。如果您是平行链开发人员，您可以使用此宏来存储永远不会进入有效性证明 (PoV) blob 的项目。

### #[托盘::钩子]
该`#[pallet::hooks]`宏允许您声明可选的托盘挂钩，以在块制作过程中的特定点实现特定于托盘的逻辑。在`#[pallet::hooks]`宏中，您可以实现Hookstrait 以在块被初始化或完成时、运行时升级之前或运行时升级完成后执行逻辑。

例如：
```
#[pallet::hooks]
impl<T: Config> Hooks<BlockNumberFor<T>> for Pallet<T> {
  // Hooks functions and logic goes here.
}
```
有关使用钩子的更多信息，请参阅关于托盘::钩子和宏扩展的 Rust 文档。

### #[托盘::调用]
`#[pallet::call]`需要实现可以为托盘分派到运行时的功能。每个功能必须：

`#[pallet::weight($expr)]`使用属性定义权重
有它的第一个论点为`origin: OriginFor<T>`
使用 `#[pallet::compact]` 对参数使用紧凑编码
返回`DispatchResultWithPostInfo`或`DispatchResult`
进入运行时的外部请求可以使用调用来触发特定逻辑。调用也可以用于链上治理，民主托盘可以对调用进行投票。使用`enum#[pallet::call]`聚合所有函数调用逻辑。聚合使 FRAME 能够将相同类型的函数批处理到单个运行时调用中。然后，运行时从代码块中定义的实现生成相关项。Callimpl

了解更多信息。请参阅托盘::call的 Rust 文档。

### #[托盘::错误]
该`#[pallet::error]`宏允许您定义可以从分派到运行时的函数调用返回的错误类型。错误信息包含在运行时元数据中。

必须将宏定义为一个名为 Error 且具有泛型类型的枚举以及带或不带字段的变体。

例如：
```
#[pallet::error]
pub enum Error<T> {
	/// $some_optional_doc
	$SomeFieldLessVariant,
	/// $some_more_optional_doc
	$SomeVariantWithOneField(FieldType),
	...
}
```
您为枚举变体指定的任何字段类型都必须实现该`scale_info::TypeInfo`特征，并且其编码大小应尽可能小。枚举变体中的字段类型还必须实现PalletError特征才能编译。

有关更多信息，请参阅 Pallet ::error的 Rust 文档。

### #[托盘::事件]
该`#[pallet::event]`宏允许您定义托盘的事件类型。

该宏与宏类似，`pallet::error`但它可以保存更多信息。该宏被定义为一个名为 Event 的枚举。

例如：
```
#[pallet::event]
#[pallet::generate_deposit($visibility fn deposit_event)] // Optional
pub enum Event<$some_generic> $optional_where_clause {
	/// Some doc
	$SomeName($SomeType, $YetanotherType, ...),
	...
}
```
有关更多信息，请参阅 `Pallet ::event`的 Rust 文档。

### #[托盘::存储]
该`#[pallet::storage]`宏使您能够在运行时存储中定义抽象存储并为该存储设置元数据。该属性宏可以多次使用。

可以使用具有 `StorageValue`、`StorageMap` 或 `StorageDoubleMap` 类型别名的`[pallet::storage]`命名或未命名泛型来定义宏。
```
#[pallet::storage]
#[pallet::getter(fn $getter_name)] // optional
$vis type $StorageName<$some_generic> $optional_where_clause
	= $StorageType<$generic_name = $some_generics, $other_name = $some_other, ...>;
```
有关更多信息，请参阅有关 `palm ::storage`和以下存储数据结构的 Rust 文档：

StorageDoubleMap
存储映射
存储价值
`#[托盘::type_value]`
该`#[pallet::type_value]`宏使您能够定义一个实现Get存储类型特征的结构。此属性宏可以与 `#[pallet::storage]` 宏结合多次使用，以定义存储中的默认值。
```
#[pallet::type_value]
fn MyDefault<T: Config>() -> T::Balance { 3.into() }
```
有关使用此宏的更多信息，请参阅`Pallet::type_value`的 Rust 文档。

### #[托盘::genesis_build]
该`#[pallet::genesis_build]`宏允许您定义创世配置的构建方式。

该宏被定义为具有` trait GenesisBuild` 的泛型类型 `<T: Config>` 的 Rust trait 实现在 GenesisConfig 类型上。

例如：
```
#[pallet::genesis_build]
impl<T: Config> GenesisBuild<T> for GenesisConfig {
	fn build(&self) {}
}
```
有关更多信息，请参阅`Pallet::genesis_build`的 Rust 文档。

### #[托盘::genesis_config]
该`#[pallet::genesis_config]`宏允许您定义托盘的创世配置。

宏可以定义为枚举或结构，但必须是公共的并使用 `#[pallet::genesis_build]` 宏实现 GenesisBuild 的特征。

例如：
```
#[pallet::genesis_config]
pub struct GenesisConfig<T: Config> {
	_myfield: BalanceOf<T>,
}
```
有关更多信息，请参阅`Pallet::genesis_config`的 Rust 文档。

### #[托盘::固有]
该`#[pallet::inherent]`宏允许托盘在未签名的固有事务中提供数据。

宏被定义为具有绑定` <T: Config,>` 的 `trait ProvideInherent` 类型 Pallet 的 trait 实现.

例如：
```
#[pallet::inherent]
impl<T: Config> ProvideInherent for Pallet<T> {
	// ... regular trait implementation
}
```
有关更多信息，请参阅`pallet::inherent`的 Rust 文档。

### #[托盘::原点]
该`#[pallet::origin]`宏允许您定义托盘的原点。

宏必须定义为类型别名、枚举或结构。宏必须是公共的。

例如：
```
#[pallet::origin]
pub struct Origin<T>(PhantomData<(T)>);
```
有关更多信息，请参阅`pallet::origin`的 Rust 文档。

### #[pallet::validate_unsigned]
该`#[pallet::validate_unsigned]`宏允许托盘验证未签名的交易。

该宏被定义为一个 trait 实现，其绑定 `<T: Config>` 的 trait ValidateUnsigned 用于类型 Pallet.

例如：
```
#[pallet::validate_unsigned]
impl<T: Config> ValidateUnsigned for Pallet<T> {
	// ... regular trait implementation
}
```
有关更多信息，请参阅 Pallet ::validate_unsigned的 Rust 文档。

运行时构造宏
作为对宏使用的介绍，Substrate 运行时宏`frame_support`突出显示了和crates中提供的几个宏`frame_system`。然而，Substrate 运行时宏中引入的一些宏需要额外注意，因为它们在构建运行时逻辑中所起的作用。

构建运行时！
该`construct_runtime!`宏生成已为要包含在运行时中的托盘声明和实现的所有数据结构。

例如，`construct_runtime!`宏从托盘属性宏中推断信息以生成详细信息，例如：

表示 Substrate 运行时的运行时结构。
为包含在具有可调用函数的运行时中的每个托盘调用枚举变量和元数据。
发出事件的运行时中包含的每个托盘的事件枚举变体。
Genesis 配置结构，用于为定义存储项的运行时中包含的每个托盘构建存储。
支持固有事务的托盘的固有数据。
使用默认来源定义或特定于托盘的自定义来源识别函数调用者的托盘的来源枚举变体。
验证支持未签名交易的托盘的未签名交易。
该 `construct_runtime!`宏还为公开的数据结构和类型实现辅助特征，生成运行时中列出的托盘索引，并将运行时中的事件、错误和可调用函数映射回它们源自的托盘。

默认情况下，`construct_runtime!`宏包含宏定义中列出的所有托盘的所有托盘属性。您可以覆盖默认行为以排除特定部分或仅包括特定部分。例如，如果您不希望运行时公开在特定托盘中定义的函数调用，您可以在`construct_runtime!`宏中显式排除它们，并使用类似于以下的条目：
```
MyCustomPallet: pallet_my_custom_pallet exclude_parts { Call }
```
在此示例中，`MyCustomPallet`是唯一标识需要特殊处理的托盘的名称，并`pallet_my_custom_pallet`表示托盘的路径。`exclude_parts`关键字指定您只想排除指定托盘的某些属性。在这种情况下，仅排除 `MyCustomPallet` 的可调用函数。

use_parts同样，您可以使用关键字覆盖默认值以在运行时显式包含某些部分。例如，如果您只希望运行时公开特定托盘的属性子集，则可以`construct_runtime!`使用类似于以下的条目将它们显式包含在宏中：
```
MyCustomPallet: pallet_my_custom_pallet use_parts { Pallet, Call, Storage, Event, Origin, Config }
```
construct_runtime!您应该注意，宏中列出托盘的顺序很重要。默认情况下，第一个托盘的托盘索引从零开始，之后每个托盘递增。您可以通过为托盘添加索引号来手动调整`construct_runtime!`宏中的索引。例如，您可以为 生成所有托盘属性，MyCustomPallet并将索引设置为 8，语法如下：
```
MyCustomPallet: pallet_my_custom_pallet use_parts = 8,
```
但是，您还应该注意在construct_runtime 中用于定义托盘的顺序！宏影响创世存储配置。如果您有一个依赖于另一个托盘的托盘，请确保所依赖的托盘在依赖于它的托盘之前（即在之前列出或具有较低的索引值）。

有关更多信息，请参阅 Rust 文档中的`construct_runtime`

参数类型！
该`parameter_types!`宏声明了在运行时构造期间要分配给每个托盘的配置特征的参数类型。

该宏将每个指定的参数转换为结构类型，并带有get()返回指定类型的函数。每个参数结构类型还实现了一个`frame_support::traits::Get<I>trait` 来将类型转换为其指定的值。

有关更多信息，请参阅 Rust 文档中的parameter_types。

`impl_runtime_apis!`
宏为宏实现的`impl_runtime_apis!`所有特征生成运行时 API。在这个宏中实现的特征必须首先在`decl_runtime_apis`宏中声明。宏生成RuntimeApi和`RuntimeApiImpl`结构以将这些特征公开为运行时 API。宏公开的特征使外部节点组件能够通过RuntimeApi类型与运行时通信。

宏声明`RuntimeApi`和`RuntimeApiImpl`结构并为该结构实现各种辅助特征`RuntimeApiImpl`。如果您为运行时定义附加接口以在`impl_runtime_apis!`宏中公开，它们将附加到默认RuntimeApiImpl实现。

该宏还生成`RUNTIME_API_VERSIONS`常量以公开有关所有已实现api特征的版本信息。此常量用于实例化 的`apis`字段`RuntimeVersion`。

有关更多信息，请参阅impl_runtime_apis的 Rust 文档。

`app_crypto!`
该`app_crypto!`宏使用指定的签名算法生成特定于应用程序的加密密钥对。

该宏声明了以下结构类型：

`Public`

对于该Public类型，宏实现了`sp_application_crypto::AppKeytrait` 来定义公钥类型，并且该`sp_application_crypto::RuntimeAppPublictrait` 能够生成密钥对、签署交易和验证签名。

`Signature`

对于`Signature`类型，宏实现`core::hash::Hash`特征以指定用于散列签名的签名算法，例如 `SR25519` 或 `ED25519`。

`Pair`

对于该`Pair`类型，宏实现 `sp_application_crypto::Pair`和`sp_application_crypto::AppKey`特征以从秘密短语或种子生成公钥-私钥对。

除了这些结构的特征之外，宏还实现了辅助特征。

有关更多信息，请参阅`app_crypto`的 Rust 文档。

基准测试宏
FRAME 基准测试框架定义了几个用于对托盘进行基准测试的宏。以下宏用于基准测试：

`add_benchmarkVec<BenchmarkBatch>`使用托盘箱名称和生成的模块结构将托盘基准添加到对象。
`benchmarks`构建用于测试函数调用执行时间的基准逻辑。
`benchmarks_instancebenchmarks`为可实例化模块提供与宏相同的功能。
`benchmarks_instance_pallet`为使用 [ ] 宏benchmarks声明的可实例化托盘提供与宏相同的功能。`frame_support::pallet`
`cb_add_benchmarksadd_benchmark`作为define_benchmarks宏的回调调用。
`cb_list_benchmarkslist_benchmark`作为define_benchmarks宏的回调调用。
`define_benchmarks`为运行时定义所有基准测试托盘。
`impl_benchmark_test_suite`创建一个运行基准测试模块中定义的基准测试的测试套件。
`list_benchmark`为运行时配置的托盘生成基准列表。
`whitelist`将帐户添加到允许列表以进行测试。
参考
Rust 编程语言
Rust 宏小书
