# 建立代币合约

本教程说明了如何使用墨水构建 ERC-20 代币合约！语。ERC-20 规范定义了可替代代币的通用标准。拥有定义令牌的属性的标准使遵循规范的开发人员能够构建可以与其他产品和服务互操作的应用程序。

ERC-20 代币标准并不是唯一的代币标准，但却是最常用的一种。

在你开始之前
在开始之前，请验证以下内容：

您通常熟悉智能合约、代币和加密货币概念和术语。
您已经按照安装中的描述安装了 Rust 并设置了您的开发环境。
您已完成准备您的第一个合约并在本地安装了 Substrate 合约节点。
你已经完成了智能合约的开发，并且熟悉了墨迹！使用 Rust 属性宏来构建智能合约。
教程目标
通过完成本教程，您将实现以下目标：

了解 ERC-20 标准中定义的基本属性和接口。
创建符合 ERC-20 标准的代币。
在合约之间转移代币。
处理涉及批准或第三方的转移活动的路由。
创建与令牌活动相关的事件。
ERC-20 标准的基础知识
ERC-20 代币标准定义了在以太坊区块链上运行的大多数智能合约的接口。这些标准接口允许个人在现有的智能合约平台之上部署自己的加密货币。

如果您查看该标准，您会注意到定义了以下核心功能。
```
// ----------------------------------------------------------------------------
// ERC Token Standard #20 Interface
// https://github.com/ethereum/EIPs/blob/master/EIPS/eip-20.md
// ----------------------------------------------------------------------------

contract ERC20Interface {
    // Storage Getters
    function totalSupply() public view returns (uint);
    function balanceOf(address tokenOwner) public view returns (uint balance);
    function allowance(address tokenOwner, address spender) public view returns (uint remaining);

    // Public Functions
    function transfer(address to, uint tokens) public returns (bool success);
    function approve(address spender, uint tokens) public returns (bool success);
    function transferFrom(address from, address to, uint tokens) public returns (bool success);

    // Contract Events
    event Transfer(address indexed from, address indexed to, uint tokens);
    event Approval(address indexed tokenOwner, address indexed spender, uint tokens);
}
```
用户余额映射到账户地址，界面允许用户转移他们拥有的代币或允许第三方代表他们转移代币。最重要的是，必须实施智能合约逻辑以确保资金不会被无意创建或销毁，并且用户的资金不会受到恶意行为者的影响。

请注意，所有公共函数都返回一个bool仅指示调用是否成功的 a。在 Rust 中，这些函数通常会返回一个Result.

创建代币供应
处理 ERC-20 代币的智能合约类似于使用地图存储值的增量器合约，使用地图存储值。对于本教程，ERC-20 合约由固定供应的代币组成，这些代币在部署合约时全部存入与合约所有者关联的账户。然后合约所有者可以将代币分发给其他用户。

您在本教程中创建的简单 ERC-20 合约并不代表您铸造和分发代币的唯一方式。但是，这份 ERC-20 合约为扩展您在其他教程中学到的内容以及如何使用墨水提供了良好的基础！用于构建更强大的智能合约的语言。

对于 ERC-20 代币合约，初始存储包括：

total_supply代表合约中代币的总供应量。
balances代表每个账户的个人余额。
首先，让我们用一些模板代码创建一个新项目。

要构建 ERC-20 代币智能合约：

如果您还没有打开终端外壳，请在本地计算机上打开终端外壳。
erc20通过运行以下命令创建一个名为的新项目：
```shell
cargo contract new erc20
```
通过运行以下命令切换到新项目目录：
```shell
cd erc20/
```
lib.rs在文本编辑器中打开文件。
用新的erc20源代码替换默认模板源代码。
保存对lib.rs文件的更改，然后关闭文件。
在文本编辑器中打开Cargo.toml文件并查看合同的依赖关系。
如有必要，在该[dependencies]部分中修改scale和设置。scale-info
```toml
scale = { package = "parity-scale-codec", version = "3", default-features = false, features = ["derive"] }
scale-info = { version = "2", default-features = false, features = ["derive"], optional = true }
```
保存对Cargo.toml文件的更改，然后关闭文件。
通过运行以下命令来验证程序是否编译并通过了简单的测试
```shell
cargo +nightly test
```
该命令应显示类似于以下内容的输出，以指示测试成功完成：
```shell
running 2 tests
test erc20::tests::new_works ... ok
test erc20::tests::balance_works ... ok

test result: ok. 2 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out; finished in 0.00s
```
通过运行以下命令，验证您是否可以为合约构建 WebAssembly：
```shell
cargo +nightly contract build
```
如果程序编译成功，您就可以以当前状态上传它或开始向合约添加功能。

上传并实例化合约
如果您想测试您目前拥有的内容，您可以使用Contracts UI上传合同。

在添加新功能之前测试 ERC-20 合约：

启动本地合约节点。
上传erc20.contract文件。
new为构造函数指定初始令牌供应。
在运行的本地节点上实例化合约。
选择totalSupply作为要发送的消息，然后单击读取以验证代币的总供应量与初始供应量相同。
选择balanceOf作为要发送的消息。
选择AccountId用于实例化合约的账户，然后点击Read。

如果您选择任何其他AccountId，然后单击读取，余额为零，因为所有代币都归合约所有者所有。

转移代币
此时，ERC-20 合约有一个用户账户，拥有该合约的total_supply 代币。为了使该合约有用，合约所有者必须能够将代币转移到其他账户。

对于这个简单的 ERC-20 合约，您将添加一个公共transfer函数，使您（作为合约调用者）能够将您拥有的代币转移给另一个用户。

公共transfer函数调用私有transfer_from_to()函数。因为这是一个内部函数，所以可以在没有任何授权检查的情况下调用它。但是，转移逻辑必须能够确定from帐户是否有可转移到接收to帐户的代币。该transfer_from_to()函数使用合约调用者 ( self.env().caller()) 作为from账户。在此上下文中，该transfer_from_to()函数然后执行以下操作：

from获取和to帐户的当前余额。
检查from余额是否小于value要发送的代币数量。
```rust
let from_balance = self.balance_of(from);
if from_balance < value {
   return Err(Error::InsufficientBalance)
}
```

value从转移帐户中减去并添加value到接收帐户。
```rust
self.balances.insert(from, &(from_balance - value));
let to_balance = self.balance_of(to);
self.balances.insert(to, &(to_balance + value));
```
将转账函数添加到智能合约：

如果您还没有打开终端外壳，请在本地计算机上打开终端外壳。
验证您是否在erc20项目目录中。
lib.rs在文本编辑器中打开。
如果账户中没有足够的代币来完成转账，请添加Error声明以返回错误。
```rust
/// Specify ERC-20 error type.
#[derive(Debug, PartialEq, Eq, scale::Encode, scale::Decode)]
#[cfg_attr(feature = "std", derive(scale_info::TypeInfo))]
pub enum Error {
/// Return if the balance cannot fulfill a request.
   InsufficientBalance,
}
```

添加Result返回类型以返回InsufficientBalance错误。
```rust
/// Specify the ERC-20 result type.
pub type Result<T> = core::result::Result<T, Error>;
```
添加transfer()公共功能以使合约调用者能够将代币转移到另一个帐户。
```rust
#[ink(message)]
pub fn transfer(&mut self, to: AccountId, value: Balance) -> Result<()> {
   let from = self.env().caller();
   self.transfer_from_to(&from, &to, value)
}
```
添加transfer_from_to()私有函数以将代币从与合约调用者关联的帐户转移到接收帐户。
```rust
fn transfer_from_to(
   &mut self,
   from: &AccountId,
   to: &AccountId,
   value: Balance,
) -> Result<()> {
    let from_balance = self.balance_of_impl(from);
    if from_balance < value {
        return Err(Error::InsufficientBalance)
    }

    self.balances.insert(from, &(from_balance - value));
    let to_balance = self.balance_of_impl(to);
    self.balances.insert(to, &(to_balance + value));
    Ok(())
}
```
此代码段使用该balance_of_impl()函数。该balance_of_impl()函数与该balance_of函数相同，只是它使用引用在 WebAssembly 中以更有效的方式查找帐户余额。将以下函数添加到智能合约中以使用此功能：
```rust
#[inline]
fn balance_of_impl(&self, owner: &AccountId) -> Balance {
   self.balances.get(owner).unwrap_or_default()
}
```
通过运行以下命令来验证程序是否编译并通过了测试用例：
```shell
cargo +nightly test
```
该命令应显示类似于以下内容的输出，以指示测试成功完成：
```shell
running 3 tests
test erc20::tests::new_works ... ok
test erc20::tests::balance_works ... ok
test erc20::tests::transfer_works ... ok

test result: ok. 3 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out; finished in 0.00s
```
创建事件
ERC-20 代币标准规定，合约调用在提交交易时不能直接返回值。但是，您可能希望您的智能合约以某种方式发出事件已发生的信号。例如，您可能希望您的智能合约指示何时进行交易或批准转移。您可以使用事件来发送这些类型的信号。

您可以使用事件来传达任何类型的数据。定义事件的数据类似于定义struct. 应使用#[ink(event)]属性声明事件。

添加转移事件
对于本教程，您将声明一个Transfer事件以提供有关已完成传输操作的信息。该Transfer事件包含以下信息：

类型的值Balance。
帐户的选项包装AccountId变量。from
帐户的选项包装AccountId变量。to
为了更快地访问事件数据，他们可以有索引字段。您可以通过使用该#[ink(topic)]字段上的属性标签来做到这一点。

添加Transfer事件：

lib.rs在文本编辑器中打开文件。
#[ink(event)]使用属性宏声明事件。
```rust
#[ink(event)]
pub struct Transfer {
   #[ink(topic)]
   from: Option<AccountId>,
   #[ink(topic)]
   to: Option<AccountId>,
   value: Balance,
}

```
您可以使用函数检索`Option<T>`变量的数据`.unwrap_or()`

发出事件
现在您已经声明了事件并定义了事件包含的信息，您需要添加发出事件的代码。为此，您可以self.env().emit_event()使用事件名称作为调用的唯一参数来调用函数。

在这个 ERC-20 合约中，您希望Transfer每次发生转账时都发出一个事件。代码中有两个地方出现这种情况：

在new调用期间初始化合约。
每次都这么transfer_from_to叫。
from和to字段的值是`Option<AccountId>`数据类型。但是，在代币的初始转移期间，为初始供应设置的价值 不来自任何其他账户。在这种情况下，Transfer 事件的from值为None。

要发出 Transfer 事件：

lib.rs在文本编辑器中打开文件。
将Transfer事件添加到构造new_init()函数中的new函数。
```rust
fn new_init(&mut self, initial_supply: Balance) {
   let caller = Self::env().caller();
   self.balances.insert(&caller, &initial_supply);
   self.total_supply = initial_supply;
   Self::env().emit_event(Transfer {
       from: None,
       to: Some(caller),
       value: initial_supply,
     });
}
```
将Transfer事件添加到transfer_from_to()函数中。
```rust
self.balances.insert(from, &(from_balance - value));
let to_balance = self.balance_of_impl(to);
self.balances.insert(to, &(to_balance + value));
self.env().emit_event(Transfer {
   from: Some(*from),
   to: Some(*to),
   value,
});
```
请注意，value不需要 aSome()因为该值未存储在 a 中Option。

添加一个将令牌从一个帐户转移到另一个帐户的测试。
```rust
#[ink::test]
fn transfer_works() {
   let mut erc20 = Erc20::new(100);
   assert_eq!(erc20.balance_of(AccountId::from([0x0; 32])), 0);
   assert_eq!(erc20.transfer((AccountId::from([0x0; 32])), 10), Ok(()));
   assert_eq!(erc20.balance_of(AccountId::from([0x0; 32])), 10);
}
```

通过运行以下命令来验证程序是否编译并通过了所有测试：
```shell
cargo +nightly test
```
该命令应显示类似于以下内容的输出，以指示测试成功完成：
```shell
running 3 tests
test erc20::tests::new_works ... ok
test erc20::tests::balance_works ... ok
test erc20::tests::transfer_works ... ok

test result: ok. 3 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out; finished in 0.00s
```
启用第三方传输
ERC-20 代币合约现在可以在账户之间转移代币并在发生这种情况时发出事件。作为最后一步，您可以添加approve和transfer_from函数以启用第三方传输。

允许一个账户代表另一个账户使用代币，可以让你的智能合约支持去中心化交易。您可以批准您拥有的一些代币代表您进行交易，而不是将您的代币直接转移给合同中的另一个用户。当您等待交易执行时，如果需要，您仍然可以控制和使用您的代币。您还可以批准多个合同或用户访问您的代币，因此如果一个合同提供最佳交易，您无需将代币从一个合同转移到另一个合同，这可能是一个成本高昂且耗时的过程。

为确保可以安全地完成批准和转移，ERC-20 代币合约使用两步流程，分别进行批准和转移操作。

添加审批逻辑
批准另一个账户来使用你的代币是第三方转账过程的第一步。作为代币所有者，您可以指定指定账户可以代表您转移的任意账户和任意数量的代币。您没有批准您帐户中的所有代币，您可以指定一个已批准帐户允许转移的最大数量。

当您approve多次调用时，您会用新值覆盖先前批准的值。默认情况下，任意两个账户之间的批准值为0。如果要撤销对帐户中令牌的访问权限，可以调用approve值为 的函数0。

要在 ERC-20 合约中存储批准，您需要使用稍微复杂的Mapping密钥。

由于每个帐户可以有不同的金额可供任何其他帐户使用，因此您需要使用元组作为映射到余额值的键。例如：
```rust
pub struct Erc20 {
 /// Balances that can be transferred by non-owners: (owner, spender) -> allowed
 allowances: ink_storage::Mapping<(AccountId, AccountId), Balance>,
}
```

元组用于(owner, spender)标识spender允许代表owner最多指定访问令牌的帐户allowance。

将审批逻辑添加到智能合约：

lib.rs在文本编辑器中打开文件。
使用属性宏声明Approval事件。#[ink(event)]
```rust
#[ink(event)]
pub struct Approval {
   #[ink(topic)]
   owner: AccountId,
   #[ink(topic)]
   spender: AccountId,
   value: Balance,
}
```
如果转账请求超出账户限额，请添加Error声明以返回错误。
```rust
#[derive(Debug, PartialEq, Eq, scale::Encode, scale::Decode)]
#[cfg_attr(feature = "std", derive(scale_info::TypeInfo))]
pub enum Error {
   InsufficientBalance,
   InsufficientAllowance,
}
```
将所有者和非所有者组合的存储映射添加到帐户余额。
```rust
allowances: Mapping<(AccountId, AccountId), Balance>,
```
增加approve()授权spender账户从调用方账户中提取token的功能，最高限额为value。
```rust
#[ink(message)]
pub fn approve(&mut self, spender: AccountId, value: Balance) -> Result<()> {
   let owner = self.env().caller();
   self.allowances.insert((&owner, &spender), &value);
   self.env().emit_event(Approval {
     owner,
     spender,
     value,
   });
   Ok(())
}
```
添加一个allowance()函数来返回一个spender允许从owner账户中提取的代币数量。
```rust
#[ink(message)]
pub fn allowance(&self, owner: AccountId, spender: AccountId) -> Balance {
   self.allowance_impl(&owner, &spender)
}
```

此代码段使用该allowance_impl()函数。该allowance_impl()函数与该allowance函数相同，只是它使用引用在 WebAssembly 中以更有效的方式查找令牌配额。将以下函数添加到智能合约中以使用此功能：
```rust
#[inline]
fn allowance_impl(&self, owner: &AccountId, spender: &AccountId) -> Balance {
   self.allowances.get((owner, spender)).unwrap_or_default()
}
```
添加从逻辑转移
现在您已经为一个帐户设置了代表另一个帐户转移代币的批准，您需要创建一个transfer_from函数以使批准的用户能够转移代币。该transfer_from函数调用私有transfer_from_to函数来完成大部分传输逻辑。授权非所有者转移代币有一些要求：

self.env().caller()必须为合约调用者分配from账户中可用的代币。
存储为 an 的分配allowance必须大于要传输的值。
如果满足这些要求，合约会将更新后的配额插入到allowance变量中，并使用指定的和帐户调用transfer_from_to()函数。fromto

记住调用transfer_from的时候self.env().caller()和from账户是用来查询当前额度的，但是transfer_from函数是在指定的from和to账户之间调用的。

每次调用时都会使用三个帐户变量transfer_from，您需要确保正确使用它们。

将transfer_from逻辑添加到智能合约：

lib.rs在文本编辑器中打开文件。
增加代币数量transfer_from()转入账户功能。valuefromto
```rust
/// Transfers tokens on the behalf of the `from` account to the `to account
#[ink(message)]
pub fn transfer_from(
   &mut self,
   from: AccountId,
   to: AccountId,
   value: Balance,
) -> Result<()> {
   let caller = self.env().caller();
   let allowance = self.allowance_impl(&from, &caller);
   if allowance < value {
       return Err(Error::InsufficientAllowance)
   }
   self.transfer_from_to(&from, &to, value)?;
   self.allowances
       .insert((&from, &caller), &(allowance - value));
   Ok(())
}
```
transfer_from()为函数添加测试。
```rust
#[ink::test]
fn transfer_from_works() {
   let mut contract = Erc20::new(100);
   assert_eq!(contract.balance_of(AccountId::from([0x1; 32])), 100);
   contract.approve(AccountId::from([0x1; 32]), 20);
   contract.transfer_from(AccountId::from([0x1; 32]), AccountId::from([0x0; 32]), 10);
   assert_eq!(contract.balance_of(AccountId::from([0x0; 32])), 10);
}
```
allowance()为函数添加测试。
```rust
#[ink::test]
fn allowances_works() {
   let mut contract = Erc20::new(100);
   assert_eq!(contract.balance_of(AccountId::from([0x1; 32])), 100);
   contract.approve(AccountId::from([0x1; 32]), 200);
   assert_eq!(contract.allowance(AccountId::from([0x1; 32]), AccountId::from([0x1; 32])), 200);

   contract.transfer_from(AccountId::from([0x1; 32]), AccountId::from([0x0; 32]), 50);
   assert_eq!(contract.balance_of(AccountId::from([0x0; 32])), 50);
   assert_eq!(contract.allowance(AccountId::from([0x1; 32]), AccountId::from([0x1; 32])), 150);

   contract.transfer_from(AccountId::from([0x1; 32]), AccountId::from([0x0; 32]), 100);
   assert_eq!(contract.balance_of(AccountId::from([0x0; 32])), 50);
   assert_eq!(contract.allowance(AccountId::from([0x1; 32]), AccountId::from([0x1; 32])), 150);
}
```
通过运行以下命令来验证程序是否编译并通过了所有测试：
```shell
cargo +nightly test
```
该命令应显示类似于以下内容的输出，以指示测试成功完成：
```shell
running 5 tests
test erc20::tests::new_works ... ok
test erc20::tests::balance_works ... ok
test erc20::tests::transfer_works ... ok
test erc20::tests::transfer_from_works ... ok
test erc20::tests::allowances_works ... ok

test result: ok. 5 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out; finished in 0.00s
```
通过运行以下命令，验证您是否可以为合约构建 WebAssembly
```shell
cargo +nightly contract build
```
在为合同构建 WebAssembly 后，您可以使用合同 UI 上传并实例化它，如上传和实例化合同中所述。

编写测试用例
在本教程中，您向lib.rs文件中添加了简单的单元测试。基本测试用例通过给出指定输入值并验证返回的结果来说明函数按预期工作。您可以通过编写额外的测试用例来提高代码的质量。例如，您可以添加对无效输入、空值或超出预期范围的值进行错误处理的测试。

下一步
在本教程中，您学习了如何使用 ink 编写简单的 ERC-20 代币智能合约！在 Substrate 区块链上运行。例如，本教程说明了：

如何创建具有固定数量代币的合约。
如何将代币从合约所有者转移到其他账户。
如何向智能合约添加测试。
如何启用第三方传输。
您可以在智能合约的资产中找到本教程的代码示例。 您可以在以下主题中了解有关智能合约开发的更多信息：

使用地图存储值
智能合约故障排除
墨水！文件