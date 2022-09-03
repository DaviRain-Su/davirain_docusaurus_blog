# 子文本
该subxt库使您能够通过远程过程调用将事务提交到 Substrate 节点。在使用该subxt库之前，您可以使用独立的subxt命令行界面下载要用作提交交易目标的 Substrate 节点的元数据。该工具使您能够执行对使用库subxt-cli至关重要的两项关键任务：subxt

您可以使用该subxt-cli工具从任何目标 Substrate 节点下载元数据。
您可以使用该subxt-cli工具从任何目标 Substrate 节点的元数据生成运行时 API 代码。
您可以将subxt库和subxt-cli工具用于使用元数据 v14 及更高版本的任何节点。有关如何使用该subxt库的示例，请参见示例文件夹。

安装
要安装subxt-cli：

如有必要，打开终端外壳。
如有必要，请验证您是否拥有 Rust 编译器和工具链。
使用以下命令下载所需的包： cargo install subxt-cli
基本命令用法
运行subxt命令的基本语法是：
```
subxt <SUBCOMMAND>
```
要使用 公开的命令subxt，您必须在本地运行节点或指定要定位的链。如果已提供元数据，则可以在codegen不运行节点的情况下使用子命令。

标志
subxt您可以在命令中使用以下可选标志。

旗帜	描述
-h，--帮助	显示使用信息。
-V，--版本	显示版本信息。
子命令
subxt您可以在命令行界面中使用以下子命令。

命令	描述
codegen	从元数据生成运行时 API 客户端代码。
metadata	从 Substrate 节点下载元数据以用于subxtcodegen。
输出
根据您指定子命令的方式，来自的输出subxt会显示以下部分或全部信息：

这个领域	包含
元数据	包含目标链元数据的文件。
API	带有目标链 API 的文件。
例子
要显示subxt程序的版本信息，请运行以下命令：
```
subxt --version
```
要显示该subxt metadata命令的使用信息，请运行以下命令：
```
subxt metadata --help
```
代码生成器
使用该subxt codegen命令为某些目标 Substrate 节点生成接口。

这对于调试或修改节点的 API 以满足某些硬件约束可能很有用。

基本用法
`subxt codegen [OPTIONS]`

标志
subxt codegen您可以在命令中使用以下可选标志。

旗帜	描述
`-h, --help`	显示使用信息。
`-V, --version`	打印版本信息。
选项
您可以在命令中使用以下命令行选项`subxt codegen`。

选项	描述
`-f, --file <file>`	指定编码元数据文件的路径。
`--url <url>`	指定要查询 codegen 元数据的 Substrate 节点的 URL。
例子
要格式化生成的 API 并将其打印到终端，请运行以下命令：
```
subxt codegen | rustfmt
```
要将生成的 API 保存在文件中，请运行以下命令：
```
subxt codegen | rustfmt --edition=2018 > api.rs
```
元数据
使用subxt metadata命令获取目标 Substrate 节点的元数据。

基本用法
`subxt metadata [OPTIONS]`

标志
subxt metadata您可以在命令中使用以下可选标志。

旗帜	描述
-h, --help	显示使用信息。
-V, --version	打印版本信息。
选项
您可以在命令中使用以下命令行选项subxt metadata。

选项	描述
`-f, --format <format>`	指定要显示的元数据的格式。有效格式为json,hex或bytes. 默认格式为json.
`--url <url>`	指定要查询元数据的 Substrate 节点的 URL。默认网址是http://localhost:9933.
例子
要将本地节点中以字节编码的元数据保存到文件中，请运行以下命令：
```
subxt metadata -f bytes > metadata.scale
```
要将 Rococo 网络中的元数据保存到 JSON 文件，请运行以下命令：
```
subxt metadata --url https://rococo-rpc.polkadot.io:443 > metadata.json
```
要从元数据中的类型数组中查询类型125并以 JSON 格式输出，请运行以下命令：
```
subxt metadata --format json | jq '.[1].V14.types.types | .[125]'
```