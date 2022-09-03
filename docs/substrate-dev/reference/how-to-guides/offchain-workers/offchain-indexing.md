# 链下索引
本指南将引导您了解如何在不写入存储的情况下将数据从外部传递到链下工作人员。

有时，链上外部需要将数据传递给具有可预测写入行为的链下工作人员。这些数据可以写入链上存储，供链下工作人员读取，但这可能会给区块链带来巨大的成本。将数据从链上世界传递到链下世界的另一种方法是通过链下索引将其保存在节点的本地存储中。

链下索引是从链上外部调用的，这意味着本地写入的数据预计将在网络中的所有节点之间保持一致。

另一个用例是需要在链上存储大量数据以供链下工作人员处理。这太贵了。解决方案是使用链下索引在链上存储该数据的哈希值，并将相应的原始数据存储在本地以供链下工作者稍后读取。

请注意，当有分叉块时，可能会多次运行相同的外部程序。结果是，如果使用非唯一密钥，数据可能会被不同的分叉块覆盖，并且本地存储中的内容在节点之间会有所不同。因此，开发人员在形成正确的索引键时应该小心，以防止潜在的覆盖。

注意：为了查看链下索引功能的实际效果，请使用链下索引标志ON运行您的 Substrate 节点。例如：
```
./target/release/substrate-node --enable-offchain-indexing true
```

脚步
创建用于索引的唯一键。

在src/lib.rs您的托盘中：
```
const ONCHAIN_TX_KEY: &[u8] = b"my_pallet::indexing1";

#[pallet::call]
impl<T: Config> Pallet<T> {
	#[pallet::weight(100)]
	pub fn extrinsic(origin: OriginFor<T>, number: u64) -> DispatchResult {
		let who = ensure_signed(origin)?;

		let key = Self::derived_key(frame_system::Module::<T>::block_number());
		// ...

		Ok(())
	}
}

impl<T: Config> Pallet<T> {
	fn derived_key(block_number: T::BlockNumber) -> Vec<u8> {
		block_number.using_encoded(|encoded_bn| {
			ONCHAIN_TX_KEY.clone().into_iter()
				.chain(b"/".into_iter())
				.chain(encoded_bn)
				.copied()
				.collect::<Vec<u8>>()
		})
	}
}
```
在常规外部代码中的上述代码中，Self::derived_key()调用了辅助方法以生成稍后用于索引的键。它将预定义的前缀与当前编码的块号连接起来，并将其作为字节向量返回。

定义索引数据并使用链下索引保存它：
```
use sp_io::offchain_index;
const ONCHAIN_TX_KEY: &[u8] = b"my_pallet::indexing1";

#[derive(Debug, Deserialize, Encode, Decode, Default)]
struct IndexingData(Vec<u8>, u64);

#[pallet::call]
impl<T: Config> Pallet<T> {
	#[pallet::weight(100)]
	pub fn extrinsic(origin: OriginFor<T>, number: u64) -> DispatchResult {
		let who = ensure_signed(origin)?;

		let key = Self::derived_key(frame_system::Module::<T>::block_number());
		let data = IndexingData(b"submit_number_unsigned".to_vec(), number);
		offchain_index::set(&key, &data.encode());
		Ok(())
	}
}

impl<T: Config> Pallet<T> {
	// -- skipped for brevity --
}
```
索引数据可以是任何可以由 、 和 特征绑定的Encode数据Decode类型Deserialize。在上面的代码中，数据是使用该offchain_index::set()方法通过链下索引存储的。

使用offchain_workerhook 方法读取链下工人数据库中的数据：
```
use sp_runtime::offchain::StorageValueRef;

#[derive(Debug, Deserialize, Encode, Decode, Default)]
struct IndexingData(Vec<u8>, u64);

fn offchain_worker(block_number: T::BlockNumber) {
	// Reading back the offchain indexing value. This is exactly the same as reading from
	// ocw local storage.
	let key = Self::derived_key(block_number);
	let storage_ref = StorageValueRef::persistent(&key);

	if let Ok(Some(data)) = storage_ref.get::<IndexingData>() {
		debug::info!("local storage data: {:?}, {:?}",
			str::from_utf8(&data.0).unwrap_or("error"), data.1);
	} else {
		debug::info!("Error reading from local storage.");
	}

	// -- snip --
}
```
有了这个，链下工作者可以从节点的本地存储中读取相应的数据。Offchain本地存储操作指南解释了如何做到这一点。

相关资料
链下运营
链下本地存储
