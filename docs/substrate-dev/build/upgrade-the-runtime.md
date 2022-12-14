# 升级运行时

无分叉运行时升级是用于区块链开发的 Substrate 框架的定义特性。在不分叉代码库的情况下更新运行时逻辑的能力使您的区块链能够随着时间的推移而发展和改进。通过将运行时执行环境的定义（运行时 WebAssembly blob）作为区块链运行时状态中的元素包含在内，此功能成为可能。

由于运行时是区块链状态的一部分，网络维护者可以利用区块链的功能实现去信任、去中心化的共识，从而安全地增强运行时。

在运行时开发的FRAME系统中，系统库定义了用于更新运行时定义的set_code调用。教程升级正在运行的网络演示了两种无需关闭节点或中断操作即可升级运行时的方法。但是，本教程中的两个升级都说明了向运行时添加功能，而不是更新现有的运行时状态。如果运行时升级需要更改现有状态，则可能需要存储迁移。

运行时版本控制
在Build process 中，您了解到编译节点会生成平台原生二进制文件和 WebAssembly 二进制文件，并且可以通过执行策略命令行选项控制在块生产过程的不同点选择使用哪个二进制文件。选择要与之通信的运行时执行环境的组件称为executor。尽管您可以覆盖自定义场景的默认执行策略，但在大多数情况下，或执行程序通过评估本机和 WebAssembly 运行时二进制文件的以下信息来选择要使用的适当二进制文件：

spec_name
spec_version
authoring_version
为了向执行程序进程提供此信息，运行时包含一个类似于以下内容的运行时版本结构：

pub const VERSION: RuntimeVersion = RuntimeVersion {
  spec_name: create_runtime_str!("node-template"),
  impl_name: create_runtime_str!("node-template"),
  authoring_version: 1,
  spec_version: 1,
  impl_version: 1,
  apis: RUNTIME_API_VERSIONS,
  transaction_version: 1,
};

结构中的参数提供以下信息：

这个参数	提供这个
spec_name	不同 Substrate 运行时的标识符。
impl_name	规范的实现名称。这对节点影响不大，仅用于区分不同实施团队的代码。
authoring_version	作者界面的版本。创作节点不会尝试创作区块，除非这等于其原生运行时。
spec_version	运行时规范的版本。除非Wasm 和本机二进制文件之间的所有spec_name、spec_version、 和都相同，否则完整节点不会尝试使用其本机运行时代替链上 Wasm 运行时。对Polkadot 网络authoring_version的更新spec_version可以作为 CI 过程自动化。当.transaction_version
impl_version	规范的实现版本。节点可以忽略这一点。它仅用于表示代码不同。只要 theauthoring_version和spec_versionthe 相同，代码本身可能已经改变，但本机和 Wasm 二进制文件做同样的事情。通常，只有非逻辑中断的优化会导致impl_version.
transaction_version	处理事务的接口版本。此参数可用于同步硬件钱包或其他签名设备的固件更新，以验证运行时事务是否有效。该参数允许硬件钱包知道他们可以安全地签署哪些交易。如果宏中的托盘索引发生变化，construct_runtime!或者可调度函数（例如参数数量或参数类型）发生任何变化，则必须增加此数字。如果此编号已更新，则spec_version也必须更新。
apis	支持的运行时 API列表及其版本。
编排引擎（有时称为执行器）在选择执行之前验证本机运行时是否具有与 WebAssembly 相同的共识驱动逻辑。但是，由于运行时版本控制是手动设置的，因此如果运行时版本被歪曲，编排引擎仍然会做出不适当的决定。

访问运行时版本
state.getRuntimeVersionFRAME 系统通过RPC 端点公开运行时版本信息。端点接受一个可选的块标识符。但是，在大多数情况下，您使用运行时元数据来了解运行时公开的 API 以及如何与这些 API 交互。运行时元数据应仅在链的运行时spec_version更改时更改。

无分叉运行时升级
传统区块链在升级其链的状态转换功能时需要硬分叉。硬分叉要求所有节点运营商停止其节点并手动升级到最新的可执行文件。对于分布式生产网络，硬分叉升级的协调可能是一个复杂的过程。

运行时版本控制属性使基于 Substrate 的区块链能够实时升级运行时逻辑，而不会导致网络分叉。

为了执行无分叉运行时升级，Substrate 使用现有运行时逻辑将存储在区块链上的 Wasm 运行时更新为具有新逻辑的新共识破坏版本。作为共识过程的一部分，此升级将推送到网络上的所有完整节点。升级 Wasm 运行时后，编排引擎会看到本机运行时spec_name、spec_version或authoring_version不再匹配新的 Wasm 运行时。因此，编排引擎执行规范的 Wasm 运行时，而不是在任何执行过程中使用本机运行时。

存储迁移
存储迁移是自定义的一次性功能，允许您更新存储以适应运行时的变化。例如，如果运行时升级将用于表示用户余额的数据类型从无符号整数更改为有符号整数，则存储迁移会将现有值读取为无符号整数并写回已转换为有符号整数的更新值整数。如果您在需要时不对数据的存储方式进行此类更改，则运行时将无法正确解释要包含在运行时状态中的存储值，并且可能会导致未定义的行为。

使用 FRAME 进行存储迁移
FRAME 存储迁移是使用OnRuntimeUpgradetrait 实现的。该OnRuntimeUpgrade特征指定了一个函数——它允许您指定在运行时升级之后但在执行任何函数或事务之前on_runtime_upgrade立即运行的逻辑。on_initialize

准备存储迁移
准备存储迁移意味着了解运行时升级定义的更改。Substrate 存储库使用E1-runtimemigration标签来指定此类更改。

编写迁移
每个存储迁移都是不同的，具有不同的要求和不同级别的复杂性。但是，您可以在需要执行存储迁移时使用以下推荐做法来指导您：

将迁移提取到可重用的函数中并为它们编写测试。
包括登录迁移以帮助调试。
请记住，迁移是在升级运行时的上下文中执行的。迁移代码可能需要包含不推荐使用的类型，如本例所示。
使用存储版本通过使迁移更具声明性来使迁移更安全，如本例所示。
订购迁移
默认情况下，FRAMEon_runtime_upgrade根据托盘在construct_runtime!宏中出现的顺序对函数的执行进行排序。对于升级，功能以相反的顺序运行，从最先执行的最后一个托盘开始。如果需要，您可以强加自定义订单（请参阅此处的示例）。

FRAME 存储迁移按以下顺序运行：

on_runtime_upgrade如果使用自定义订单，则自定义函数。
系统frame_system::on_runtime_upgrade功能。
on_runtime_upgrade运行时中定义的所有函数都从construct_runtime!宏中的最后一个托盘开始。
测试迁移
测试存储迁移很重要。一些可供您测试存储迁移的工具包括：

Substrate 调试套件包括一个远程外部性工具，允许对实时链数据安全地执行存储迁移单元测试。
fork-off-substrate脚本可以轻松创建链规范来引导本地测试链，以测试运行时升级和存储迁移。
下一步去哪里
升级运行时
基板迁移