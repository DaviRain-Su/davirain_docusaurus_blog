# 将托盘添加到运行时
正如您在构建本地区块链中看到的那样，Substrate 节点模板提供了一个工作运行时，其中包括一些默认的 FRAME 开发模块（托盘），以帮助您开始构建自定义区块链。

本教程介绍了将新托盘添加到节点模板的运行时的基本步骤。每当您想向运行时添加新的 FRAME 托盘时，这些步骤都是类似的。但是，每个托盘都需要特定的配置设置——例如，执行托盘实现的功能所需的特定参数和类型。在本教程中，您会将Nicks 托盘添加到节点模板的运行时，因此您将了解如何配置特定于 Nicks 托盘的设置。Nicks 托盘允许区块链用户支付押金，为他们控制的账户保留昵称。它实现了以下功能：

set_name收取存款并设置帐户名称（如果尚未使用该名称）的功能。
clear_name删除与帐户关联的名称并退回押金的功能。
kill_name强制删除账户名而不退还押金的功能。
请注意，本教程是更高级教程的垫脚石，这些教程说明了如何添加具有更复杂配置设置的托盘、如何创建自定义托盘以及如何发布托盘。

在你开始之前
在开始之前，请验证以下内容：

通过安装Rust 和 Rust 工具链，您已经为 Substrate 开发配置了环境。
您已完成构建本地区块链polkadot-v0.9.26教程并在本地安装了来自 Developer Hub 分支的 Substrate 节点模板。
您通常熟悉软件开发和使用命令行界面。
您通常熟悉区块链和智能合约平台。
教程目标
通过完成本教程，您将使用 Nicks 托盘来完成以下目标：

了解如何更新运行时依赖项以包含新托盘。
了解如何配置特定于托盘的 Rust 特征。
通过使用前端模板与新托盘交互来查看运行时的更改。
添加 Nicks 托盘依赖项
在您可以使用新托盘之前，您必须将一些关于它的信息添加到编译器用来构建运行时二进制文件的配置文件中。

对于 Rust 程序，您使用该Cargo.toml文件来定义配置设置和依赖项，这些设置和依赖项决定了在生成的二进制文件中编译的内容。因为 Substrate 运行时编译为包含标准库 Rust 函数的本机平台二进制文件和不包含标准 Rust 库的WebAssembly (Wasm)Cargo.toml二进制文件，所以该文件控制两个重要信息：

要作为运行时依赖项导入的托盘，包括要导入的托盘的位置和版本。
编译原生 Rust 二进制文件时应启用的每个托盘中的功能。通过从每个托盘启用标准 ( std) 功能集，您可以编译运行时以包含在构建 WebAssembly 二进制文件时会丢失的函数、类型和原语。
有关在Cargo.toml文件中添加依赖项的信息，请参阅Cargo 文档中的依赖项。有关从依赖包启用和管理功能的信息，请参阅Cargo 文档中的功能。

要将 Nicks 托盘的依赖项添加到运行时：

打开终端外壳并切换到节点模板的根目录。
runtime/Cargo.toml在文本编辑器中打开配置文件。
找到 [dependencies] 部分并注意如何导入其他托盘。
复制现有的托盘依赖描述并将托盘名称替换为pallet-nicks以使托盘可用于节点模板运行时。

例如，添加类似于以下内容的行：
```toml
pallet-nicks = { version = "4.0.0-dev", default-features = false, git = "https://github.com/paritytech/substrate.git", branch = "polkadot-v0.9.26" }
```
此行将pallet-nickscrate 作为依赖项导入并指定以下内容：

版本标识您要导入的 crate 的哪个版本。
使用标准 Rust 库编译运行时时包含托盘功能的默认行为。
用于检索pallet-nickscrate 的存储库位置。
用于检索 crate 的分支。
对于任何给定版本的节点模板中的每个托盘，这些详细信息都应该相同。

在编译运行时将功能添加pallet-nicks/std到要启用的列表中。features
```toml
[features]
default = ["std"]
std = [
  ...
  "pallet-aura/std",
  "pallet-balances/std",
  "pallet-nicks/std",
  ...
]
```
本节指定要为此运行时编译的默认功能集是std功能集。使用std功能集编译运行时时，std将启用所有列为依赖项的托盘的功能。有关如何使用标准 Rust 库将运行时编译为平台原生二进制文件以及使用该no_std属性编译为 WebAssembly 二进制文件的更多详细信息，请参阅构建过程。

如果您忘记更新文件features中的部分，您可能会在编译运行时二进制文件时Cargo.toml看到错误。cannot find function

通过运行以下命令检查新依赖项是否正确解析：
```shell
cargo check -p node-template-runtime
```
查看余额的配置
每个托盘都有一个名为Config. 该Config特征用于识别托盘执行其功能所需的参数和类型。

添加托盘所需的大多数特定于托盘的代码都是使用Configtrait 实现的。您可以通过参考其 Rust 文档或托盘的源代码来查看您需要为任何托盘实现的内容。例如，要查看您需要为nicks托盘实现什么，您可以参考 Rust 文档或Nicks 托盘源代码pallet_nicks::Config中的特征定义。

对于本教程，您可以看到托盘中的Configtraitnicks声明了以下类型：
```rust
pub trait Config: Config {
    type Event: From<Event<Self>> + IsType<<Self as Config>::Event>;
    type Currency: ReservableCurrency<Self::AccountId>;
    type ReservationFee: Get<<<Self as Config>::Currency as Currency<<Self as Config>::AccountId>>::Balance>;
    type Slashed: OnUnbalanced<<<Self as Config>::Currency as Currency<<Self as Config>::AccountId>>::NegativeImbalance>;
    type ForceOrigin: EnsureOrigin<Self::Origin>;
    type MinLength: Get<u32>;
    type MaxLength: Get<u32>;
}
```
确定您的托盘所需的类型后，您需要将代码添加到运行时以实现该Config特征。要了解如何Config为托盘实现 trait，让我们以Balances托盘为例。

要查看ConfigBalances 托盘的特征：

runtime/src/lib.rs在文本编辑器中打开文件。
找到Balances托盘并注意它由以下实现 ( impl) 代码块组成：
```rust
impl pallet_balances::Config for Runtime {
  type MaxLocks = ConstU32<50>;
  type MaxReserves = ();
  type ReserveIdentifier = [u8; 8];
  /// The type for recording an account's balance.
  type Balance = Balance;
  /// The ubiquitous event type.
  type Event = Event;
  /// The empty value, (), is used to specify a no-op callback function.
  type DustRemoval = ();
  /// Set the minimum balanced required for an account to exist on-chain
  type ExistentialDeposit = ConstU128<500>;
  /// The FRAME runtime system is used to track the accounts that hold balances.
  type AccountStore = System;
  /// Weight information is supplied to the Balances pallet by the node template runtime.
  type WeightInfo = pallet_balances::weights::SubstrateWeight<Runtime>;
}
```
正如您在此示例中所看到的，该块允许您配置由 Balances 托盘特征impl pallet_balances::Config指定的类型和参数。Config例如，此impl块将余额托盘配置为使用u128类型来跟踪余额。

实现 Nicks 的配置
现在您已经看到了如何Config为 Balances 托盘实现特征的示例，您已准备好Config为 Nicks 托盘实现特征。

要nicks在运行时实现托盘：

runtime/src/lib.rs在文本编辑器中打开文件。
找到 Balances 代码块的最后一行。
为 Nicks 托盘添加以下代码块：
```rust
impl pallet_nicks::Config for Runtime {
  // The Balances pallet implements the ReservableCurrency trait.
  // `Balances` is defined in `construct_runtime!` macro.
  type Currency = Balances;

  // Set ReservationFee to a value.
  type ReservationFee = ConstU128<100>;

  // No action is taken when deposits are forfeited.
  type Slashed = ();

  // Configure the FRAME System Root origin as the Nick pallet admin.
  // https://paritytech.github.io/substrate/master/frame_system/enum.RawOrigin.html#variant.Root
  type ForceOrigin = frame_system::EnsureRoot<AccountId>;

  // Set MinLength of nick name to a desired value.
  type MinLength = ConstU32<8>;

  // Set MaxLength of nick name to a desired value.
  type MaxLength = ConstU32<32>;

  // The ubiquitous event type.
  type Event = Event;
}
```
将尼克斯添加到construct_runtime!宏。

例如：
```rust
construct_runtime!(
pub enum Runtime where
   Block = Block,
   NodeBlock = opaque::Block,
   UncheckedExtrinsic = UncheckedExtrinsic
 {
   /* --snip-- */
   Balances: pallet_balances,

   /*** Add This Line ***/
   Nicks: pallet_nicks,
 }
);
```
通过运行以下命令检查新依赖项是否正确解析：
```shell
cargo check -p node-template-runtime
```
如果没有错误，您就可以编译了。

通过运行以下命令以发布模式编译节点：
```shell
cargo build --release
```
启动区块链节点
在您的节点编译后，您就可以启动已通过Nicks 托盘中的昵称功能增强的节点，并使用前端模板与其交互。

启动本地 Substrate 节点：

如有必要，打开终端外壳。
切换到 Substrate 节点模板的根目录。
通过运行以下命令以开发模式启动节点：
```shell
./target/release/node-template --dev
```
在这种情况下，该--dev选项指定节点使用预定义的development链规范在开发者模式下运行。默认情况下，当您按 Control-c 停止节点时，此选项还会删除所有活动数据，例如密钥、区块链数据库和网络信息。使用该--dev选项可确保您在任何时候停止和重新启动节点时都处于干净的工作状态。

通过查看终端中显示的输出来验证您的节点是否已启动并成功运行。

如果finalized控制台输出中的数字在增加，则您的区块链正在生成新块并就它们描述的状态达成共识。

保持显示节点输出的终端打开以继续。
启动前端模板
现在您已经向运行时添加了一个新托盘，您可以使用 Substrate 前端模板与节点模板交互并访问 Nicks 托盘。

启动前端模板：

在您的计算机上打开一个新的终端外壳。
在新终端中，切换到安装前端模板的根目录。
通过运行以下命令启动前端模板的 Web 服务器：
```shell
yarn start
```
在浏览器中打开http://localhost:8000/以查看前端模板。
使用 Nicks 托盘设置昵称
启动前端模板后，您可以使用它与刚刚添加到运行时的 Nicks 托盘进行交互。

为帐户设置昵称：

检查帐户选择列表以验证当前选择了 Alice 帐户。
在 Pallet Interactor 组件中，确认选择了Extrinsic。
nicks从可调用的托盘列表中选择。
选择setName作为要从nicks托盘调用的功能。
键入一个长度超过MinNickLength（8 个字符）且不超过MaxNickLength（32 个字符）的名称。


选择托盘和要调用的功能
单击已签名以执行该功能。
观察呼叫状态从 Ready 到 InBlock 到 Finalized 的变化，并注意Nicks 托盘发出的事件。


成功更新爱丽丝的昵称
使用 Nicks 托盘查询帐户信息
接下来，您可以使用查询功能从 Nicks 托盘的运行时存储中读取 Alice 的昵称值。

要返回为 Alice 存储的信息：

在 Pallet Interactor 组件中，选择Query作为交互类型。
nicks从可查询的托盘列表中选择。
选择nameOf作为要调用的函数。
复制并粘贴aliceAccountId 字段中的帐户地址，然后单击Query。


读一个名字
返回类型是一个包含两个值的元组：

Alice 帐户的十六进制编码昵称53756273747261746520737570657273746172202d20416c696365。
从 Alice 的账户中预留的用于保护昵称 ( 100) 的金额。
如果您要查询nameOfBob 帐户的 Nicks 托盘，您会看到None返回的值，因为 Bob 没有调用setName函数来保留昵称。

探索附加功能
本教程说明如何将一个简单的托盘添加到运行时，并演示如何使用前端模板与新托盘交互。在本例中，您将nicks托盘添加到运行时并使用前端模板调用set_name和函数。托盘还提供了两个附加功能——nameOf函数和函数——使帐户所有者可以删除保留名称或根级别用户可以强制删除帐户名称。您可以通过探索这些功能的工作原理来了解其他功能，例如 Sudo 托盘和原始帐户的使用。但是，这些功能超出了本教程的预期范围。如果您想探索通过 Nicks 和 Sudo 托盘公开的其他功能，请参阅nicksclear_namekill_name下一步并选择指定呼叫来源。

下一步
有几个教程可以作为进一步了解 Substrate 开发的后续步骤。

指定呼叫来源探索使用不同始发帐户的呼叫功能。
配置合同托盘通过将合同托盘添加到运行时演示更复杂的配置要求。
在自定义托盘中使用宏说明了如何使用宏来创建自己的托盘。