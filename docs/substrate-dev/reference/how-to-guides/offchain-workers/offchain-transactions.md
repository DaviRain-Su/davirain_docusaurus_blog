# 进行链下交易
本指南将教您如何保存已由链下工作人员使用签名和未签名交易处理的数据。

您不能将脱链工作人员处理的数据直接保存到链上存储。要在链上存储来自链下工作人员的任何数据，您必须创建一个将来自链下工作人员的数据发送到链上存储系统的事务。您可以创建将数据从链下工作人员发送到链上存储的交易作为签名交易或未签名交易，具体取决于您希望如何处理交易调用帐户。例如：

如果您想记录关联的交易调用者并从调用者帐户中扣除交易费用，请使用签名交易。
如果您不想记录关联的交易调用者，请使用未签名的交易。
如果您想记录关联的交易调用者，但不希望调用者负责交易费用支付，请使用带有签名负载的未签名交易。
发送签名交易
在您的托盘中，调用链下工作人员的挂钩，如下所示：
```
#[pallet::hooks]
impl<T: Config> Hooks<BlockNumberFor<T>> for Pallet<T> {
	/// Offchain worker entry point.
	///
	/// By implementing `fn offchain_worker` you declare a new offchain worker.
	/// This function will be called when the node is fully synced and a new best block is
	/// successfully imported.
	/// Note that it's not guaranteed for offchain workers to run on EVERY block, there might
	/// be cases where some blocks are skipped, or for some the worker runs twice (re-orgs),
	/// so the code should be able to handle that.
	fn offchain_worker(block_number: T::BlockNumber) {
		log::info!("Hello from pallet-ocw.");
		// The entry point of your code called by offchain worker
	}
	// ...
}
```
CreateSignedTransaction将特征添加到托盘的 Config 特征中。例如，您的托盘Config特征应类似于以下内容：
```
/// This pallet's configuration trait
#[pallet::config]
pub trait Config: CreateSignedTransaction<Call<Self>> + frame_system::Config {
	// ...
}
```
添加一个crypto带有 sr25519 签名密钥的模块，以确保您的托盘拥有一个可用于签署交易的帐户。
```
use sp_core::{crypto::KeyTypeId};

// ...

pub const KEY_TYPE: KeyTypeId = KeyTypeId(*b"demo");

// ...

pub mod crypto {
	use super::KEY_TYPE;
	use sp_core::sr25519::Signature as Sr25519Signature;
	use sp_runtime::{
		app_crypto::{app_crypto, sr25519},
		traits::Verify, MultiSignature, MultiSigner
	};
	app_crypto!(sr25519, KEY_TYPE);

	pub struct TestAuthId;

	// implemented for runtime
	impl frame_system::offchain::AppCrypto<MultiSigner, MultiSignature> for TestAuthId {
	type RuntimeAppPublic = Public;
	type GenericSignature = sp_core::sr25519::Signature;
	type GenericPublic = sp_core::sr25519::Public;
	}
}
```
该app_crypto宏声明了一个带有 sr25519 签名的帐户，该签名由KEY_TYPE. 请注意，这不会创建新帐户。该宏仅声明此托盘有一个可用的加密帐户。您将需要在下一步中初始化此帐户。

初始化一个签名账户，用于将签名交易发送到链上存储。
```
fn offchain_worker(block_number: T::BlockNumber) {
	let signer = Signer::<T, T::AuthorityId>::all_accounts();

	// ...
}
```
调用`Signer<T, C>::all_accounts()`以检索此托盘拥有的所有签名者。您稍后将（在第 9 步中）将一个帐户注入此托盘以进行此检索。

用于send_signed_transaction()发送外部调用：
```
fn offchain_worker(block_number: T::BlockNumber) {
	let signer = Signer::<T, T::AuthorityId>::all_accounts();

	// Using `send_signed_transaction` associated type we create and submit a transaction
	// representing the call we've just created.
	// `send_signed_transaction()` return type is `Option<(Account<T>, Result<(), ()>)>`. It is:
	//	 - `None`: no account is available for sending transaction
	//	 - `Some((account, Ok(())))`: transaction is successfully sent
	//	 - `Some((account, Err(())))`: error occurred when sending the transaction
	let results = signer.send_signed_transaction(|_account| {
		Call::on_chain_call { key: val }
	});

	// ...
}
```
检查交易是否在链上成功提交，并通过检查返回的results.
```
fn offchain_worker(block_number: T::BlockNumber) {
	// ...

	for (acc, res) in &results {
		match res {
			Ok(()) => log::info!("[{:?}]: submit transaction success.", acc.id),
			Err(e) => log::error!("[{:?}]: submit transaction failure. Reason: {:?}", acc.id, e),
		}
	}

	Ok(())
}
```
让您的托盘能够进行交易
在运行时实现CreateSignedTransaction特征。

因为您Config为此托盘配置了特征以实现该CreateSignedTransaction特征，所以您还需要为运行时实现该特征。

通过查看CreateSignedTransactionRust 文档，您可以看到您只需要create_transaction()为运行时实现该功能。在runtime/src/lib.rs：
```
impl<LocalCall> frame_system::offchain::CreateSignedTransaction<LocalCall> for Runtime
where
	Call: From<LocalCall>,
{
	fn create_transaction<C: frame_system::offchain::AppCrypto<Self::Public, Self::Signature>>(
		call: Call,
		public: <Signature as sp_runtime::traits::Verify>::Signer,
		account: AccountId,
		index: Index,
	) -> Option<(Call, <UncheckedExtrinsic as sp_runtime::traits::Extrinsic>::SignaturePayload)> {
		let period = BlockHashCount::get() as u64;
		let current_block = System::block_number()
			.saturated_into::<u64>()
			.saturating_sub(1);
		let tip = 0;
		let extra: SignedExtra = (
			frame_system::CheckSpecVersion::<Runtime>::new(),
			frame_system::CheckTxVersion::<Runtime>::new(),
			frame_system::CheckGenesis::<Runtime>::new(),
			frame_system::CheckEra::<Runtime>::from(generic::Era::mortal(period, current_block)),
			frame_system::CheckNonce::<Runtime>::from(index),
			frame_system::CheckWeight::<Runtime>::new(),
			pallet_transaction_payment::ChargeTransactionPayment::<Runtime>::from(tip),
		);

		let raw_payload = SignedPayload::new(call, extra)
			.map_err(|e| {
				log::warn!("Unable to create signed payload: {:?}", e);
			})
			.ok()?;
		let signature = raw_payload.using_encoded(|payload| C::sign(payload, public))?;
		let address = account;
		let (call, extra, _) = raw_payload.deconstruct();
		Some((call, (sp_runtime::MultiAddress::Id(address), signature.into(), extra)))
	}
}
```
上面的代码看起来很长，但实际上它试图做的是：

创建和准备类型extra，SignedExtra并放置各种检查器。
根据传入的call和创建原始有效负载extra。
使用帐户公钥对原始有效负载进行签名。
最后，捆绑所有数据并返回调用、调用者、其签名和任何签名扩展数据的元组。
您可以在Substrate代码库中查看完整的代码示例。

实现SigningTypes并SendTransactionTypes在运行时支持提交事务，无论它们是签名的还是未签名的。
```
impl frame_system::offchain::SigningTypes for Runtime {
	type Public = <Signature as sp_runtime::traits::Verify>::Signer;
	type Signature = Signature;
}

impl<C> frame_system::offchain::SendTransactionTypes<C> for Runtime
where
	Call: From<C>,
{
	type OverarchingCall = Call;
	type Extrinsic = UncheckedExtrinsic;
}
```
您可以在Substrate代码库中看到此实现的示例。

为该托盘注入一个帐户以拥有。在开发环境中（使用--dev标志运行的节点），此帐户密钥插入node/src/service.rs文件中，如下所示：
```
pub fn new_partial(config: &Configuration) -> Result <SomeStruct, SomeError> {

	//...

	if config.offchain_worker.enabled {
	// Initialize seed for signing transaction using offchain workers. This is a convenience
	// so learners can see the transactions submitted simply running the node.
	// Typically these keys should be inserted with RPC calls to `author_insertKey`.
	sp_keystore::SyncCryptoStore::sr25519_generate_new(
		&*keystore,
		node_template_runtime::pallet_your_ocw_pallet::KEY_TYPE,
		Some("//Alice"),
	).expect("Creating key with account Alice should succeed.");
	}
}
```
有关工作示例，请参阅此文件。此示例将Alice帐户的密钥添加到由托盘定义的密钥库中KEY_TYPE。在生产中，通过链规范配置注入一个或多个帐户。

现在，您的托盘已准备好从链下工作人员发送已签名的交易。

发送未签名的交易
默认情况下，所有未签名的交易都会在 Substrate 中被拒绝。要使 Substrate 能够接受某些未签名的交易，您必须ValidateUnsigned为托盘实现 trait。

在文本编辑器中打开src/lib.rs托盘文件。
```
#[pallet::validate_unsigned]
impl<T: Config> ValidateUnsigned for Pallet<T> {
	type Call = Call<T>;

		/// Validate unsigned call to this module.
		///
		/// By default unsigned transactions are disallowed, but implementing the validator
		/// here we make sure that some particular calls (the ones produced by offchain worker)
		/// are being whitelisted and marked as valid.
		fn validate_unsigned(source: TransactionSource, call: &Self::Call) -> TransactionValidity {
		//...
		}
}
```
调用validate_unsignedPallet 宏，然后按如下方式实现 trait：
```
fn validate_unsigned(source: TransactionSource, call: &Self::Call) -> TransactionValidity {
	let valid_tx = |provide| ValidTransaction::with_tag_prefix("my-pallet")
		.priority(UNSIGNED_TXS_PRIORITY) // please define `UNSIGNED_TXS_PRIORITY` before this line
		.and_provides([&provide])
		.longevity(3)
		.propagate(true)
		.build();
	// ...
}
```
接下来，检查调用外部函数以确定是否允许调用。ValidTransaction如果允许调用则返回，如果不允许调用则返回TransactionValidityError。
```
fn validate_unsigned(source: TransactionSource, call: &Self::Call) -> TransactionValidity {
	// ...
	match call {
		Call::extrinsic1 { key: value } => valid_tx(b"extrinsic1".to_vec()),
		_ => InvalidTransaction::Call.into(),
	}
}
```
在这个例子中，用户可以在没有签名的情况下调用链上extrinsic1函数，但不能调用任何其他外部函数。

ValidateUnsigned要查看如何在托盘中实现的完整示例，pallet-example-offchain-worker请参阅Substrate。

在 offchain worker 函数中，您可以发送未签名的交易，如下所示：
```
#[pallet::hooks]
impl<T: Config> Hooks<BlockNumberFor<T>> for Pallet<T> {
	/// Offchain worker entry point.
	fn offchain_worker(block_number: T::BlockNumber) {
		let value: u64 = 10;
		// This is your call to on-chain extrinsic together with any necessary parameters.
		let call = Call::unsigned_extrinsic1 { key: value };

		// `submit_unsigned_transaction` returns a type of `Result<(), ()>`
		//	 ref: https://paritytech.github.io/substrate/master/frame_system/offchain/struct.SubmitTransaction.html
		SubmitTransaction::<T, Call<T>>::submit_unsigned_transaction(call.into())
			.map_err(|_| {
			log::error!("Failed in offchain_unsigned_tx");
		});
	}
}
```
此代码let call = ...在行中准备调用，使用 提交事务SubmitTransaction::submit_unsigned_transaction，并在传入的回调函数中执行任何必要的错误处理。

通过将类型添加到宏中，在运行时启用ValidateUnsigned托盘的特征。ValidateUnsignedconstruct_runtime

例如：
```
construct_runtime!(
	pub enum Runtime where
		Block = Block,
		NodeBlock = opaque::Block,
		UncheckedExtrinsic = UncheckedExtrinsic
	{
		// ...
		OcwPallet: pallet_ocw::{Pallet, Call, Storage, Event<T>, ValidateUnsigned},
	}
);
```
SendTransactionTypes如发送签名交易中所述，为运行时实现特征。

pallet-example-offchain-worker您可以在Substrate代码库中查看完整示例。

发送带有签名负载的未签名交易
发送带有签名负载的未签名交易类似于发送未签名交易。你需要：

实现ValidateUnsigned托盘的特征。
ValidateUnsigned使用此托盘时将类型添加到运行时。
SignedPayload通过实现trait来准备要签名的数据结构——签名的有效负载。
使用签名的有效负载发送交易。
您可以参考关于发送未签名事务的部分，以获取有关实现ValidateUnsigned特征并将ValidateUnsigned类型添加到运行时的更多信息。以下代码示例说明了发送未签名交易和发送带有签名有效负载的未签名交易之间的区别。

要使您的数据结构可签名，请实现SignedPayloadtrait。

例如：
```
#[derive(Encode, Decode, Clone, PartialEq, Eq, RuntimeDebug, scale_info::TypeInfo)]
pub struct Payload<Public> {
	number: u64,
	public: Public,
}

impl<T: SigningTypes> SignedPayload<T> for Payload<T::Public> {
	fn public(&self) -> T::Public {
	self.public.clone()
}
}
```
您还可以在此处查看示例。

在您的托盘offchain_worker函数中，调用签名者，然后调用发送交易的函数：
```
#[pallet::hooks]
impl<T: Config> Hooks<BlockNumberFor<T>> for Pallet<T> {
	/// Offchain worker entry point.
	fn offchain_worker(block_number: T::BlockNumber) {
		let value: u64 = 10;

		// Retrieve the signer to sign the payload
		let signer = Signer::<T, T::AuthorityId>::any_account();

		// `send_unsigned_transaction` is returning a type of `Option<(Account<T>, Result<(), ()>)>`.
		//	 The returned result means:
		//	 - `None`: no account is available for sending transaction
		//	 - `Some((account, Ok(())))`: transaction is successfully sent
		//	 - `Some((account, Err(())))`: error occurred when sending the transaction
		if let Some((_, res)) = signer.send_unsigned_transaction(
			// this line is to prepare and return payload
			|acct| Payload { number, public: acct.public.clone() },
			|payload, signature| Call::some_extrinsics { payload, signature },
		) {
			match res {
				Ok(()) => log::info!("unsigned tx with signed payload successfully sent.");
				Err(()) => log::error!("sending unsigned tx with signed payload failed.");
			};
		} else {
			// The case of `None`: no account is available for sending
			log::error!("No local account available");
		}
	}
}
```
此代码使用两个函数闭包检索signerthen 调用。send_unsigned_transaction()第一个函数闭包返回要使用的有效负载。第二个函数闭包返回带有有效负载和签名的链上调用。此调用返回一个`Option<(Account<T>, Result<(), ()>)>`结果类型以允许以下结果：

None如果没有可用于发送交易的帐户。
Some((account, Ok(())))如果交易成功发送。
Some((account, Err(())))如果发送交易时发生错误。
对于更复杂的实现ValidateUnsigned，请检查提供的签名是否与用于签署有效负载的公钥匹配：
```
#[pallet::validate_unsigned]
impl<T: Config> ValidateUnsigned for Pallet<T> {
	type Call = Call<T>;

	fn validate_unsigned(_source: TransactionSource, call: &Self::Call) -> TransactionValidity {
		let valid_tx = |provide| ValidTransaction::with_tag_prefix("ocw-demo")
			.priority(UNSIGNED_TXS_PRIORITY)
			.and_provides([&provide])
			.longevity(3)
			.propagate(true)
			.build();

		match call {
			Call::unsigned_extrinsic_with_signed_payload {
			ref payload,
			ref signature
			} => {
			if !SignedPayload::<T>::verify::<T::AuthorityId>(payload, signature.clone()) {
				return InvalidTransaction::BadProof.into();
			}
			valid_tx(b"unsigned_extrinsic_with_signed_payload".to_vec())
			},
			_ => InvalidTransaction::Call.into(),
		}
	}
}
```
此示例用于SignedPayload验证有效负载中的公钥是否与提供的签名具有相同的签名。

有关上述工作示例，请参阅链外函数调用和实现。ValidateUnsigned

您现在已经了解了如何使用链下工作者发送数据以进行链上存储，使用：

签署的交易
未签名的交易
带有签名有效负载的未签名交易
例子
Substrate Offchain Worker 示例托盘
Substrate Offchain Worker 演示中的 OCW Pallet
相关资料
链下功能
