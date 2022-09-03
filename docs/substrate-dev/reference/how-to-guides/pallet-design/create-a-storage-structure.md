# 创建存储结构（struct）

创建类似分组的存储项目的结构是跟踪它们的有序方法。StorageValue以这种方式分组时，比单独保存单个项目更容易引用它们。这可以使测试和创世配置更容易。

本指南向您展示如何创建一个StorageValue包含结构并用于on_initialize. 该结构执行以下操作：

跟踪初始金额 ( issuance)
跟踪收到该金额的帐户 ( minter)
跟踪可以烧掉一些金额的帐户 ( burner)
（部分）用于on_initialize
在你开始之前
确保您有一个工作托盘来构建您的结构。如果您还没有正在处理的模板，请使用模板托盘。

创建一个结构

命名结构MetaData并声明其不同的类型：
```
#[derive(Clone, Encode, Decode, Eq, PartialEq, RuntimeDebug, Default)]
pub struct MetaData<AccountId, Balance> {
	issuance: Balance,
	minter: AccountId,
	burner: AccountId,
}
```
将结构声明为存储项

用于StorageValue将结构声明为存储中的新单项：
```
#[pallet::storage]
#[pallet::getter(fn meta_data)]
	pub(super) type MetaDataStore<T: Config> = StorageValue<_, MetaData<T::AccountId, T::Balance>, ValueQuery>;
```
配置GenesisConfig

使用该#[pallet::genesis_config]属性初始化MetaData结构中的值。
```
// Declare `admin` as type `T::AccountId`.
#[pallet::genesis_config]
pub struct GenesisConfig<T: Config> {
	pub admin: T::AccountId,
	}
// Give it a default value.
#[cfg(feature = "std")]
impl<T: Config> Default for GenesisConfig<T> {
	fn default() -> Self {
		Self {
			admin: Default::default(),
			}
		}
	}
```
这个变量必须和文件里面admin使用的变量对应。node/chainspec.rsfn testnet_genesis

配置GenesisBuild

使用`#[pallet::genesis_build]`属性来初始化结构的值，使用admin来初始化 type 的值T::AccountId：
```
#[pallet::genesis_build]
impl<T: Config> GenesisBuild<T> for GenesisConfig<T> {
	fn build(&self) {
		MetaDataStore::<T>::put(MetaData {
			issuance: Zero::zero(),
			minter: self.admin.clone(),
			burner: self.admin.clone(),
		});
	}
}
```
使用结构体on_initialize()

将金额分配给启动链时要初始化的issuance字段：MetaData
```
fn on_initialize(_n: T::BlockNumber) -> Weight {
	// Create an alias for the StorageValue struct.
	let mut meta = MetaDataStore::<T>::get();

	// Add a value to the `issuance` field.
	let value: T::Balance = 50u8.into();
	meta.issuance = meta.issuance.saturating_add(value);

	// Add the amount to the `minter` account in storage.
	Accounts::<T>::mutate(&meta.minter, |bal| {
		*bal = bal.saturating_add(value);
	});
}
```
该on_initialize函数确保在启动链时将指定项目的值写入存储。

例子
reward-coin示例托盘
资源
Default::default()