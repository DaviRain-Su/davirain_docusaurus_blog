# 账户数据结构

账户是任何区块链的基本组成部分，并且在 Substrate 中，账户可以以多种不同的方式使用。本文介绍了账户是如何存储在 Substrate 中的，以及账户数据结构是如何在运行时逻辑中用于管理账户生命周期的。

帐户
数据类型是一个存储映射，Account通常在frame-system托盘中定义：

/// The full account information for a particular account ID.
#[pallet::storage]
#[pallet::getter(fn account)]
pub type Account<T: Config> = StorageMap<
  _,
  Blake2_128Concat,
  T::AccountId,
  AccountInfo<T::Index, T::AccountData>,
  ValueQuery,
>;

StorageMapfor anAccount由以下参数组成：

第一个参数 (_) 用于宏扩展。
Blake2_128Concat指定要使用的散列算法。
T::AccountId用作AccountInfo<T::Index, T::AccountData>结构体的键。
有关详细信息，请参阅StorageMapAPI。

帐户信息
一个AccountInfo帐户在frame_system托盘中定义：

#[derive(Clone, Eq, PartialEq, Default, RuntimeDebug, Encode, Decode)]
pub struct AccountInfo<Index, AccountData> {
  /// The number of transactions this account has sent.
  pub nonce: Index,
  /// The number of other modules that currently depend on this account's existence. The account
  /// cannot be reaped until this is zero.
  pub consumers: RefCount,
  /// The number of other modules that allow this account to exist. The account may not be reaped
  /// until this and `sufficients` are both zero.
  pub providers: RefCount,
  /// The number of modules that allow this account to exist for their own purposes only. The
  /// account may not be reaped until this and `providers` are both zero.
  pub sufficients: RefCount,
  /// The additional data that belongs to this account. Used to store the balance(s) in a lot of
  /// chains.
  pub data: AccountData,
}

每个帐户都AccountInfo包含以下内容：

指示帐户已发送的nonce交易数量。
consumers指示当前依赖此帐户存在的其他模块的数量的引用计数器。
providers指示允许此帐户存在的其他模块数量的引用计数器。
引用计数器，sufficients指示允许此帐户仅用于其自身目的的模块数量。
您可以配置用于保存不同类型数据的AccountData结构。
帐户参考计数器
帐户引用计数器跟踪运行时中的帐户依赖性。例如，如果您将数据存储在由帐户控制的地图下，则在删除帐户控制的地图下存储的数据之前，您不希望删除该帐户。

和引用计数器consumers设计providers为一起使用。例如，当帐户在成为验证者之前设置其会话密钥时，托盘中的consumers引用计数器会增加。 在计数器可以递增之前Session，providers参考计数器必须大于零。consumer

参考providers计数器指示帐户是否已准备好被依赖。例如，providers当创建的新帐户超过现有存款[2]时，参考计数器会增加。

providers引用计数器会阻止 Substrate 托盘存储有关帐户的数据，直到帐户处于活动状态 ( > providers0)。consumers引用计数器会阻止 Substrate 托盘删除帐户，直到所有托盘中有关该帐户的数据都被删除（== consumers0）。参考计数器让consumers用户对存储在链上的数据负责。如果用户想要删除他们的账户并取回现有存款，他们需要删除他们存储在所有链上托盘上的所有数据以递减consumers计数器。

托盘还具有清理功能，以减少providers参考计数器以将帐户标记为在托盘管理范围内停用。当账户providers参考计数器为零且consumers为零时，所有链上托盘都认为该账户已停用。

参考sufficients计数器指示帐户是否自给自足并且可以独立存在。例如，在资产托盘中，一个账户可以拥有足够数量的某些资产，但不拥有任何本地账户余额。

运行时开发人员可以使用托盘公开的inc_consumers()、dec_consumers()、inc_providers()、dec_providers()、inc_sufficients()和方法更新这些计数器。在账户生命周期中，对某个计数器的每次递增调用都应该伴随着相应的计数器递减调用。dec_sufficients()frame-system

还有三个查询功能可以简化这些计数器的使用：

can_inc_consumer()检查帐户是否可以使用（providers> 0）。
can_dec_provider()在递减到 0之前检查一个帐户是否在运行时不再被引用（consumers== 0） 。providers
is_provider_required()检查帐户是否有未完成的消费者参考（consumers> 0）。
有关详细信息，请参阅frame-systemAPI。

AccountData 特征和实现
AccountInfo可以是任何结构，只要该结构满足 trait 中定义的关联类型traitAccountData bound 。默认情况下，Substrate 运行时配置为.frame-system::pallet::ConfigAccountInfopallet-balances

下一步去哪里
有关其他技术详细信息，请参阅以下资源：

frame_system::AccountInfoAPI
pallet_balances::AccountData应用程序接口。
pallet_session::Pallet::set_keys可调度调用
frame_system::Provider HandleLifetime执行
pallet_assets new_account功能