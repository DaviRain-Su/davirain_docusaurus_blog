# 配置创世状态

Genesis 配置定义了诸如帐户、余额、自定义托盘的 Genesis 等事物的初始状态。本指南解释了如何使用 2 个简单的存储项目配置托盘的起源。

在您的托盘中实施创世纪
为了本指南的目的，我们假设您有一个托盘`<SingleValue<T>>`和`<AccountMap<T>>`.

例如：
```
#[pallet::storage]
#[pallet::getter(fn something)]
pub type SingleValue<T: Config> = StorageValue<_, T::Balance>;

// A map that has enumerable entries.
#[pallet::storage]
#[pallet::getter(fn accounts)]
pub type AccountMap<T: Config> = StorageMap<_, Blake2_128Concat, T::AccountId, T::Balance>;
```
该GenesisConfig代码应该在您的存储项目之后。

使用该`#[pallet::genesis_config]`属性，为您的托盘编写GenesisConfig结构。
```
#[pallet::genesis_config]
pub struct GenesisConfig<T: Config> {
	pub single_value: T::Balance,
	pub account_map: Vec<(T::AccountId, T::Balance)>,
}
```
设置GenesisConfig结构的默认值。
```
#[cfg(feature = "std")]
impl<T: Config> Default for GenesisConfig<T> {
	fn default() -> Self {
		Self { single_value: Default::default(), account_map: Default::default() }
	}
}
```
使用`#[pallet::genesis_build]`属性，实现GenesisBuild特征。
```
#[pallet::genesis_build]
impl<T: Config> GenesisBuild<T> for GenesisConfig<T> {
	fn build(&self) {
		<SingleValue<T>>::put(&self.single_value);
		for (a, b) in &self.account_map {
			<AccountMap<T>>::insert(a, b);
		}
	}
}
```
这允许您执行一些逻辑来定义GenesisConfig结构如何将某些内容放入存储中。

配置节点的链规范
剩下要做的就是指定我们想要在链的创世状态中放入哪些值。我们假设我们的托盘被调用pallet_something，声明为PalletSomething在我们运行时的construct_runtime!宏中。

在node/chain_spec.rs中，创建T::Balance要存储的类型的常量值`<SingleValue<T>>`（在testnet_genesis方法内部）。
```
const VALUE: Balance = 235813;
```
请注意，此步骤还意味着我们已在文件use node_template_runtime::Balance;顶部导入chain_spec.rs。

创建一个帐户向量以进行初始化`<AccountMap<T>>`（也在testnet_genesis方法内部）。
```
let accounts_to_map: Vec<AccountId> =
	vec![
		get_account_id_from_seed::<sr25519::Public>("Alice"),
		get_account_id_from_seed::<sr25519::Public>("Bob"),
		get_account_id_from_seed::<sr25519::Public>("Charlie"),
	];
```
完成函数GenesisConfig中的子句。testnet_genesis

runtime/src/lib.rs惯例是在inside中使用您的托盘名称的小写拼写construct_runtime!。例如，对于声明为的托盘CamelCase，约定是camel_case在testnet_genesis函数中引用它。

在我们的示例中，这看起来像：
```
pallet_something: PalletSomethingConfig {
	single_value: VALUE,
	account_map: accounts_to_map.iter().cloned().map(|x| (x, VALUE)).collect(),
}
```
在上面的代码中，我们将每个帐户从 映射accounts_to_map到等于 的金额VALUE。GenesisConfig这种模式与 Balances 托盘的实现非常相似。

例子
节点模板“chain_spec.rs”
示例托盘
相关资料
了解如何为天平托盘定制链的创世配置。
BalancesConfig
