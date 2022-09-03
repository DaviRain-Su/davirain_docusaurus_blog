# 开发智能合约
在准备你的第一个合约中，你学习了使用默认的第一个项目在基于 Substrate 的区块链上构建和部署智能合约的基本步骤。在本教程中，您将开发一个新的智能合约，每次执行函数调用时都会增加一个计数器值。

在你开始之前
在开始之前，请验证以下内容：

您有良好的互联网连接并可以访问本地计算机上的 shell 终端。
您通常熟悉软件开发和使用命令行界面。
您通常熟悉区块链和智能合约平台。
您已经按照安装中的描述安装了 Rust 并设置了您的开发环境。
您已完成准备您的第一个合约并在本地安装了 Substrate 合约节点。
教程目标
通过完成本教程，您将实现以下目标：

了解如何使用智能合约模板。
使用智能合约存储简单值。
使用智能合约增加和检索存储的值。
向智能合约添加公共和私有功能。
智能合约和墨水！
在准备您的第一份合同中，您安装了cargo-contract用于命令行访问 ink 的软件包！编程语言。墨水！语言是一种嵌入式领域特定语言。该语言使您能够使用 Rust 编程语言编写基于 WebAssembly 的智能合约。

该语言使用带有专用#[ink(...)]属性宏的标准 Rust 模式。这些属性宏描述了智能合约的不同部分所代表的内容，以便可以将它们转换为与 Substrate 兼容的 WebAssembly 字节码。

创建一个新的智能合约项目
在 Substrate 上运行的智能合约从项目开始。cargo contract您使用命令创建项目。

在本教程中，您将为incrementer智能合约创建一个新项目。创建新项目会将新项目目录和默认启动文件（也称为模板文件）添加到项目目录中。您将修改这些起始模板文件以构建incrementer项目的智能合约逻辑。

为您的智能合约创建一个新项目：

如果您还没有打开终端外壳，请在本地计算机上打开终端外壳。
incrementer通过运行以下命令创建一个名为的新项目：

cargo contract new incrementer

通过运行以下命令切换到新项目目录：

cd incrementer/

lib.rs在文本编辑器中打开文件。

默认情况下，模板lib.rs文件包含flipper智能合约的源代码，flipper合约名称的实例重命名为incrementer.

用新的增量器源代码替换默认模板源代码。
保存对lib.rs文件的更改，然后关闭文件。
在文本编辑器中打开Cargo.toml文件并查看合同的依赖关系。
如有必要，在该[dependencies]部分中修改scale和设置。scale-info

scale = { package = "parity-scale-codec", version = "3", default-features = false, features = ["derive"] }
scale-info = { version = "2", default-features = false, features = ["derive"], optional = true }

保存对Cargo.toml文件的更改，然后关闭文件。
通过运行以下命令来验证程序是否编译并通过了简单的测试：

cargo +nightly test

您可以忽略任何警告，因为此模板代码只是一个骨架。该命令应显示类似于以下内容的输出，以指示测试成功完成：

running 1 test
test incrementer::tests::default_works ... ok

test result: ok. 1 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out; finished in 0.00s
通过运行以下命令，验证您是否可以为合约构建 WebAssembly：

cargo +nightly contract build

如果程序编译成功，您就可以开始编程了。

存储简单值
现在您已经有了一些incrementer智能合约的入门源代码，您可以引入一些新功能。例如，这个智能合约需要存储简单的值。以下代码说明了如何使用#[ink(storage)]属性宏存储此合约的简单值：

#[ink(storage)]
pub struct MyContract {
	// Store a bool
	my_bool: bool,
	// Store a number
	my_number: u32,
}

支持的类型
Substrate 智能合约支持大多数 Rust 常用数据类型，包括布尔值、无符号和有符号整数、字符串、元组和数组。这些数据类型使用Parity 规模编解码器进行编码和解码，以便通过网络进行有效传输。

除了可以使用 scale 编解码器进行编码和解码的常见 Rust 类型之外，还有 ink! 语言支持特定于 Substrate 的类型——比如AccountId、Balance和Hash——就好像它们是原始类型一样。下面的代码说明了如何为这个合约存储一个AccountId和：Balance

// We are importing the default ink! types
use ink_lang as ink;

#[ink::contract]
mod MyContract {

	// Our struct will use those default ink! types
	#[ink(storage)]
	pub struct MyContract {
		// Store some AccountId
		my_account: AccountId,
		// Store some Balance
		my_balance: Balance,
	}
	/* --snip-- */
}

构造函数
每一种墨水！智能合约必须至少有一个在创建合约时运行的构造函数。但是，如果需要，智能合约可以有多个构造函数。以下代码说明了使用多个构造函数：

use ink_lang as ink;

#[ink::contract]
mod mycontract {

	#[ink(storage)]
	pub struct MyContract {
		number: u32,
	}

	impl MyContract {
		/// Constructor that initializes the `u32` value to the given `init_value`.
		#[ink(constructor)]
		pub fn new(init_value: u32) -> Self {
			Self {
				number: init_value,
			}
		}

		/// Constructor that initializes the `u32` value to the `u32` default.
		///
		/// Constructors can delegate to other constructors.
		#[ink(constructor)]
		pub fn default() -> Self {
			Self {
				number: Default::default(),
			}
		}
	/* --snip-- */
	}
}

更新你的智能合约
现在您已经了解了存储简单值、声明数据类型和使用构造函数，您可以更新智能合约源代码以实现以下内容：

创建一个value数据类型为的存储值i32。
创建一个新的Incrementer构造函数并将其设置value为init_value.
创建第二个名为default没有输入的构造函数，并创建一个新Incrementer的并将其value设置为0.
更新智能合约：

lib.rs在文本编辑器中打开文件。
Storage Declaration通过声明以value数据类型命名的存储项来替换注释i32。

#[ink(storage)]
pub struct Incrementer {
   value: i32,
}

修改Incrementer构造函数以将其设置value为init_value.

impl Incrementer {
 #[ink(constructor)]
 pub fn new(init_value: i32) -> Self {
       Self {
         value: init_value,
       }
 }
}

添加第二个名为的构造函数default，该函数创建一个新Incrementer的并将其value设置为0.

#[ink(constructor)]
pub fn default() -> Self {
   Self {
       value: 0,
   }
}

保存更改并关闭文件。
通过运行以下命令，使用test子命令和工具链来测试您的工作：nightly

cargo +nightly test

该命令应显示类似于以下内容的输出，以指示测试成功完成：

running 1 test
test incrementer::tests::default_works ... ok

test result: ok. 1 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out; finished in 0.00s
添加一个函数来获取一个存储值
现在您已经创建并初始化了一个存储值，您可以使用公共和私有函数与它进行交互。在本教程中，您将添加一个公共函数来获取存储值。请注意，所有公共函数都必须使用#[ink(message)]属性宏。

将公共功能添加到智能合约：

lib.rs在文本编辑器中打开文件。
更新get公共函数以返回value具有该i32数据类型的存储项的数据。

#[ink(message)]
pub fn get(&self) -> i32 {
   self.value
}

因为这个函数只从合约存储中读取&self，所以它使用参数来访问合约函数和存储项。此功能不允许更改value存储项目的状态。

如果函数中的最后一个表达式没有分号 (;)，Rust 会将其视为返回值。

用代码替换Test Your Contract私有default_works函数中的注释来测试get函数。

fn default_works() {
   let contract = Incrementer::default();
   assert_eq!(contract.get(), 0);
}

保存更改并关闭文件。
通过运行以下命令，使用test子命令和工具链来测试您的工作：nightly

cargo +nightly test

添加修改存储值的功能
此时，智能合约不允许用户修改存储。要使用户能够修改存储项，您必须显式标记value为可变变量。

要添加用于递增存储值的函数：

lib.rs在文本编辑器中打开文件。
添加一个新的inc公共函数以value使用by数据类型为i32.

#[ink(message)]
pub fn inc(&mut self, by: i32) {
   self.value += by;
}

在源代码中添加一个新的测试来验证这个功能。

#[ink::test]
   fn it_works() {
       let mut contract = Incrementer::new(42);
       assert_eq!(contract.get(), 42);
       contract.inc(5);
       assert_eq!(contract.get(), 47);
       contract.inc(-50);
       assert_eq!(contract.get(), -3);
}

保存更改并关闭文件。
通过运行以下命令，使用test子命令和工具链来测试您的工作：nightly

cargo +nightly test

该命令应显示类似于以下内容的输出，以指示测试成功完成：

running 2 tests
test incrementer::tests::it_works ... ok
test incrementer::tests::default_works ... ok

test result: ok. 2 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out; finished in 0.00s

为合约构建 WebAssembly
测试incrementer合约后，您就可以将此项目编译为 WebAssembly。将智能合约编译为 WebAssembly 后，您可以使用Contracts UI在本地合约节点上部署和测试智能合约。

为这个智能合约构建 WebAssembly：

如果需要，在您的计算机上打开终端外壳。
验证您是否位于incrementer项目文件夹中。
incrementer通过运行以下命令编译智能合约：

cargo +nightly contract build

该命令显示类似于以下内容的输出：

Your contract artifacts are ready. You can find them in:
/Users/dev-docs/incrementer/target/ink

- incrementer.contract (code + metadata)
- incrementer.wasm (the contract's code)
- metadata.json (the contract's metadata)
部署和测试智能合约
如果您在substrate-contracts-node本地安装了节点，您可以为您的智能合约启动一个本地区块链节点，然后使用Contracts UI来部署和测试智能合约。

在本地节点上部署：

如果需要，在您的计算机上打开终端外壳。
通过运行以下命令以本地开发模式启动contracts节点：

substrate-contracts-node --dev

打开Contracts UI并验证它是否已连接到本地节点。
单击添加新合同。
点击上传新合同代码。
选择incrementer.contract文件，然后单击Next。
单击上传并实例化。
使用合约 UI 探索智能合约并与之交互。
下一步
在本教程中，您学习了一些使用墨水创建智能合约的基本技术！编程语言和属性宏。例如，本教程说明了：

如何在新的智能合约项目中添加存储项、指定数据类型和实现构造函数。
如何向智能合约添加功能。
如何向智能合约添加测试。
如何使用合同 UI 上传和实例化合同。
您可以在智能合约的资产中找到本教程的最终代码示例。 您可以在以下主题中了解有关智能合约开发的更多信息：

使用地图存储值
构建 ERC20 代币合约
智能合约故障排除