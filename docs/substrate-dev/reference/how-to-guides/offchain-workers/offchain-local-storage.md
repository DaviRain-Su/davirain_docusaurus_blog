# 链下本地存储
本指南将教您如何使用脱链工作者将检索到的数据保存在本地存储中以供将来访问。

在上一节中，我们提到了链下工作者（简称OCW）不能直接修改区块链状态，因此他们必须提交交易才能将计算结果保存回链上。尽管如此，有时数据不适合保存在链上，但仍需要存储在某个地方以供将来访问。这包括计算完成后可以丢弃的临时数据或中间计算。

在本指南中，我们将指导链下工作人员将数据写入本地存储，而无需在整个区块链网络之间传递数据。引入存储锁的概念是为了在多个 OCW 之间一致地访问值。OCW 在每个块生产结束时异步运行，并且不受运行时间的限制，因此在任何时间点都可能启动多个 OCW 实例，如下所示。

<图像丢失>
本地存储 API 类似于它们的链上对应物，使用get、set和mutate来访问它们。 mutate正在使用比较和设置模式-它将内存位置的内容与给定值进行比较，并且仅当它们相同时，才将该内存位置的内容修改为新的给定值。这是作为单个原子操作完成的。原子性保证了新值是根据最新信息计算出来的；如果同时该值已被另一个线程更新，则写入将失败。

请注意，由于存储在本地存储中的值没有经过网络之间的共识机制，因此它们受节点运营商的操纵。

在本操作指南中，我们将首先从充当缓存的存储中检查计算值是否存在。如果找到缓存的值，offchain worker 返回；否则它将尝试获取锁，执行密集计算，并将其保存到存储/缓存中。

脚步
offchain_worker在托盘的功能挂钩中定义存储参考：
```
fn offchain_worker(block_number: T::BlockNumber) {
 // Create a reference to Local Storage value.
 // Since the local storage is common for all offchain workers, it's a good practice
 // to prepend our entry with the pallet name.
 let storage = StorageValueRef::persistent(b"pallet::my-storage");
}
```
在上面的代码中，定义了一个持久的本地存储，使用StorageValueRef::persistent()它由pallet::my-storagekey 标识。键是字节数组类型而不是str类型。此本地存储在脱链工作人员的运行中被持久化和共享。

检查存储是否包含缓存值。
```
fn offchain_worker(block_number: T::BlockNumber) {
 // ...
 let storage = StorageValueRef::persistent(b"pallet::my-storage");

 if let Ok(Some(res)) = storage.get::<u64>() {
   log::info!("cached result: {:?}", res);
   return Ok(());
 }
}
```
使用`get<T: Decode>()`函数获取结果，返回一个`Result<Option<T>, StorageRetrievalError>`类型。我们只关心具有有效值的情况。如果是，则返回Ok(())。注意我们还需要定义返回值的类型。

用于set()写入存储以及`mutate<T, E, F>()`以原子方式读取和更改存储。

如果没有有效值 ( None) 或有StorageRetrievalError，则继续计算所需的结果并获取存储锁。

首先定义存储锁如下。
```
const LOCK_BLOCK_EXPIRATION: u32 = 3; // in block number
const LOCK_TIMEOUT_EXPIRATION: u64 = 10000; // in milli-seconds

fn offchain_worker(block_number: T::BlockNumber) {
 // ...
 let storage = StorageValueRef::persistent(b"pallet::my-storage");

 if let Ok(Some(res)) = storage.get::<u64>() {
   log::info!("cached result: {:?}", res);
   return Ok(());
 }

 // Very intensive computation here
 let val: u64 = 100 + 100;

 // Define the storage lock
 let mut lock = StorageLock::<BlockAndTime<Self>>::with_block_and_time_deadline(
   b"pallet::storage-lock",
   LOCK_BLOCK_EXPIRATION,
   Duration::from_millis(LOCK_TIMEOUT_EXPIRATION)
 );
}
```
在上面的代码片段中，定义了一个存储锁，同时指定了块和时间期限。该函数接受锁标识符、块号到期和时间到期。上述锁在超过指定数量的块数或持续时间时过期。我们还可以仅使用块号或持续时间来指定到期期限。

使用 获取存储锁.try_lock()。
```
fn offchain_worker(block_number: T::BlockNumber) {
 // ...

 let mut lock = /* .... */;

 if let Ok(_guard) = lock.try_lock() {
   storage.set(&val);
 }
}
```
它返回`Result<StorageLockGuard<'a, '_, L>, <L as Lockable>::Deadline>`。这里的机制是首先获得锁守卫，在写入存储之前一次只能由一个进程持有。然后使用 将值写入存储set()。传入的值的数据类型set()必须与上面在`get<T>()`调用中指定的类型相同。

最后，从链下工作函数返回。

完整的代码类似于以下内容：
```
const LOCK_BLOCK_EXPIRATION: u32 = 3; // in block number
const LOCK_TIMEOUT_EXPIRATION: u64 = 10000; // in milli-seconds

fn offchain_worker(block_number: T::BlockNumber) {
 let storage = StorageValueRef::persistent(b"pallet::my-storage");

 if let Ok(Some(res)) = storage.get::<u64>() {
   log::info!("cached result: {:?}", res);
   return Ok(());
 }

 // Very intensive computation here
 let val: u64 = 100 + 100;

 // Define the storage lock
 let mut lock = StorageLock::<BlockAndTime<Self>>::with_block_and_time_deadline(
   b"pallet::storage-lock",
   LOCK_BLOCK_EXPIRATION,
   Duration::from_millis(LOCK_TIMEOUT_EXPIRATION)
 );

 if let Ok(_guard) = lock.try_lock() {
   storage.set(&val);
 }
 Ok(())
}
```
例子
Substrate 中的链下工人示例托盘
OCW 托盘演示