# 在自定义托盘中使用宏

本教程说明了如何使用作为[FRAME](https://docs.substrate.io/reference/frame-macros/)开发环境一部分的宏为 Substrate 运行时创建自定义托盘。

在本教程中，您将构建一个简单的存在证明应用程序。存在证明是一种通过将有关对象的信息存储在区块链上来验证数字对象的真实性和所有权的方法。因为区块链将时间戳​​和帐户与对象相关联，所以区块链记录可用于“证明”特定对象在特定日期和时间存在。它还可以验证记录的所有者在该日期和时间是谁。

## 数字对象和哈希

与其将整个文件存储在区块链上，不如简单地存储该文件的[加密哈希值](https://en.wikipedia.org/wiki/Cryptographic_hash_function)会更有效。这也称为“数字指纹”。哈希使区块链能够通过使用小而唯一的哈希值有效地存储任意大小的文件。因为对文件的任何更改都会导致不同的哈希值，用户可以通过计算哈希值并将该哈希值与存储在链上的哈希值进行比较来证明文件的有效性。


<!-- ![文件哈希]() -->

## 数字对象和帐户签名
区块链使用[公钥加密技术](https://en.wikipedia.org/wiki/Public-key_cryptography)将数字身份映射到拥有私钥的账户。区块链记录您用于存储数字对象哈希的帐户，作为交易的一部分。由于账户信息是作为交易的一部分存储的，因此该账户的私钥的控制者可以稍后证明其所有权是最初上传文件的人。

## 完成本教程需要多少时间？
本教程需要编译 Rust 代码，大约需要一到两个小时才能完成。

## 在你开始之前
对于本教程，您将下载并使用工作代码。在开始之前，请验证以下内容：

- 通过安装[Rust 和 Rust 工具链](https://docs.substrate.io/install/)，您已经为 Substrate 开发配置了环境。
- 您已完成[构建本地区块链](https://docs.substrate.io/tutorials/get-started/build-local-blockchain/)并在本地安装了 Substrate 节点模板。
- 您已使用[模拟网络](https://docs.substrate.io/tutorials/get-started/simulate-network/)中所述的预定义帐户在单台计算机上启动节点。
- 您通常熟悉软件开发并使用命令行界面。

## 教程目标
通过完成本教程，您将实现以下目标：

了解定制托盘的基本结构。
查看 Rust 宏如何简化您需要编写的代码的示例。
启动一个包含自定义托盘的区块链节点。
添加暴露存在证明托盘的前端代码。

## 设计应用程序
存在证明应用程序公开了以下可调用函数：

create_claim()允许用户通过上传哈希来声明文件的存在。
revoke_claim()允许索赔的当前所有者撤销所有权。

## 构建自定义托盘
Substrate 节点模板具有基于 FRAME 的运行时。正如您在[运行时开发](https://docs.substrate.io/fundamentals/runtime-development/)中了解到的，FRAME 是一个代码库，它允许您通过组合称为托盘的模块来构建 Substrate 运行时。您可以将托盘视为定义区块链可以做什么的专用逻辑单元。Substrate 为您提供了许多用于基于 FRAME 的运行时的预构建托盘。


<!-- ![运行时组合]() -->

本教程演示了如何创建您自己的 FRAME 托盘以包含在您的自定义区块链中。

### 为您的托盘搭建脚手架
本教程演示如何从头开始创建自定义托盘。因此，第一步是从节点模板目录中的文件中删除一些文件和内容。

1. 打开终端外壳并导航到节点模板的根目录。

2. pallets/template/src通过运行以下命令切换到目录：

```
cd pallets/template/src
```

3. 删除以下文件：
```
benchmarking.rs
mock.rs
tests.rs
```
4. lib.rs在文本编辑器中打开文件。

此文件包含可用作新托盘模板的代码。您不会在本教程中使用模板代码。但是，您可以在删除模板代码之前查看它提供的内容。

5. 删除文件中的所有行lib.rs。
6. 添加构建本机 Rust 二进制文件 ( std) 和 WebAssembly ( no_std) 二进制文件所需的宏。
```
#![cfg_attr(not(feature = "std"), no_std)]
```

运行时中使用的所有托盘都必须设置为与no_std功能一起编译。

7. 通过复制以下代码，添加自定义托盘所需的托盘依赖项和[宏](https://docs.substrate.io/reference/frame-macros/)的框架集：
```rust
// Re-export pallet items so that they can be accessed from the crate namespace.
pub use pallet::*;

#[frame_support::pallet]
pub mod pallet {
  use frame_support::pallet_prelude::*;
  use frame_system::pallet_prelude::*;

  #[pallet::pallet]
  #[pallet::generate_store(pub(super) trait Store)]
  pub struct Pallet<T>(_);

  #[pallet::config]  // <-- Step 2. code block will replace this.
  #[pallet::event]   // <-- Step 3. code block will replace this.
  #[pallet::error]   // <-- Step 4. code block will replace this.
  #[pallet::storage] // <-- Step 5. code block will replace this.
  #[pallet::call]    // <-- Step 6. code block will replace this.
}
```
您现在拥有一个包含事件、错误、存储和可调用函数的占位符的框架。

8. 保存您的更改。

## 配置托盘以发出事件
每个托盘都有一个名为[Config](https://doc.rust-lang.org/book/ch10-02-traits.html). 您可以使用此特征来配置特定托盘所需的设置。对于本教程，配置设置使托盘能够发出事件。

要定义Config存在证明托盘的特征：

1. pallets/template/src/lib.rs在文本编辑器中打开文件。
2. #[pallet::config]用以下代码块替换该行：
```rust
/// Configure the pallet by specifying the parameters and types on which it depends.
#[pallet::config]
pub trait Config: frame_system::Config {
  /// Because this pallet emits events, it depends on the runtime's definition of an event.
  type Event: From<Event<Self>> + IsType<<Self as frame_system::Config>::Event>;
}
```
3. 保存您的更改。

## 实施托盘事件
现在您已将托盘配置为发出事件，您已准备好定义这些事件。如[设计应用程序](https://docs.substrate.io/tutorials/work-with-pallets/use-macros-in-a-custom-pallet/#design-the-application)中所述，存在证明托盘在以下条件下发出事件：
- 当新的声明添加到区块链时。
- 当索赔被撤销时。

每个事件还显示一个以标识谁触发了该事件以及正在存储或删除AccountId的存在证明声明（如）。Hash

要实现托盘事件：

1. pallets/template/src/lib.rs在文本编辑器中打开文件。
2. #[pallet::event]用以下代码块替换该行：
```rust
// Pallets use events to inform users when important changes are made.
// Event documentation should end with an array that provides descriptive names for parameters.
#[pallet::event]
#[pallet::generate_deposit(pub(super) fn deposit_event)]
pub enum Event<T: Config> {
  /// Event emitted when a claim has been created.
  ClaimCreated { who: T::AccountId, claim: T::Hash },
  /// Event emitted when a claim is revoked by the owner.
  ClaimRevoked { who: T::AccountId, claim: T::Hash },
}
```
3. 保存您的更改。

## 包括托盘错误
您定义的事件指示对托盘的调用何时成功完成。错误指示调用何时失败以及失败的原因。对于本教程，您定义以下错误条件：

- 试图提出已经存在的声明。
- 试图撤销不存在的声明。
- 试图撤销由另一个帐户拥有的声明。

要实现存在证明托盘的错误：
1. pallets/template/src/lib.rs在文本编辑器中打开文件。
2. #[pallet::error]用以下代码块替换该行：
```rust
#[pallet::error]
pub enum Error<T> {
  /// The claim already exists.
  AlreadyClaimed,
  /// The claim does not exist, so it cannot be revoked.
  NoSuchClaim,
  /// The claim is owned by another account, so caller can't revoke it.
  NotClaimOwner,
}
```
3. 保存您的更改。

## 为存储的项目实现存储映射
为了向区块链添加新的声明，存在证明托盘需要一个存储机制。为了满足这一要求，您可以创建一个键值映射，其中每个声明都指向所有者和声明发出时的块号。要创建此键值映射，您可以使用 FRAME [StorageMap](https://paritytech.github.io/substrate/master/frame_support/pallet_prelude/struct.StorageMap.html)。

要实现存在证明托盘的存储：

1. pallets/template/src/lib.rs在文本编辑器中打开文件。
2. #[pallet::storage]用以下代码块替换该行：
```rust
#[pallet::storage]
pub(super) type Claims<T: Config> = StorageMap<_, Blake2_128Concat, T::Hash, (T::AccountId, T::BlockNumber)>;
```
3. 保存您的更改。

## 实现可调用函数

存在证明托盘向用户公开了两个可调用函数：
- create_claim()允许用户使用散列声明文件的存在。
- revoke_claim()允许索赔的所有者撤销索赔。
这些函数使用StorageMap来实现以下逻辑：
- 如果声明已在存储中，则它已经拥有所有者并且不能再次声明。
- 如果声明不在存储中，则可以声明并写入存储。
要在存在证明托盘中实现此逻辑：
1. pallets/template/src/lib.rs在文本编辑器中打开文件。
2.  #[pallet::call]用以下代码块替换该行：
```rust
// Dispatchable functions allow users to interact with the pallet and invoke state changes.
// These functions materialize as "extrinsics", which are often compared to transactions.
// Dispatchable functions must be annotated with a weight and must return a DispatchResult.
#[pallet::call]
impl<T: Config> Pallet<T> {
  #[pallet::weight(0)]
  pub fn create_claim(origin: OriginFor<T>, claim: T::Hash) -> DispatchResult {
    // Check that the extrinsic was signed and get the signer.
    // This function will return an error if the extrinsic is not signed.
    let sender = ensure_signed(origin)?;

    // Verify that the specified claim has not already been stored.
    ensure!(!Claims::<T>::contains_key(&claim), Error::<T>::AlreadyClaimed);

    // Get the block number from the FRAME System pallet.
    let current_block = <frame_system::Pallet<T>>::block_number();

    // Store the claim with the sender and block number.
    Claims::<T>::insert(&claim, (&sender, current_block));

    // Emit an event that the claim was created.
    Self::deposit_event(Event::ClaimCreated { who: sender, claim });

    Ok(())
  }

  #[pallet::weight(0)]
  pub fn revoke_claim(origin: OriginFor<T>, claim: T::Hash) -> DispatchResult {
    // Check that the extrinsic was signed and get the signer.
    // This function will return an error if the extrinsic is not signed.
    let sender = ensure_signed(origin)?;

    // Get owner of the claim, if none return an error.
    let (owner, _) = Claims::<T>::get(&claim).ok_or(Error::<T>::NoSuchClaim)?;

    // Verify that sender of the current call is the claim owner.
    ensure!(sender == owner, Error::<T>::NotClaimOwner);

    // Remove claim from storage.
    Claims::<T>::remove(&claim);

    // Emit an event that the claim was erased.
    Self::deposit_event(Event::ClaimRevoked { who: sender, claim });
    Ok(())
  }
}
```
3. 保存更改并关闭文件。
4. 通过运行以下命令检查您的代码是否编译：
```shell
cargo check -p node-template-runtime
```

卡住可以参考节点模板[解决方案](https://github.com/substrate-developer-hub/substrate-node-template/blob/tutorials/solutions/proof-of-existence/pallets/template/src/lib.rs)。

## 使用新托盘构建运行时
将存在证明托盘的所有部分复制到pallets/template/lib.rs文件中后，就可以编译并启动节点了。

编译并启动更新的 Substrate 节点：
1. 打开终端外壳。
2. 切换到节点模板的根目录。
3. 通过运行以下命令编译节点模板：
```shell
cargo build --release
```
4. 通过运行以下命令以开发模式启动节点：
```shell
./target/release/node-template --dev
```
该选项使用预定义的链规范--dev启动节点。development使用该--dev选项可确保您在任何时候停止和重新启动节点时都处于干净的工作状态。
5. 验证节点产生块。

## 与您的区块链交互
现在您有了一个使用自定义存在证明托盘运行的新区块链，我们可以与该链交互以确保所有功能都按预期工作！

为此，我们将使用 Polkadot JS 应用程序，它是一种开发工具，可以连接到任何基于 Substrate 的区块链并与之交互。

默认情况下，您的区块链应该在 上运行`ws://127.0.0.1:9944`，因此要连接到它，我们可以使用此链接：

https://polkadot.js.org/apps/?rpc=ws%3A%2F%2F127.0.0.1%3A9944#/

如果您的 Substrate 区块链正在运行并且 Polkadot JS 应用程序已连接，您应该会在左上角看到您的块数增加：


<!-- ![Polkadot JS 浏览器]() -->

## 提交索赔
使用前端测试存在证明托盘：

1. 导航到“[开发人员 > 外部](https://polkadot.js.org/apps/?rpc=ws%3A%2F%2F127.0.0.1%3A9944#/extrinsics)”选项卡。

<!-- ![外部选项卡]() -->

2. 调整extrinsics页面，选择“ALICE”作为account，“templateModule > createClaim”作为extrinsic。


<!-- ![创建声明]() -->
3. 然后您可以切换“散列文件”，这将允许您选择要在区块链上散列和声明的文件。


<!-- ![哈希文件]() -->
4. 点击右下角的“提交交易”，然后在弹出的窗口中点击“签名并提交”。


<!-- ![提交外部]() -->
5. 如果一切顺利，您应该会看到绿色的外部成功通知！


<!-- ![外在的成功]() -->

## 阅读索赔
本教程的最后一步是检查您的区块链上存储了哪些声明。

1. 导航到“[开发人员 > 链状态](https://polkadot.js.org/apps/?rpc=ws%3A%2F%2F127.0.0.1%3A9944#/chainstate)”选项卡。


<!-- ![连锁状态]() -->
2. 将状态查询调整为“templateModule > claim”。
3. 关闭散列输入上的“包含选项”以将输入留空。

这将使我们能够看到所有的声明，而不是一次只看到一个。


<!-- ![查询所有索赔]() -->
4. 按“+”按钮执行查询。


<!-- ![查询结果]() -->

现在您可以看到索赔存储在区块链中，其中包含有关所有者地址和提出索赔时的区块号的数据！

## 下一步
在本教程中，您学习了如何创建新的自定义托盘的基础知识，包括：

- 如何将事件、错误、存储和可调用函数添加到自定义托盘。
- 如何将自定义托盘集成到运行时。
- 如何编译和启动包含自定义托盘的节点。
- 如何使用 Polkadot JS Apps 开发者工具与您的自定义区块链进行交互。
本教程涵盖了基础知识，但并未深入研究代码。但是，当您努力构建自己的完全定制的区块链时，您还可以做更多的事情。自定义托盘使您能够公开您希望区块链支持的功能。

要完成对存在证明链的理解，请尝试：

- 使用“ALICE”甚至“BOB”帐户再次声明相同的文件。
  - 你应该得到一个错误！
- 使用“ALICE”和/或“BOB”帐户声明其他文件。
- 使用适当的声明所有者帐户撤销声明。
- 查看来自读取存储的最终声明列表。
要了解有关创建自定义托盘的可能性的更多信息，请浏览 FRAME 文档和[操作指南](https://docs.substrate.io/reference/how-to-guides/)。
