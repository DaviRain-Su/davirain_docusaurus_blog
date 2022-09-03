# 节点模板

该node-template程序提供了一个带有 FRAME 系统托盘的工作 Substrate 节点和一个用于处理常见区块链功能操作的附加托盘子集。凭借其功能托盘的基线，它node-template可用作构建您自己的区块链和开发自定义运行时的入门工具包。您可以使用该程序来启动 Substrate 节点并执行Subcommandsnode-template中列出的任务。

基本命令用法
运行node-template命令的基本语法是：

`node-template [subcommand] [flags] [options]`

根据您指定的子命令，可能会应用或需要其他参数、选项和标志。要查看特定node-template子命令的使用信息，请指定子命令和--help标志。例如，要查看 的使用信息node-template key，您可以运行以下命令：

`node-template key --help`

标志
node-template您可以在命令中使用以下可选标志。

旗帜	描述
--alice	将预定义Alice帐户的会话密钥添加到本地密钥库。此标志等效于使用--name alice --validator命令行选项运行节点。
--allow-private-ipv4	允许节点连接到私有 IPv4 地址。如果节点的链规范被标识为，local或者您使用该标志以开发模式启动节点，则默认启用此--dev标志。
--bob	将预定义Bob帐户的会话密钥添加到本地密钥库。此标志等效于使用--name bob --validator命令行选项运行节点。
--charlie	将预定义Charlie帐户的会话密钥添加到本地密钥库。此标志等效于使用--name charlie --validator命令行选项运行节点。
--dave	将预定义Dave帐户的会话密钥添加到本地密钥库。此标志等效于使用--name dave --validator命令行选项运行节点。
--dev	在新状态下以开发模式启动节点。如果您使用此标志运行节点，则不会保留任何状态。
--disable-log-color	禁止在日志消息中使用颜色。
--disable-log-reloading	禁用日志过滤器更新和重新加载。默认情况下，启用动态日志过滤。但是，该功能可能会影响性能。如果使用此标志启动节点，则system_addLogFilter和system_resetLogFilter远程过程调用无效。
--discover-local	在本地网络上启用对等发现。默认情况下，true如果您使用该标志启动节点，--dev或者链规范是Localor Development，则此标志是或false否则。
--eve	将预定义Eve帐户的会话密钥添加到本地密钥库。此标志等效于使用--name eve --validator命令行选项运行节点。
--ferdie	将预定义Ferdie帐户的会话密钥添加到本地密钥库。此标志等效于使用--name ferdie --validator命令行选项运行节点。
--force-authoring	即使节点处于脱机状态，也启用块创作。
-h,--help	显示使用信息。
--ipfs-server	加入 IPFS 网络并通过 bitswap 协议服务事务
--kademlia-disjoint-query-paths	需要迭代 Kademlia 分布式哈希表 (DHT) 查询才能使用脱节路径。在存在潜在对抗节点的情况下，此选项可提高弹性。有关高级设计及其安全改进的更多信息，请参阅 S/Kademlia 论文。
--light	以轻客户端模式运行节点（实验性）。
--no-grandpa	如果节点作为验证者模式运行，则禁用 GRANDPA 投票者。如果节点未作为验证者运行，该选项将禁用 GRANDPA 观察者。
--no-mdns	禁用 mDNS 发现。默认情况下，网络使用 mDNS 来发现本地网络上的其他节点。如果您使用该选项启动节点，此选项将禁用发现并自动应用--dev。
--no-private-ipv4	阻止连接到私有 IPv4 地址，除非该地址是使用--reserved-nodesor--bootnodes选项传递的。默认情况下，对于在其链规范中标记为“活动”的链启用此设置。
--no-prometheus	禁用 Prometheus 端点的暴露以接收指标。默认情况下，指标会导出到 Prometheus 端点。
--no-telemetry	禁用连接到 Substrate 遥测服务器。默认情况下，为全局链启用遥测。
--one	提供指定--name One --validator将会话密钥添加One到密钥库的快捷方式。
--password-interactive	使您能够在终端 shell 中以交互方式指定用于连接到密钥库的密码。
--prometheus-external	在所有接口上公开 Prometheus 导出器。默认为本地。
--reserved-only	指定是否仅将链与保留节点同步。此选项还禁用自动对等发现。TCP 连接可能仍与非保留节点建立。特别是，如果您是验证者，您的节点可能仍会连接到其他验证者节点和收集者节点，无论它们是否被定义为保留节点。
--rpc-external	侦听所有 RPC 接口。默认为本地。注意：并非所有 RPC 方法都可以安全地公开。使用 RPC 代理服务器过滤掉危险的方法。更多细节：https ://github.com/paritytech/substrate/wiki/Public-RPC 。--unsafe-rpc-external如果您了解风险，则用于抑制警告。
--storage-chain	启用存储链模式。如果设置此选项，则每个事务单独存储在事务数据库列中，并且仅由块体列中的哈希引用。
--tmp	运行一个临时节点。此选项创建一个临时目录来存储区块链配置，包括节点数据库、节点密钥和密钥库。
--two	提供指定--name Two --validator将会话密钥添加Two到密钥库的快捷方式。
--unsafe-pruning	强制节点以不安全的修剪设置开始。作为验证器运行时，强烈建议禁用默认状态修剪（即存档）。如果启用修剪，则节点将拒绝作为验证器启动，除非设置了此选项。
--unsafe-rpc-external	侦听所有 RPC 接口。此选项与 相同--rpc-external。
--unsafe-ws-external	监听所有 Websocket 接口。此选项与此选项相同，--ws-external但不会向您发出警告。
--validator	启动具有权限角色的节点，并使其能够积极参与它可以参与的任何共识任务（例如，取决于本地密钥的可用性）。
-V,--version	显示版本信息。
--ws-external	监听所有 Websocket 接口。默认情况下，节点仅在本地侦听。请记住，并非所有 RPC 方法都可以安全地公开。您可以使用 RPC 代理服务器来过滤掉危险的方法。如果您了解风险，您可以使用--unsafe-ws-external来抑制警告。
选项
node-template您可以在命令中使用以下选项。

选项	描述
`-d,--base-path <path>`	指定自定义基本路径。
`--bootnodes <node-identifier>`...	指定对等通信的引导节点标识符列表。
`--chain <chain-specification>`	指定要使用的链规范。您可以使用预定义的链规范名称设置此选项，例如dev, local，staging或者您可以指定包含链规范的文件的路径，例如使用build-spec子命令生成的链规范。
`--database <database>`	选择要使用的数据库后端。有效值为rocksdb、paritydb-experimental或auto。
`--db-cache <MiB>`	限制数据库缓存可以使用的内存量。
`--offchain-worker <execution>`	确定何时执行脱链工作进程。默认情况下，仅对正在创作新块的节点启用脱链工作者，并且在块验证期间执行脱链工作者。有效值为Always、Never或WhenValidating。
`--execution <strategy>`	确定所有执行上下文使用的执行策略。有效值为Native、Wasm或。BothNativeElseWasm
`--execution-block-construction <strategy>`	指定调用运行时构造块时使用的执行类型。有效值为Native、Wasm、Both或NativeElseWasm。
`--execution-import-block <strategy>`	指定调用运行时以导入块（包括本地创作的块）时使用的执行类型。有效值为Native、Wasm、Both或NativeElseWasm。
`--execution-offchain-worker <strategy>`	指定调用运行时以使用脱链工作者时使用的执行类型。有效值为Native、Wasm、Both或NativeElseWasm。
`--execution-other <strategy>`	指定调用运行时而不同步、导入或构造块时使用的执行类型。有效值为Native、Wasm、Both或NativeElseWasm。
`--execution-syncing <strategy>`	指定调用运行时以导入块作为初始同步的一部分时使用的执行类型。有效值为Native、Wasm、Both或NativeElseWasm。
`--in-peers <count>`	指定要接受的最大传入连接数。默认值为 25 个对等点。
`--enable-offchain-indexing <database>`	启用链下索引 API。链下索引 API 使运行时能够在块导入期间直接写入链下工作数据库。
`--ipc-path <path>`	指定将进程间通信 (IPC) 发送到远程过程调用 (RPC) 服务器的路径。
`--keep-blocks <count>`	指定要保存在数据库中的最终块的数量。默认是保留所有块。
`--keystore-path <path>`	指定自定义密钥库的路径。
`--keystore-uri <keystore-uri>`	为密钥库服务指定要连接的自定义 URI。
`--listen-addr <listen-address>`...	指定要侦听的节点的地址。默认情况下，如果您使用该--validator选项启动节点，则使用地址`/ip4/0.0.0.0/tcp/<port>`和`/ip6/[::]/tcp/<port>`。否则，使用`/ip4/0.0.0.0/tcp/<port>/wsand/ip6/[::]/tcp/<port>/ws`地址。
`-l,--log <log-pattern>`...	设置自定义日志过滤器。`<log-target>=<level>`例如，要使用的语法是-lsync=debug。从最详细到最详细的有效日志级别是error、warn、info、debug和trace。默认情况下，所有目标都记录info级别消息。您可以使用 设置全局日志级别`-l<level>`。
`--max-parallel-downloads <count>`	指定从其并行请求相同块的最大对等点数。此选项允许节点从多个对等点下载已宣布的块。您可以减少计数以减少流量，但可能会增加延迟。默认为 5 次并行下载。
`--max-runtime-instances <max-runtime-instances>`	指定每个运行时实例缓存的最大大小。默认值为 8，高于 256 的值将被忽略。
`--name <name>`	指定此节点的人类可读名称。如果启用，则将节点名称报告给遥测服务器。
`--node-key <key>`	指定用于libp2p网络的密钥。该值是一个基于--node-key-type. 例如，如果节点密钥类型为ed25519，则节点密钥被解析为十六进制编码的 Ed25519 32 字节密钥（64 个十六进制字符）。此选项的值优先于--node-key-file。请注意，作为命令行参数提供的秘密很容易暴露。您应该只将此选项用于开发和测试。要使用外部管理的密钥，请使用该--node-key-file选项。
`--node-key-file <file>`	指定包含用于libp2p网络的节点的密钥的文件。文件的内容是根据--node-key-type. 例如，如果节点密钥类型为ed25519，则文件必须包含未编码的 32 字节或十六进制编码的 Ed25519 密钥。--node-key-type如果该文件不存在，则使用您使用该选项指定的类型的新生成的密钥创建它。
`--node-key-type <type>`	指定用于点对点 ( libp2p) 网络的密钥类型。--node-key您可以使用该选项在命令行上指定密钥，使用该选项从文件中读取密钥，或者从该选项指定的基本目录内--node-key-file的链特定目录中指定的文件中读取密钥。如果此文件不存在，则使用新生成的所选类型的密钥创建它。节点的密钥决定了公钥——对等标识符——用于使用库与节点通信。默认类型为 Ed25519。config--base-dirlibp2p
`--out-peers <count>`	指定要维护的最大传出连接数。默认值为 25。
`--password <password>`	指定要用于密钥库的密码。
`--password-filename <path>`	指定包含用于密钥库的密码的文件的路径。
`--pool-kbytes <count>`	指定存储在事务池中的所有事务的最大千字节数。默认值为 20480 KB。
`--pool-limit <count>`	指定可以在事务池中的最大事务数。默认值为 8192 个事务。
`--port <port>`	指定用于对等通信的 TCP 端口。
`--prometheus-port <port>`	指定用于 Prometheus 导出器服务的 TCP 端口。
`--pruning <pruning-mode>`	指定要保留或archive保留所有方块状态的最大方块状态数。如果节点作为验证者运行，则默认保留所有块状态。如果节点不作为验证者运行，则仅保留最后 256 个块的状态。
`--public-addr <public-address>`...	指定其他节点可以用来连接到该节点的公共地址。您可以使用此选项通过代理连接到节点。
`--reserved-nodes <address>`...	指定保留节点地址的列表。
`--rpc-cors <origins>`	指定允许访问 HTTP 和 WS RPC 服务器的浏览器来源。protocol://domain您可以使用语法null、 或将此选项指定为以逗号分隔的来源列表all。值all禁用来源验证。默认情况下，localhost允许https://polkadot.js.org来源。如果您使用该--dev选项启动节点，则默认情况下允许所有来源。
`--rpc-http-threads <count>`	指定 RPC HTTP 服务器线程池的大小。
`--rpc-max-payload <rpc-max-payload>`	设置请求和响应（HTTP 和 Web 套接字）的最大 RPC 负载大小，以兆字节为单位。默认值为 15 MiB。
`--rpc-methods <method-set>` 指定要公开的 RPC 方法。有效值是Unsafe公开每个 RPC 方法，Safe仅公开 RPC 方法的安全子集，拒绝不安全的 RPC 方法，或者如果 RPC 是在外部提供服务Auto时公开SafeRPC 方法，例如，如果您使用--rpc-externalor运行节点--rpc-external，或者在以下情况下公开UnsafeRPC 方法RPC 不在外部提供。默认值为Auto.
`--rpc-port <port>`	指定用于 HTTP RPC 服务器的 TCP 端口。
`--state-cache-size <bytes>`	指定状态缓存大小。默认值为 67108864 字节。
`--sync <sync-mode>`	指定区块链同步模式 有效值是Full下载和验证完整的区块链历史，Fast仅下载块和最新状态，或FastUnsafe下载最新状态但跳过下载状态证明。默认值为Full.
`--telemetry-url <url verbosity>`...	指定要连接的遥测服务器的 URL。您可以多次传递此标志以指定多个遥测端点。详细级别范围为 0-9，其中 0 表示最不详细。使用以下格式指定详细程度选项后跟的 URL 是--telemetry-url 'wss://foo/bar 0'.
`--tracing-receiver <receiver>`	指定接收者来处理跟踪消息。默认为日志。
`--tracing-targets <targets>`	设置自定义分析过滤器。语法与日志记录相同：`<target>=<level>`.
`--wasm-execution <method>`	指定执行 Wasm 运行时代码的方法。有效值为interpreted, 或compiled。默认值为Compiled.
`--wasm-runtime-overrides <path>`	指定存储本地 WASM 运行时的路径。当版本匹配时，这些运行时会覆盖链上运行时。
`--ws-max-connections <count>`	指定 WS RPC 服务器连接的最大数量。
`--ws-port <port>`	指定用于 WebSockets RPC 服务器的 TCP 端口。
子命令
您可以将以下子命令与该node-template命令一起使用。有关说明使用这些子命令的参考信息和示例，请选择适当的命令。

命令	描述
benchmark	对运行时托盘进行基准测试。
build-spec	构建链规范。
check-block	验证块。
export-blocks	导出块。
export-state	将给定块的状态导出到链规范中。
help	显示node-template指定子命令的使用信息。
import-blocks	导入块。
key	提供本地密钥管理实用程序。
purge-chain	删除所有链数据。
revert	将链恢复到以前的状态。
基准
使用该node-template benchmark命令分析在运行时托盘中配置的外部调用中执行事务所需的资源。您可以分析特定托盘中的单个外部调用或所有托盘中的所有外部调用。使用该benchmark子命令，您可以使用其他命令行选项来测试不同的执行场景并比较结果。

请注意，您必须在启用基准测试的情况下编译节点才能使用node-template benchmark. 要编译启用了基准测试功能的节点，请运行以下命令：
```
cargo build --package node-template --release --features runtime-benchmarks
```
基本命令用法

`node-template benchmark [subcommand] [flags] [options]`

根据您指定的子命令，可能会应用或需要其他参数、选项和标志。要查看特定benchmark子命令的使用信息，请指定子命令和--help标志。例如，要查看 的使用信息benchmark pallet，您可以运行以下命令：
```
node-template benchmark pallet --help
```
子命令
您可以将以下子命令与该node-template benchmark命令一起使用。

命令	描述
block	对历史块的执行时间进行基准测试。
help	显示node-template benchmark指定子命令的使用信息。
overhead	对每个块和每个外部的执行开销进行基准测试。
pallet	对 FRAME 托盘的外在重量进行基准测试。
storage	对链快照的存储速度进行基准测试。
标志
node-template benchmark您可以在命令中使用以下可选标志。

旗帜	描述
-h,--help	显示使用信息。
-V,--version	显示版本信息。
选项
您可以将所有常见的节点模板命令行选项与node-template benchmark子命令结合使用。例如，您可以使用`--base-path <path>`为区块链数据指定自定义目录，并`--chain <chain-specification>`指定要与任何子benchmark命令一起使用的链规范。

但是，有许多命令行选项专门用于执行基准测试任务。例如，node-template benchmark block子命令支持用于指定要分析的块--from的--to命令行选项。

因为基准测试 FRAME 托盘代表最常见的基准测试任务，所以该node-template benchmark pallet子命令支持最特定于任务的命令行选项。例如，您可以将以下选项与node-template benchmark pallet子命令一起使用。

选项	描述
`--external-repeat`	指定为客户端重复执行基准测试的次数。请注意，此选项可能会产生较慢的结果，但会最大化 Wasm 内存。默认是一次执行。
--extra	显示并运行额外的基准，否则重量构建将不需要这些基准。
`-e,--extrinsic <extrinsic>`	指定托盘中的单个函数进行基准测试，或*对托盘中的所有函数调用进行基准测试。
`--header <header>`	将头文件添加到您的基准测试输出。
`--heap-pages <heap-pages>`	在运行基准测试时设置堆页面。如果未设置，则使用节点的默认值。
`--high <highest-range-values>`...	表示每个组件范围的最高值。
--list	列出所有当前定义的基准而不运行它们。
`--low <lowest-range-values>`...	表示每个组件范围的最低值。
--no-median-slopes	禁用中值斜率线性回归分析。
--no-min-squares	禁用最小平方线性回归分析。
--no-storage-info	禁用分析输出中存储信息的显示。这与输出文件中出现的存储信息无关。为此目的使用 Handlebar 模板。
--no-verify	运行基准测试时禁用验证逻辑。
`--output <output>`	将基准输出到给定路径的 Rust 文件。
`--output-analysis <analysis-type>`	指定要在基准输出中使用的分析函数。有效值为min-squares、median-slopes或max。默认为min-squares分析。有关基准分析的更多信息，请参阅基准。
`-p,--pallet <pallet>`	指定要对 FRAME 托盘进行基准测试，或*对所有托盘进行基准测试。如果您对所有托盘进行基准测试，您还必须指定--extrinsic *对所有外部调用进行基准测试。
--record-proof	估计验证证明 (PoV) 大小。
`-r,--repeat <repeat>`	指定从 WebAssembly 二进制文件中重复执行基准测试的次数。默认是一次执行。
`-s,--steps <steps>`	指定要在变量组件中采集的样本数。默认为一个样本。
`--template <template>`	指定用于输出基准测试结果的 Handlebars 模板文件的路径。
有关不同基准测试子命令和相关命令行选项的示例，请参阅基准测试示例。

基准测试示例
编译运行时并启用基准测试后，您可以运行类似于以下的命令来对所有配置了运行时基准测试的托盘中的所有函数调用进行基准测试：
```
./target/release/node-template benchmark pallet \
    --chain dev \
    --extrinsic "*" \
    --pallet "*" \
    --output pallets/weights.rs
```
使用此命令，每个函数调用都会使用单个值执行一次，并且生成的权重会记录在weights.rs文件中。

根据您要进行基准测试的功能，您可以添加--steps和--repeat命令行选项以使用不同的值多次执行调用。例如下面的命令执行中的do_something函数，pallet_template调用该函数20次取10个数据点：
```
./target/release/node-template benchmark pallet \
    --chain dev \
    --extrinsic do_something \
    --pallet pallet_template \
    --steps 10 \
    --repeat 20 \
    --list
```
使用该--list选项，该命令将显示以下输出：
```
pallet, benchmark
pallet_template, do_something
```
使用--steps和--repeat命令行选项，该命令显示以下基准测试结果：
```
Pallet: "pallet_template", Extrinsic: "do_something", Lowest values: [], Highest values: [], Steps: 10, Repeat: 20
Raw Storage Info
========
Storage: TemplateModule Something (r:0 w:1)

Median Slopes Analysis
========
-- Extrinsic Time --

Model:
Time ~=       12
    + s        0
              µs

Reads = 0 + (0 * s)
Writes = 1 + (0 * s)

Min Squares Analysis
========
-- Extrinsic Time --

Data points distribution:
    s   mean µs  sigma µs       %
    0      12.4     0.489    3.9%
   10      12.1       0.3    2.4%
   20      12.1       0.3    2.4%
   30      11.8       0.4    3.3%
   40        12         0    0.0%
   50      11.9       0.3    2.5%
   60        12         0    0.0%
   70      12.2       0.4    3.2%
   80        12         0    0.0%
   90        12         0    0.0%
  100        12         0    0.0%

Quality and confidence:
param     error
s             0

Model:
Time ~=    12.13
    + s        0
              µs

Reads = 0 + (0 * s)
Writes = 1 + (0 * s)
```
要测量每个块和每个外部的平均、中值、最小和最大执行时间，您可以运行node-template benchmark overhead子命令：
```
./target/release/node-template benchmark overhead
```
该命令显示类似于以下内容的输出：
```
Running 10 warmups...    
Executing block 100 times    
Per-block execution overhead [ns]:
	Total: 81779519
	Min: 695173, Max: 1522917
	Average: 817795, Median: 763360, Stddev: 160759.2
	Percentiles 99th, 95th, 75th: 1443343, 1263935, 814356    
	Writing weights to "block_weights.rs"    
Running 10 warmups...    
Executing block 100 times    
Building block, this takes some time...    
Extrinsics per block: 12000    
Running 10 warmups...    
Executing block 100 times
Per-extrinsic execution overhead [ns]:
	Total: 8955542
	Min: 85950, Max: 97448
	Average: 89555, Median: 88348, Stddev: 2713.05
	Percentiles 99th, 95th, 75th: 97247, 95040, 91733    
	Writing weights to "extrinsic_weights.rs"
```
默认情况下，该命令执行基准测试 100 次，生成结果，并将输出写入block_weights.rs和extrinsics_weights.rs文件。您可以使用命令行选项通过添加单位或将平均执行时间乘以某个因子来调整计算的权重。

要测量 Substrate 开发链规范的存储执行时间，可以运行以下命令：
```
./target/release/node-template benchmark storage --state-version 1
```
该命令显示类似于以下内容的输出：
```
Warmup round 1/1    
Preparing keys from block BlockId::Number(0)    
Reading 36 keys    
Time summary [ns]:
	Total: 478367
	Min: 8889, Max: 32258
	Average: 13287, Median: 12580, Stddev: 4189.73
	Percentiles 99th, 95th, 75th: 32258, 21863, 14563
Value size summary:
	Total: 157217
	Min: 1, Max: 155944
	Average: 4367, Median: 16, Stddev: 25621.19
	Percentiles 99th, 95th, 75th: 155944, 82, 80    
Warmup round 1/1    
Preparing keys from block BlockId::Number(0)    
Writing 36 keys    
Time summary [ns]:
	Total: 1877401
	Min: 13476, Max: 665226
	Average: 52150, Median: 25740, Stddev: 111805.24
	Percentiles 99th, 95th, 75th: 665226, 252697, 30427
Value size summary:
	Total: 157217
	Min: 1, Max: 155944
	Average: 4367, Median: 16, Stddev: 25621.19
	Percentiles 99th, 95th, 75th: 155944, 82, 80    
Writing weights to "rocksdb_weights.rs"
```
要获取paritydb数据库而不是默认rocksdb数据库的基准测试信息，请使用--db paritydb命令行选项。要获取 Polkadot 或任何其他真实链快照的存储基准测试信息，请使用命令行选项--state-version 0。有关使用 benchmark storage 子命令的更多信息，请参阅benchmark storage 命令。

有关如何向运行时添加基准测试的更多信息，请参阅基准测试和添加基准测试。

构建规范
使用该node-template build-spec命令为您的运行时创建链规范文件。

基本命令用法
`node-template build-spec [flags] [options]`

标志
node-template build-spec您可以在命令中使用以下可选标志。

旗帜	描述
--detailed-log-output	启用详细的日志输出，包括日志目标名称、日志级别和线程名称。如果您启用的日志级别高于info.
--dev	以开发模式启动节点。默认情况下，使用此标志还会启用--chain=dev、--force-authoring、--rpc-cors=all、--alice和--tmp标志。
--disable-default-bootnode	禁止将默认引导节点添加到规范中。默认情况下，/ip4/127.0.0.1/tcp/30333/p2p/NODE_PEER_ID当不存在引导节点时，将引导节点添加到规范中。
--disable-log-color	禁用日志颜色输出。
--enable-log-reloading	使日志过滤器能够动态更新和重新加载。请注意，此选项会显着降低性能。设置此选项不会影响system_addLogFilter和system_resetLogFilterRPC 方法。
-h,--help	显示使用信息。
--raw	将链规范格式化为原始创世存储输出。
-V,--version	显示版本信息。
选项
您可以在命令中使用以下命令行选项node-template build-spec。

选项	描述
`-d,--base-path <path>`	指定自定义基本路径。
`--chain <chain-specification>`	指定要使用的链规范。您可以使用预定义的链规范名称设置此选项，例如dev, local，staging或者您可以指定包含链规范的文件的路径，例如使用build-spec子命令生成的链规范。
`-l,--log <log-pattern>`...	设置自定义日志过滤器。`<log-target>=<level>`例如，要使用的语法是-lsync=debug。从最详细到最详细的有效日志级别是error、warn、info、debug和trace。默认情况下，所有目标都记录info级别消息。您可以使用 设置全局日志级别`-l<level>`。
`--node-key <key>`	指定用于libp2p网络的密钥。该值是一个基于--node-key-type. 例如，如果节点密钥类型为ed25519，则节点密钥被解析为十六进制编码的 Ed25519 32 字节密钥（64 个十六进制字符）。此选项的值优先于--node-key-file。请注意，作为命令行参数提供的秘密很容易暴露。您应该只将此选项用于开发和测试。要使用外部管理的密钥，请使用该--node-key-file选项。
`--node-key-file <file>`	指定包含用于libp2p网络的节点的密钥的文件。文件的内容是根据--node-key-type. 例如，如果节点密钥类型为ed25519，则文件必须包含未编码的 32 字节或十六进制编码的 Ed25519 密钥。--node-key-type如果该文件不存在，则使用您使用该选项指定的类型的新生成的密钥创建它。
`--node-key-type <type>`	指定用于点对点 ( libp2p) 网络的密钥类型。--node-key您可以使用该选项在命令行上指定密钥，使用该选项从文件中读取密钥，或者从该选项指定的基本目录内--node-key-file的链特定目录中指定的文件中读取密钥。如果此文件不存在，则使用新生成的所选类型的密钥创建它。节点的密钥决定了公钥——对等标识符——用于使用库与节点通信。默认类型为 Ed25519。config--base-dirlibp2p
`--tracing-receiver <receiver>`	指定接收者来处理跟踪消息。默认为日志。
`--tracing-targets <targets>`	设置自定义分析过滤器。语法与日志记录相同：`<target>=<level>`.
例子
要将预定义的local链规范导出到名为 的文件customSpec.json，您可以运行以下命令：
```
./target/release/node-template build-spec --chain local > customSpec.json
```
如果您之前创建了一个包含自定义链规范的 JSON 文件，则可以指定该文件的路径并使用--raw命令行选项导出链规范以及节点用来引用其本地数据的编码存储密钥贮存。
```
./target/release/node-template build-spec --chain ./my-test-chain.json --raw
```
检查块
使用该node-template check-block命令验证特定块。您必须通过块哈希或块号指定要验证的块。

基本命令用法
`node-template check-block [flags] [options] <block-identifier>`

标志
node-template check-block您可以在命令中使用以下可选标志。

旗帜	描述
--detailed-log-output	启用详细的日志输出，包括日志目标名称、日志级别和线程名称。如果您启用的日志级别高于info.
--dev	以开发模式启动节点。默认情况下，使用此标志还会启用--chain=dev、--force-authoring、--rpc-cors=all、--alice和--tmp标志。
--disable-log-color	禁用日志颜色输出。
--enable-log-reloading	使日志过滤器能够动态更新和重新加载。请注意，此选项会显着降低性能。设置此选项不会影响system_addLogFilter和system_resetLogFilterRPC 方法。
-h,--help	显示使用信息。
--storage-chain	更改块的存储格式。如果您指定此选项，则每个交易都单独存储在交易数据库列中，并且仅由其在块体列中的哈希引用。
--unsafe-pruning	强制节点在启用修剪的情况下启动。默认情况下，验证器节点禁用状态修剪。要启动启用修剪的验证器节点（也称为存档模式），您必须设置此选项。
-V,--version	显示版本信息。
选项
您可以在命令中使用以下命令行选项node-template check-block。

选项	描述
`-d,--base-path <path>`	指定自定义基本路径。
`--chain <chain-specification>`	指定要使用的链规范。您可以使用预定义的链规范名称设置此选项，例如dev, local，staging或者您可以指定包含链规范的文件的路径，例如使用build-spec子命令生成的链规范。
`--database <database>`	选择要使用的数据库后端。有效值为rocksdb、paritydb-experimental或auto。
`--db-cache <MiB>`	限制数据库缓存可以使用的内存量。默认值为 128 MiB。
`--default-heap-pages <count>`	指定要为 Wasm 执行分配的默认 64KB 页数。除非您知道自己在做什么，否则不应使用此选项。
`--execution <strategy>`	确定所有执行上下文使用的执行策略。有效值为Native、Wasm、Both或NativeElseWasm。
`--execution-block-construction <strategy>`	确定调用运行时构造块时使用的执行策略。有效值为Native、Wasm、Both或NativeElseWasm。
`--execution-import-block <strategy>`	确定调用运行时以导入块（包括本地创作的块）时使用的执行策略。有效值为Native、Wasm、Both或NativeElseWasm。
`--execution-offchain-worker <strategy>`	确定调用运行时以使用脱链工作者时使用的执行策略。有效值为Native、Wasm、Both或NativeElseWasm。
`--execution-other <strategy>`	确定在调用运行时以进行同步、导入或构造块以外的操作时使用的执行策略。有效值为Native、Wasm、Both或NativeElseWasm。
`--execution-syncing <strategy>`	确定调用运行时同步块时使用的执行策略。有效值为Native、Wasm、Both或NativeElseWasm。
`--keep-blocks <count>`	指定要保存在数据库中的最终块的数量。默认是保留所有块。
`-l,--log <log-pattern>`...	设置自定义日志过滤器。`<log-target>=<level>`例如，要使用的语法是-lsync=debug。从最详细到最详细的有效日志级别是error、warn、info、debug和trace。默认情况下，所有目标都记录info级别消息。您可以使用 设置全局日志级别`-l<level>`。
`--pruning <pruning-mode>`	指定要保留或archive保留所有方块状态的最大方块状态数。如果节点作为验证者运行，则默认保留所有块状态。如果节点不作为验证者运行，则仅保留最后 256 个块的状态。
`--state-cache-size <bytes>`	指定状态缓存大小。默认值为 67108864 字节。
`--tracing-receiver <receiver>`	指定接收者来处理跟踪消息。默认为日志。
`--tracing-targets <targets>`	设置自定义分析过滤器。语法与日志记录相同：`<target>=<level>`.
`--wasm-execution <method>`	指定执行 Wasm 运行时代码的方法。有效值为interpreted, 或compiled。默认值为Compiled.
`--wasm-runtime-overrides <path>`	指定存储本地 WASM 运行时的路径。如果设置此选项，如果运行时版本相同，则节点使用本地运行时而不是链上运行时。
论据
运行命令时必须指定以下命令行参数node-template check-block。

争论	描述
block-identifier	指定要检查的块哈希或块号。
出口块
使用node-template export-blocks命令导出块。

基本命令用法
`node-template export-blocks [flags] [options] [--] [output]`

标志
node-template export-blocks您可以在命令中使用以下可选标志。

旗帜	描述
--binary	将块导出为二进制输出而不是 JSON 文件。
--detailed-log-output	启用详细的日志输出，包括日志目标名称、日志级别和线程名称。如果您启用的日志级别高于info.
--dev	以开发模式启动节点。默认情况下，使用此标志还会启用--chain=dev、--force-authoring、--rpc-cors=all、--alice和--tmp标志。
--disable-log-color	禁用日志颜色输出。
--enable-log-reloading	使日志过滤器能够动态更新和重新加载。请注意，此选项会显着降低性能。设置此选项不会影响system_addLogFilter和system_resetLogFilterRPC 方法。
-h,--help	显示使用信息。
--storage-chain	更改块的存储格式。如果您指定此选项，则每个交易都单独存储在交易数据库列中，并且仅由其在块体列中的哈希引用。
-V,--version	显示版本信息。
选项
您可以在命令中使用以下命令行选项node-template export-blocks。

选项	描述
`-d,--base-path <path>`	指定自定义基本路径。
`--chain <chain-specification>`	指定要使用的链规范。您可以使用预定义的链规范名称设置此选项，例如dev, local，staging或者您可以指定包含链规范的文件的路径，例如使用build-spec子命令生成的链规范。
`--database <database>`	选择要使用的数据库后端。有效值为rocksdb、paritydb-experimental或auto。
`--db-cache <MiB>`	限制数据库缓存可以使用的内存量。默认值为 128 MiB。
`--from <block>`	指定开始导出的块编号。默认值为第一个块 (1)。
`--keep-blocks <count>`	指定要保存在数据库中的最终块的数量。默认是保留所有块。
`-l,--log <log-pattern>`...	设置自定义日志过滤器。`<log-target>=<level>`例如，要使用的语法是-lsync=debug。从最详细到最详细的有效日志级别是error、warn、info、debug和trace。默认情况下，所有目标都记录info级别消息。您可以使用 设置全局日志级别`-l<level>`。
`--pruning <pruning-mode>`	指定要保留或archive保留所有方块状态的最大方块状态数。如果节点作为验证者运行，则默认保留所有块状态。如果节点不作为验证者运行，则仅保留最后 256 个块的状态。
`--to <block>`	指定要导出的最后一个块编号。默认为最佳块。
`--tracing-receiver <receiver>`	指定接收者来处理跟踪消息。默认为日志。
`--tracing-targets <targets>`	设置自定义分析过滤器。语法与日志记录相同：`<target>=<level>`.
论据
您可以在运行node-template export-blocks命令时指定以下命令行参数。

争论	描述
`<output>`	指定导出块的输出文件名。如果您不指定输出文件名，则将块导出到标准输出 ( stdout)。
出口国
使用该node-template export-state命令将给定块的状态导出到链规范中。

基本命令用法
`node-template export-state [flags] [options] [--] [block-identifier]`

标志
node-template export-state您可以在命令中使用以下可选标志。

旗帜	描述
--detailed-log-output	启用详细的日志输出，包括日志目标名称、日志级别和线程名称。如果您启用的日志级别高于info.
--dev	以开发模式启动节点。默认情况下，使用此标志还会启用--chain=dev、--force-authoring、--rpc-cors=all、--alice和--tmp标志。
--disable-log-color	禁用日志颜色输出。
--enable-log-reloading	使日志过滤器能够动态更新和重新加载。请注意，此选项会显着降低性能。设置此选项不会影响system_addLogFilter和system_resetLogFilterRPC 方法。
-h,--help	显示使用信息。
-V,--version	显示版本信息。
选项
您可以在命令中使用以下命令行选项node-template export-state。

选项	描述
`-d,--base-path <path>`	指定自定义基本路径。
`--chain <chain-specification>`	指定要使用的链规范。您可以使用预定义的链规范名称设置此选项，例如dev, local，staging或者您可以指定包含链规范的文件的路径，例如使用build-spec子命令生成的链规范。
`--keep-blocks <count>`	指定要保存在数据库中的最终块的数量。默认是保留所有块。
`-l,--log <log-pattern>`...	设置自定义日志过滤器。`<log-target>=<level>`例如，要使用的语法是-lsync=debug。从最详细到最详细的有效日志级别是error、warn、info、debug和trace。默认情况下，所有目标都记录info级别消息。您可以使用 设置全局日志级别`-l<level>`。
`--pruning <pruning-mode>`	指定要保留或archive保留所有方块状态的最大方块状态数。如果节点作为验证者运行，则默认保留所有块状态。如果节点不作为验证者运行，则仅保留最后 256 个块的状态。
`--tracing-receiver <receiver>`	指定接收者来处理跟踪消息。默认为日志。
`--tracing-targets <targets>`	设置自定义分析过滤器。语法与日志记录相同：`<target>=<level>`.
论据
您可以在运行node-template export-state命令时指定以下命令行参数。

争论	描述
`<block-identifier>`	指定要导出的块哈希或块号。
帮助
使用该node-template help命令可以显示node-template任何子命令的使用信息或命令行使用信息的摘要node-template。

基本命令用法
`node-template help [subcommand]`

例子
要显示子命令的使用信息摘要export-blocks，请运行以下命令：
```
node-template help export-blocks
```
导入块
使用node-template import-blocks命令导入块。

基本命令用法
`node-template import-blocks [flags] [options] [--] [input]`

标志
node-template import-blocks您可以在命令中使用以下可选标志。

旗帜	描述
--binary	尝试以二进制格式而不是从 JSON 文件导入块。
--detailed-log-output	启用详细的日志输出，包括日志目标名称、日志级别和线程名称。如果您启用的日志级别高于info.
--dev	以开发模式启动节点。默认情况下，使用此标志还会启用--chain=dev、--force-authoring、--rpc-cors=all、--alice和--tmp标志。
--disable-log-color	禁用日志颜色输出。
--enable-log-reloading	使日志过滤器能够动态更新和重新加载。请注意，此选项会显着降低性能。设置此选项不会影响system_addLogFilter和system_resetLogFilterRPC 方法。
-h,--help	显示使用信息。
--storage-chain	更改块的存储格式。如果您指定此选项，则每个交易都单独存储在交易数据库列中，并且仅由其在块体列中的哈希引用。
--unsafe-pruning	强制节点在启用修剪的情况下启动。默认情况下，验证器节点禁用状态修剪。要启动启用修剪的验证器节点（也称为存档模式），您必须设置此选项。
-V,--version	显示版本信息。
选项
您可以在命令中使用以下命令行选项node-template import-blocks。

选项	描述
`-d,--base-path <path>`	指定自定义基本路径。
`--chain <chain-specification>`	指定要使用的链规范。您可以使用预定义的链规范名称设置此选项，例如dev, local，staging或者您可以指定包含链规范的文件的路径，例如使用build-spec子命令生成的链规范。
`--database <database>`	选择要使用的数据库后端。有效值为rocksdb、paritydb-experimental或auto。
`--db-cache <MiB>`	限制数据库缓存可以使用的内存量。默认值为 128 MiB。
`--default-heap-pages <count>`	指定要为 Wasm 执行分配的默认 64KB 页数。在大多数情况下，您不应使用此选项。
`--execution <strategy>`	确定所有执行上下文使用的执行策略。有效值为Native、Wasm、Both或NativeElseWasm。
`--execution-block-construction <strategy>`	确定调用运行时构造块时使用的执行策略。有效值为Native、Wasm、Both或NativeElseWasm。
`--execution-import-block <strategy>`	确定调用运行时以导入块（包括本地创作的块）时使用的执行策略。有效值为Native、Wasm、Both或NativeElseWasm。
`--execution-offchain-worker <strategy>`	确定调用运行时以使用脱链工作者时使用的执行策略。有效值为Native、Wasm、Both或NativeElseWasm。
`--execution-other <strategy>`	确定在调用运行时以进行同步、导入或构造块以外的操作时使用的执行策略。有效值为Native、Wasm、Both或NativeElseWasm。
`--execution-syncing <strategy>`	确定调用运行时同步块时使用的执行策略。有效值为Native、Wasm、Both或NativeElseWasm。
`--keep-blocks <count>`	指定要保存在数据库中的最终块的数量。默认是保留所有块。
`-l,--log <log-pattern>`...	设置自定义日志过滤器。`<log-target>=<level`>例如，要使用的语法是-lsync=debug。从最详细到最详细的有效日志级别是error、warn、info、debug和trace。默认情况下，所有目标都记录info级别消息。您可以使用 设置全局日志级别`-l<level>`。
`--pruning <pruning-mode>`	指定要保留或archive保留所有方块状态的最大方块状态数。如果节点作为验证者运行，则默认保留所有块状态。如果节点不作为验证者运行，则仅保留最后 256 个块的状态。
`--state-cache-size <bytes>`	指定状态缓存大小。默认值为 67108864 字节。
`--tracing-receiver <receiver>`	指定接收者来处理跟踪消息。默认为日志。
`--tracing-targets <targets>`	设置自定义分析过滤器。语法与日志记录相同：`<target>=<level>`.
`--wasm-execution <method>`	指定执行 Wasm 运行时代码的方法。有效值为interpreted, 或compiled。默认值为Compiled.
`--wasm-runtime-overrides <path>`	指定存储本地 WASM 运行时的路径。如果设置此选项，如果运行时版本相同，则节点使用本地运行时而不是链上运行时。
论据
您可以在运行node-template import-blocks命令时指定以下命令行参数。

争论	描述
`<input>`	指定用于导入块的输入文件。如果您不指定输入文件，则从标准输入 ( stdin) 导入块。
钥匙
使用该node-template key命令生成、检查和管理私钥和公钥对和地址。该命令提供对独立程序node-template key中可用的密钥管理服务子集的方便访问。subkey有关大多数node-template key子命令的子命令和命令行选项的完整详细信息，请参阅subkey. 尽管大多数node-template key子命令与 [ subkey] 子命令相同，但该node-template key insert子命令不是受支持的 [ subkey] 子命令。该node-template key insert子命令使您能够将生成的密钥直接添加到节点密钥库。有关与子命令一起使用的命令行选项和参数的信息node-template key insert，请参阅在节点上插入密钥或运行以下命令：
```
node-template key insert --help
```
基本命令用法
`node-template key [subcommand] [flags]`

标志
node-template key您可以在命令中使用以下可选标志。

旗帜	描述
-h,--help	显示使用信息。
-V,--version	显示版本信息。
子命令
您可以将以下子命令与该node-template key命令一起使用。

命令	描述
generate	生成随机帐户密钥。
generate-node-key	生成随机节点libp2p密钥。
help	显示指定子命令的使用信息。
insert	将帐户或节点密钥添加到本地节点上的密钥库。
inspect	显示您指定的秘密 URI 的公钥和 SS58 地址。
inspect-node-key	显示与您指定的文件名中的秘密节点密钥对应的对等 ID。
在节点上插入密钥
使用该node-template key insert命令将用于执行节点操作的密钥添加到节点密钥库。例如，需要密钥来保护节点之间的点对点通信，并使节点能够充当共识的验证机构。

基本命令用法
`node-template key insert [FLAGS] [OPTIONS] --key-type <key-type> --scheme <SCHEME>`

标志
node-template key insert您可以在命令中使用以下可选标志。

旗帜	描述
--detailed-log-output	启用详细的日志输出，包括日志目标名称、日志级别和线程名称。如果您启用的日志级别高于info.
--dev	以开发模式启动节点。默认情况下，使用此标志还会启用--chain=dev、--force-authoring、--rpc-cors=all、--alice和--tmp标志。
--disable-log-color	禁用日志颜色输出。
--enable-log-reloading	使日志过滤器能够动态更新和重新加载。请注意，此选项会显着降低性能。设置此选项不会影响system_addLogFilter和system_resetLogFilterRPC 方法。
-h,--help	显示使用信息。
--password-interactive	显示交互式提示，让您在终端 shell 中输入密码以访问密钥库。
-V,--version	显示版本信息。
选项
您可以在命令中使用以下命令行选项node-template key insert。

选项	描述
`-d,--base-path <path>`	指定自定义基本路径。
`--chain <chain-specification>`	指定要使用的链规范。您可以使用预定义的链规范名称设置此选项，例如dev, local，staging或者您可以指定包含链规范的文件的路径，例如使用build-spec子命令生成的链规范。
`--key-type <key-type>`	指定密钥授权的节点活动类型。例如，有效的密钥类型包括aura授权节点生成块、gran授权节点完成块以及imon发送表明节点在线的心跳交易。
`--keystore-path <path>`	指定自定义密钥库的路径。
`--keystore-uri <keystore-uri>`	为密钥库服务指定要连接的自定义 URI。
`-l,--log <log-pattern>`...	设置自定义日志过滤器。`<log-target>=<level>`例如，要使用的语法是-lsync=debug。从最详细到最详细的有效日志级别是error、warn、info、debug和trace。默认情况下，所有目标都记录info级别消息。您可以使用 设置全局日志级别`-l<level>`。
`--password <password>`	使您能够向密钥库所需的密钥添加额外的用户定义密钥。
`--password-filename <path>`	指定包含用于访问密钥库的密码的文件。
`--scheme <scheme>`	指定用于从给定 URI 生成密钥的加密方案。有效值为Ed25519、Sr25519或Ecdsa。
`--suri <suri>`	指定密钥 URI。如果为此选项指定文件，则文件内容将用作 URI。如果不指定此选项，系统会提示您指定 URI。
`--tracing-receiver <receiver>`	指定接收者来处理跟踪消息。默认为日志。
`--tracing-targets <targets>`	设置自定义分析过滤器。语法与日志记录相同：`<target>=<level>`.
净化链
使用该node-template purge-chain命令删除区块链和所有与区块链相关的信息。

基本命令用法
`node-template purge-chain [flags] [options]`

标志
node-template purge-chain您可以在命令中使用以下可选标志。

旗帜	描述
--detailed-log-output	启用详细的日志输出，包括日志目标名称、日志级别和线程名称。如果您启用的日志级别高于info.
--dev	以开发模式启动节点。默认情况下，使用此标志还会启用--chain=dev、--force-authoring、--rpc-cors=all、--alice和--tmp标志。
--disable-log-color	禁用日志颜色输出。
--enable-log-reloading	使日志过滤器能够动态更新和重新加载。请注意，此选项会显着降低性能。设置此选项不会影响system_addLogFilter和system_resetLogFilterRPC 方法。
-h,--help	显示使用信息。
--storage-chain	更改块的存储格式。如果您指定此选项，则每个交易都单独存储在交易数据库列中，并且仅由其在块体列中的哈希引用。
| -V, --version| 显示版本信息。 -y| 提供抢先yes响应以跳过交互式提示以确认您要删除链。

选项
您可以在命令中使用以下命令行选项node-template purge-chain。

选项	描述
`-d,--base-path <path>`	指定自定义基本路径。
`--chain <chain-specification>`	指定要使用的链规范。您可以使用预定义的链规范名称设置此选项，例如dev, local，staging或者您可以指定包含链规范的文件的路径，例如使用build-spec子命令生成的链规范。
`--database <database>`	选择要使用的数据库后端。有效值为rocksdb、paritydb-experimental或auto。
`--db-cache <MiB>`	限制数据库缓存可以使用的内存量。默认值为 128 MiB。
`-l,--log <log-pattern>`...	设置自定义日志过滤器。`<log-target>=<level>`例如，要使用的语法是-lsync=debug。从最详细到最详细的有效日志级别是error、warn、info、debug和trace。默认情况下，所有目标都记录info级别消息。您可以使用 设置全局日志级别`-l<level>`。
`--tracing-receiver <receiver>`	指定接收者来处理跟踪消息。默认为日志。
`--tracing-targets <targets>`	设置自定义分析过滤器。语法与日志记录相同：`<target>=<level>`.
恢复
使用该node-template revert命令将链恢复到以前的状态。

基本命令用法
`node-template revert [flags] [options] [--] [num]`

标志
node-template revert您可以在命令中使用以下可选标志。

旗帜	描述
--detailed-log-output	启用详细的日志输出，包括日志目标名称、日志级别和线程名称。如果您启用的日志级别高于info.
--dev	以开发模式启动节点。默认情况下，使用此标志还会启用--chain=dev、--force-authoring、--rpc-cors=all、--alice和--tmp标志。
--disable-log-color	禁用日志颜色输出。
--enable-log-reloading	使日志过滤器能够动态更新和重新加载。请注意，此选项会显着降低性能。设置此选项不会影响system_addLogFilter和system_resetLogFilterRPC 方法。
-h,--help	显示使用信息。
-V,--version	显示版本信息。
选项
您可以在命令中使用以下命令行选项node-template rever。

选项	描述
`-d,--base-path <path>`	指定自定义基本路径。
`--chain <chain-specification>`	指定要使用的链规范。您可以使用预定义的链规范名称设置此选项，例如dev, local，staging或者您可以指定包含链规范的文件的路径，例如使用build-spec子命令生成的链规范。
`--keep-blocks <count>`	指定要保存在数据库中的最终块的数量。默认是保留所有块。
`-l,--log <log-pattern>`...	设置自定义日志过滤器。`<log-target>=<level>`例如，要使用的语法是`-lsync=debug`。从最详细到最详细的有效日志级别是error、warn、info、debug和trace。默认情况下，所有目标都记录info级别消息。您可以使用 设置全局日志级别`-l<level>`。
`--pruning <pruning-mode>`	指定要保留或archive保留所有方块状态的最大方块状态数。如果节点作为验证者运行，则默认保留所有块状态。如果节点不作为验证者运行，则仅保留最后 256 个块的状态。
`--tracing-receiver <receiver>`	指定接收者来处理跟踪消息。默认为日志。
`--tracing-targets <targets>`	设置自定义分析过滤器。语法与日志记录相同：`<target>=<level>`.
论据
您可以在命令中使用以下命令行参数node-template revert。

争论	描述
`<num>`	指定要还原的块数。默认值为 256 个块。