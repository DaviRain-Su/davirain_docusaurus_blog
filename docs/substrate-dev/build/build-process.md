# 构建过程

在Architecture中，您了解了 Substrate 节点由外部节点主机和运行时执行环境组成。这些节点组件通过运行时 API 调用和宿主函数调用相互通信。在本节中，您将了解有关如何将 Substrate 运行时编译为平台原生可执行文件和存储在区块链上的 WebAssembly (Wasm) 二进制文件的更多信息。在了解如何编译二进制文件的内部工作之后，您将了解更多关于为什么有两个二进制文件、何时使用它们以及如何更改执行策略（如果需要）的更多信息。

编译优化的工件
您可能已经知道可以通过cargo build --release在 Substrate 节点项目的根目录中运行命令来编译 Substrate 节点。此命令为项目构建特定于平台的可执行文件和 WebAssembly 二进制文件，并生成优化的可执行工件。生成优化的可执行工件包括一些编译后处理。

作为优化过程的一部分，WebAssembly 运行时二进制文件通过一系列内部步骤进行编译和压缩，然后才包含在链的创世状态中。为了让您更好地理解该过程，下图总结了这些步骤。


WebAssembly 在包含在链上之前编译和压缩
以下部分更详细地描述了构建过程。

构建 WebAssembly 二进制文件
这wasm-builder是一个工具，它将为您的项目构建 WebAssembly 二进制文件的过程集成到主cargo构建过程中。此工具发布在substrate-wasm-buildercrate 中。

当您开始构建过程时，cargo会从项目中的所有内容构建依赖关系图Cargo.toml。然后运行时build.rs模块使用substrate-wasm-buildercrate 将运行时的 Rust 代码编译成 WebAssembly 二进制文件，创建初始二进制工件。

WebAssembly 中包含的功能
默认情况下，wasm-builder启用 WebAssembly 二进制文件和平台原生可执行文件中为项目定义的所有功能，除了仅为原生构建启用的default和功能。std

用于自定义构建过程的环境变量
您可以使用以下环境变量来自定义 WebAssembly 二进制文件的构建方式：

使用这个变量	如果你想这样做
SKIP_WASM_BUILD	跳过构建 WebAssembly 二进制文件。当您只需要重新编译本机二进制文件时，这很有用。但是，如果 WebAssembly 二进制文件不存在，则不会编译任何二进制文件。您可以通过在环境变量中包含项目名称来跳过单个项目的 WebAssembly 构建。例如，要跳过为 cargo 项目节点运行时构建 WebAssembly 二进制文件，您可以使用环境变量 SKIP NODE RUNTIME WASM_BUILD。
WASM_BUILD_TYPE	指定 WebAssembly 二进制文件是release构建还是debug构建。默认情况下，使用您为cargo命令指定的构建类型。
FORCE_WASM_BUILD	强制构建 WebAssembly。wasm-builder由于指示cargo检查文件更改，因此很少需要此环境变量。
WASM_BUILD_RUSTFLAGS	在构建 WebAssembly 二进制文件时扩展RUSTFLAGS传递给命令。cargo build
WASM_BUILD_NO_COLOR	禁用 WebAssembly 构建的颜色输出。
WASM_TARGET_DIRECTORY	将 WebAssembly 二进制文件复制到指定目录。路径必须是绝对的。
WASM_BUILD_TOOLCHAIN	指定用于构建 WebAssembly 二进制文件的工具链。格式必须与 使用的格式相同cargo，例如nightly-2020-02-20。
CARGO_NET_OFFLINE	阻止为支持离线环境而启动的部分或所有进程的网络访问。
压缩和压缩 WebAssembly 二进制文件
crate 使用较低级别的substrate-wasm-builder进程来优化指令序列并删除任何不必要的代码（例如用于调试的代码）以创建紧凑的 WebAssembly 二进制文件。然后进一步压缩二进制文件以最小化最终 WebAssembly 二进制文件的大小。当编译器处理runtime/src/lib.rs节点的文件时，它会看到包含生成的 WebAssembly 二进制文件的要求：

include!(concat!(env!("OUT_DIR"), "/wasm_binary.rs"));

这段代码在其编译结果中包含了压缩的 WebAssembly 二进制文件 ( WASM_BINARY) 和编译器生成的未压缩的 WebAssembly 二进制文件 ( ) WASM_BINARY_BLOATY，并生成了项目的最终可执行二进制文件。

在构建过程的每个阶段，WebAssembly 二进制文件都被压缩到越来越小的大小。例如，您可以比较 Polkadot 的每个 WebAssembly 二进制工件的大小：

.rw-r--r-- 1.2M pep  1 Dec 16:13 │  ├── polkadot_runtime.compact.compressed.wasm
.rw-r--r-- 5.1M pep  1 Dec 16:13 │  ├── polkadot_runtime.compact.wasm
.rwxr-xr-x 5.5M pep  1 Dec 16:13 │  └── polkadot_runtime.wasm

您应该始终使用完全压缩的运行时 ( *_runtime.compact.compressed.wasm) WebAssembly 二进制文件进行链上升级和中继链验证。在大多数情况下，不需要使用初始 WebAssembly 二进制或临时紧凑工件。

执行策略
在使用本机和 WebAssembly 运行时编译节点后，您可以使用命令行选项来指定节点应如何操作。有关可用于启动节点的命令行选项的详细信息，请参阅node-template命令行参考。

当您启动节点时，节点可执行文件使用您指定的命令行选项来初始化链并生成创世块。作为此过程的一部分，节点将 WebAssembly 运行时添加为存储项值和相应的:code键。

启动节点后，正在运行的节点会选择要使用的运行时。默认情况下，节点始终使用 WebAssembly 运行时进行所有操作，包括：

同步
创作新区块
导入块
与链下工作人员互动
WebAssembly 运行时的选择
使用 WebAssembly 运行时很重要，因为 WebAssembly 和本机运行时可能存在差异。例如，如果您对运行时进行更改，则必须生成新的 WebAssembly blob 并更新链以使用新版本的 WebAssembly 运行时。更新后，WebAssembly 运行时与原生运行时不同。为了解决这种差异，所有执行策略都将运行时的 WebAssembly 表示视为规范运行时。如果本机运行时和 WebAssembly 运行时版本不同，则始终选择 WebAssembly 运行时。

由于 WebAssembly 运行时存储为区块链状态的一部分，因此网络必须就该二进制文件的表示达成共识。为了就二进制文件达成共识，代表 WebAssembly 运行时的 blob 在所有同步节点中必须完全相同。

WebAssembly 执行环境
WebAssembly 执行环境可能比 Rust 执行环境更具限制性。例如，WebAssembly 执行环境是 32 位架构，最大内存为 4GB。可以在 WebAssembly 运行时执行的逻辑总是可以在 Rust 执行环境中执行。然而，并不是所有可以在 Rust 运行时中执行的逻辑都可以在 WebAssembly 运行时中执行。块创作节点通常使用 WebAssembly 执行环境来帮助确保它们生成有效的块。

本机运行时
尽管默认选择 WebAssembly 运行时，但您可以通过将执行策略指定为命令行选项来覆盖为所有或特定操作选择的运行时。

如果原生运行时和 WebAssembly 运行时共享相同的版本，除了 WebAssembly 运行时之外，您可以选择性地使用原生运行时而不是 WebAssembly 运行时，或者在使用 WebAssembly 运行时失败时作为备用。通常，您只会出于性能原因或因为它是一个比 WebAssembly 运行时限制较少的环境而选择使用本机运行时。例如，您可能希望使用本机运行时进行初始同步。要为此同步块使用本机运行时，您可以使用--execution-syncing native或--execution-syncing native-else-wasm命令行选项启动节点。

有关使用命令行选项为所有或特定操作指定执行策略的信息，请参阅node-template。有关执行策略变体的信息，请参阅ExecutionStrategy

在没有本机运行时的情况下构建 WebAssembly
启动新链需要 WebAssembly 运行时。提供初始 WebAssembly 运行时后，代表 WebAssembly 运行时的 blob 可以作为链规范的一部分传递给其他节点。在极少数情况下，您可能希望在没有本机运行时的情况下编译 WebAssembly 目标。例如，如果您正在测试 WebAssembly 运行时以准备进行无分叉升级，您可能只想编译新的 WebAssembly 二进制文件。

尽管这是一个罕见的用例，但您可以使用build-only-wasm.sh脚本来构建no_stdWebAssembly 二进制文件，而无需编译本机运行时。

您还可以使用wasm-runtime-overrides命令行选项从文件系统加载 WebAssembly。

在没有 WebAssembly 的情况下编译 Rust
如果您想在不构建新的 WebAssembly 运行时的情况下为节点编译 Rust 代码，您可以使用SKIP_WASM_BUILD作为构建选项。当您不需要更新 WebAssembly 时，此选项主要用于加快编译时间。

下一步去哪里
Wasm 构建器自述文件
Rust 编译选项
讨论：删除本机运行时