# 单元测试

在为运行时构建逻辑时，您需要定期测试逻辑是否按预期工作。您可以使用Rust 提供的单元测试框架为运行时创建单元测试。创建一个或多个单元测试后，您可以使用cargo test命令执行测试。例如，您可以通过运行以下命令来运行您为运行时创建的所有测试：
```
cargo test
```
有关使用 Rust cargo test 命令和测试框架的更多信息，请运行以下命令：
```
cargo help test
```
在模拟运行时测试托盘日志
除了可以使用 Rust 测试框架进行的单元测试之外，您还可以通过构建模拟运行时环境来验证运行时中的逻辑。配置类型Test被定义为一个 Rust 枚举，其中包含在模拟运行时中使用的每个托盘配置特征的实现。
```
frame_support::construct_runtime!(
 pub enum Test where
  Block = Block,
  NodeBlock = Block,
  UncheckedExtrinsic = UncheckedExtrinsic,
 {
  System: frame_system::{Pallet, Call, Config, Storage, Event<T>},
  TemplateModule: pallet_template::{Pallet, Call, Storage, Event<T>},
 }
);

impl frame_system::Config for Test {
 // -- snip --
 type AccountId = u64;
}
```
如果Testimplements pallet_balances::Config，则分配可能u64用于Balance类型。例如：
```
impl pallet_balances::Config for Test {
 // -- snip --
 type Balance = u64;
}
```
通过分配pallet_balances::Balanceand frame_system::AccountIdto u64，测试账户和余额只需要(AccountId: u64, Balance: u64)在模拟运行时跟踪映射。

在模拟运行时测试存储
sp-iocrate 公开了一个TestExternalities实现，您可以使用它在模拟环境中测试存储。它是内存中基于哈希图的外部性实现的类型别名，substrate_state_machine称为TestExternalities.

下面的示例演示定义一个调用ExtBuilder来构建 的实例的结构TestExternalities，并将块编号设置为 1。
```
pub struct ExtBuilder;

impl ExtBuilder {
 pub fn build(self) -> sp_io::TestExternalities {
  let mut t = system::GenesisConfig::default().build_storage::<TestRuntime>().unwrap();
  let mut ext = sp_io::TestExternalities::new(t);
  ext.execute_with(|| System::set_block_number(1));
  ext
 }
}
```
为了在单元测试中创建测试环境，调用 build 方法以TestExternalities使用默认的 genesis 配置生成一个。
```
#[test]
fn fake_test_example() {
 ExtBuilder::default().build_and_execute(|| {
  // ...test logics...
 });
}
```
Externalities的自定义实现允许您构建提供对外部节点功能的访问的运行时环境。另一个例子可以在offchain模块中找到。该offchain模块维护自己的Externalities实现。

创世纪配置
在前面的示例中，该ExtBuilder::build()方法使用默认的 genesis 配置来构建模拟运行时环境。在很多情况下，在测试之前设置存储是很方便的。例如，您可能希望在测试之前预先设定帐户余额。

在执行中frame_system::Config，AccountId和Balance都设置为u64。您可以将货币(u64, u64)对放入balancesvec 以(AccountId, Balance)作为账户余额的种子对。例如：
```
impl ExtBuilder {
 pub fn build(self) -> sp_io::TestExternalities {
  let mut t = frame_system::GenesisConfig::default().build_storage::<Test>().unwrap();
  pallet_balances::GenesisConfig::<Test> {
   balances: vec![
    (1, 10),
    (2, 20),
    (3, 30),
    (4, 40),
    (5, 50),
    (6, 60)
   ],
  }
   .assimilate_storage(&mut t)
   .unwrap();

  let mut ext = sp_io::TestExternalities::new(t);
  ext.execute_with(|| System::set_block_number(1));
  ext
 }
}
```
A在本例中，账户 1 的余额为 10，账户 2 的余额为 20，以此类推。

用于定义托盘创世配置的确切结构取决于托盘GenesisConfig结构定义。例如，在 Balances 托盘中，它被定义为：
```
pub struct GenesisConfig<T: Config<I>, I: 'static = ()> {
 pub balances: Vec<(T::AccountId, T::Balance)>,
}
```
块生产
模拟块生产以验证预期行为在块生产中是否成立是有用的。

一个简单的方法是增加系统模块的块号，并在所有模块之间on_initialize调用on_finalize作为System::block_number()唯一输入。尽管运行时代码缓存对存储或系统模块的调用很重要，但测试环境脚手架应优先考虑可读性，以便于将来的维护。
```
fn run_to_block(n: u64) {
 while System::block_number() < n {
  if System::block_number() > 1 {
   ExamplePallet::on_finalize(System::block_number());
   System::on_finalize(System::block_number());
  }
  System::set_block_number(System::block_number() + 1);
  System::on_initialize(System::block_number());
  ExamplePallet::on_initialize(System::block_number());
 }
}
```
和方法仅在托盘特征实现特征以分别在每个块之前和之后执行在运行时方法中编码的逻辑on_finalize时才被调用。on_initializeExamplePalletframe_support::traits::{OnInitialize, OnFinalize}

然后按以下方式调用此函数。
```
#[test]
fn my_runtime_test() {
 with_externalities(&mut new_test_ext(), || {
  assert_ok!(ExamplePallet::start_auction());
  run_to_block(10);
  assert_ok!(ExamplePallet::end_auction());
 });
}
```
下一步去哪里