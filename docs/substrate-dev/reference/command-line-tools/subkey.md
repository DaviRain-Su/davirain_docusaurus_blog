# 子键
该subkey程序是包含在 Substrate 存储库中的密钥生成和管理实用程序。您可以使用该subkey程序执行以下任务：

生成和检查加密安全的公钥和私钥对。
从秘密短语和原始种子中恢复密钥。
在消息上签名并验证签名。
签署和验证编码交易的签名。
派生分层确定性子密钥对。
签名方案
该subkey程序目前支持以下签名方案：

sr25519：Ristretto 组的 Schorr 签名。
ed25519：曲线 25519 上的 SHA-512 (SHA-2)。
secp256k1：secp256k1 上的 ECDSA 签名。
在基于 Substrate 的网络中，sr25519编码密钥用于生成 SS58 地址作为与区块链交互的公钥。

安装
您无需克隆完整的 Substrate 存储库subkey即可下载、安装和编译。cargo但是，您必须先将 Substrate 构建依赖项添加到您的环境中，然后才能安装subkey为独立的二进制文件。为确保依赖项可用，您可以subkey从 Substrate 存储库的克隆构建二进制文件。

要安装和编译subkey程序：

如有必要，打开终端外壳。
如有必要，请验证您是否拥有 Rust 编译器和工具链。
如有必要，通过运行以下命令克隆 Substrate 存储库：
```
git clone https://github.com/paritytech/substrate.git
```
通过运行以下命令切换到 Substrate 存储库的根目录：
```
cd substrate
```
通过运行以下命令，使用工具链编译subkey程序：nightly
```
cargo +nightly build --package subkey --release
```
由于涉及的软件包数量众多，编译程序可能需要几分钟时间。

通过运行以下命令验证subkey程序是否已准备好使用并查看有关可用选项的信息：
```
./target/release/subkey --help
```
分层确定性键
该subkey程序支持分层确定性密钥。分层确定性 (HD) 密钥使您能够使用父种子在分层树结构中派生子密钥对。在这种分层结构中，从父级派生的每个子级都有自己的密钥对。派生的密钥也可用于派生额外的子密钥对，类似于文件系统如何在分层目录结构中具有嵌套目录。有关如何派生分层确定性密钥的背景信息，请参阅分层确定性钱包的BIP32规范。

有关使用子键命令派生分层确定性键的信息，请参阅使用派生键。

基本命令用法
运行subkey命令的基本语法是：
```
subkey [subcommand] [flag]
```
根据您指定的子命令，可能会应用或需要其他参数、选项和标志。要查看特定subkey子命令的使用信息，请指定子命令和--help标志。例如，要查看 的使用信息subkey inspect，您可以运行以下命令：
```
subkey inspect --help
```
标志
subkey您可以在命令中使用以下可选标志。

旗帜	描述
-h，--帮助	显示使用信息。
-V，--版本	显示版本信息。
子命令
您可以将以下子命令与该subkey命令一起使用。有关说明使用子键子命令的参考信息和示例，请选择适当的命令。

命令	描述
generate	生成随机帐户密钥。
generate-node-key	生成随机节点libp2p密钥。您可以将密钥保存到文件中或将其显示为标准输出 ( stdout)。
help	显示subkey指定子命令的使用信息。
inspect	显示您指定的秘密 URI 的公钥和 SS58 地址。
inspect-node-key	显示与您指定的文件名中的秘密节点密钥对应的对等 ID。
sign	使用您指定的密钥对消息进行签名。
vanity	生成提供虚荣地址的种子。
verify	验证消息的签名对于您指定的公钥或私钥是否有效。
输出
根据您指定的子命令，子键程序的输出会显示以下部分或全部信息：

这个领域	包含
秘密短语	一系列以人类友好的方式对密钥进行编码的英文单词。如果以正确的顺序提供了正确的单词集，则这一系列单词（也称为助记词或助记词）可用于恢复密钥。
秘密种子	恢复密钥对所需的最少信息。秘密种子有时也称为私钥或原始种子。所有其他信息都是根据该值计算的。
公钥（十六进制）	十六进制格式的加密密钥对的公共部分。
公钥 (SS58)	SS58 编码中加密密钥对的公共部分。
帐户ID	十六进制格式的公钥的别名。
SS58 地址	基于公钥的 SS58 编码的公共地址。
例子
要显示subkey程序的版本信息，请运行以下命令：
```
subkey --version
```
要显示该subkey verify命令的使用信息，请运行以下命令：
```
subkey verify --help
```
子键生成
使用该subkey generate命令生成公钥和私钥以及帐户地址。您可以使用命令行选项来生成具有不同签名方案的密钥或具有更多或更少单词的助记词。

基本用法
`subkey generate [flags] [options]`
标志
subkey generate您可以在命令中使用以下可选标志。

旗帜	描述
-h,--help	显示使用信息。
--password-interactive	使您能够在终端中以交互方式输入访问密钥库的密码。
-V,--version	显示版本信息。
选项
您可以在命令中使用以下命令行选项subkey generate。

选项	描述
`--keystore-path <path>`	指定自定义密钥库路径。
`--keystore-uri <keystore-uri>`	为密钥库服务指定要连接的自定义 URI
`-n,--network <network>`	指定要使用的网络地址格式。例如，kusama或polkadot。有关支持的网络的完整列表，请参阅在线使用信息。
`--output-type <format>`	指定要使用的输出格式。有效值为 Json 和 Text。默认输出格式为文本。
`--password <password>`	指定密钥库使用的密码。此选项使您能够将额外的秘密附加到种子。
`--password-filename <path>`	指定包含密钥库使用的密码的文件的名称。
`--scheme <scheme>`	指定要生成的密钥的加密方案。有效值为Ecdsa, Ed25519, Sr25519。默认方案是Sr25519.
`-w,--words <words>`	指定要生成的密钥的秘密短语中的字数。有效值为 12、15、18、21、24。默认情况下，秘密短语由 12 个单词组成。
例子
要生成使用 sr25519 签名方案的新密钥对，请运行以下命令：
```
subkey generate
```
该命令显示类似于以下内容的输出，其中包含 12 个单词的秘密短语：
```
Secret phrase:       bread tongue spell stadium clean grief coin rent spend total practice document
  Secret seed:       0xd5836897dc77e6c87e5cc268abaaa9c661bcf19aea9f0f50a1e149d21ce31eb7
  Public key (hex):  0xb6a8b4b6bf796991065035093d3265e314c3fe89e75ccb623985e57b0c2e0c30
  Account ID:        0xb6a8b4b6bf796991065035093d3265e314c3fe89e75ccb623985e57b0c2e0c30
  Public key (SS58): 5GCCgshTQCfGkXy6kAkFDW1TZXAdsbCNZJ9Uz2c7ViBnwcVg
  SS58 Address:      5GCCgshTQCfGkXy6kAkFDW1TZXAdsbCNZJ9Uz2c7ViBnwcVg
```
该subkey程序根据使用它的网络所需的格式对与公钥/私钥对关联的地址进行不同的编码。如果您想在 Kusama 和 Polkadot 网络上使用相同的私钥，您可以使用该--network选项为 Kusama 和 Polkadot 网络生成单独的地址格式。公钥是相同的，但地址格式是特定于网络的。要为特定网络生成密钥对，请运行类似于以下内容的命令：
```
subkey generate --network picasso
```
该命令显示与输出相同的字段，但使用您指定的网络的地址格式。

要为网络生成使用ed25519签名方案和 24 字秘密短语的更安全的密钥对moonriver，您将运行以下命令：
```
subkey generate --scheme ed25519 --words 24 --network moonriver
```
该命令显示与输出相同的字段，但使用 Ed25519 签名方案、24 字密码短语和moonriver网络地址格式。
```
Secret phrase:       cloth elevator sadness twice arctic adjust axis vendor grant angle face section key safe under fee fine garage pupil hotel museum valve popular motor
  Secret seed:       0x5fa5923c1d6753fa30f268ffd363efb730ca0db906f55bc17efe65cd24f92097
  Public key (hex):  0x3f1da4d35489e3d84739de1490f51b567ad2a62793cca1357e624fbfa534fc85
  Account ID:        0x3f1da4d35489e3d84739de1490f51b567ad2a62793cca1357e624fbfa534fc85
  Public key (SS58): VkFLVqcighJnssSbL4LDSGy5ShJQZvYhm7G8K8W1ZXt96Z5VG
  SS58 Address:      VkFLVqcighJnssSbL4LDSGy5ShJQZvYhm7G8K8W1ZXt96Z5VG
```
要生成受密码保护的密钥，请使用该选项运行subkey generate命令。`--password <password>`例如：
```
subkey generate --password "pencil laptop kitchen cutter"
```
生成需要密码的密钥后，您可以通过在命令行中包含选项和密码字符串或在密码短语末尾--password添加三个斜杠 ( ) 来检索它。///请记住，确保密码、秘密短语和秘密种子的安全并将它们备份在安全的位置非常重要。

子密钥生成节点密钥
使用该subkey generate-node-key命令为 Substrate 节点之间的点对点 ( libp2p) 通信生成随机公钥和私钥。公钥是在链规范文件中使用的对等标识符，或作为命令行参数来识别参与区块链网络的节点。在大多数情况下，您使用命令行选项运行此命令以将私钥保存到文件中。

基本用法
`subkey generate-node-key [flags] [options]`
标志
subkey generate-node-key您可以在命令中使用以下可选标志。

旗帜	描述
-h,--help	显示使用信息。
-V,--version	显示版本信息。
选项
您可以在命令中使用以下命令行选项subkey generate-node-key。

选项	描述
`--file <file-name>`	指定要用于保存为本地节点生成的密钥的文件位置。如果您不指定此选项，则生成的密钥将显示为标准输出 ( stdout)。
例子
要为点对点通信生成随机密钥对并将密钥保存在文件中，请运行类似于以下内容的命令：
```
subkey generate-node-key --file ../generated-node-key
```
此命令显示终端中节点密钥的对等标识符，私钥保存在generated-node-key文件中。在此示例中，保存的密钥在父目录而不是当前工作目录中。

12D3KooWHALHfL7dDBiGTt4JTEAvCbDWts8zHwvcPvJXDF9fxue7
子键帮助
使用该subkey help命令显示subkey指定子命令的使用消息。

基本用法
`subkey help [subcommand]`
例子
要显示子命令的使用信息verify，请运行以下命令：
```
subkey help verify
```
子项检查
使用该subkey inspect命令重新计算指定密钥或助记词的公钥和公共地址。

基本用法
`subkey inspect [flags] [options] uri`
标志
subkey inspect您可以在命令中使用以下可选标志。

旗帜	描述
-h,--help	显示使用信息。
--password-interactive	使您能够在终端中以交互方式输入访问密钥库的密码。
--public	表示uri您指定检查的是十六进制编码的公钥。
-V,--version	显示版本信息。
选项
您可以在命令中使用以下命令行选项subkey inspect。

选项	描述
`--keystore-path <path>`	指定自定义密钥库路径。
`--keystore-uri <keystore-uri>`	为密钥库服务指定要连接的自定义 URI。
`-n,--network <network>`	指定要使用的网络地址格式。例如，kusama或polkadot。有关支持的网络的完整列表，请参阅在线使用信息。
`--output-type <format>`	指定要使用的输出格式。有效值为 Json 和 Text。默认输出格式为文本。
`--password <password>`	指定密钥库使用的密码。此选项使您能够将额外的秘密附加到种子。
`--password-filename <path>`	指定包含密钥库使用的密码的文件的名称。
`--scheme <scheme>`	指定您正在检查的密钥的加密方案。有效值为Ecdsa, Ed25519, Sr25519。默认方案是Sr25519.
论据
您必须在命令中指定以下必需参数subkey inspect。

争论	描述
uri	指定要检查的密钥 URI。您可以使用密钥短语、密钥种子（带有派生路径和密码）、SS58 地址、公钥或十六进制编码的公钥来指定密钥。如果您指定uri使用十六进制编码的公钥，您还必须--public在命令行中包含该标志。如果为 指定文件名uri，则文件内容将用作 URI。
例子
要检查从助记词派生的公钥，您可以运行类似于以下的命令：
```
subkey inspect "caution juice atom organ advance problem want pledge someone senior holiday very"
```
该命令显示类似于以下内容的输出：
```
Secret phrase `caution juice atom organ advance problem want pledge someone senior holiday very` is account:
  Secret seed:       0xc8fa03532fb22ee1f7f6908b9c02b4e72483f0dbd66e4cd456b8f34c6230b849
  Public key (hex):  0xd6a3105d6768e956e9e5d41050ac29843f98561410d3a47f9dd5b3b227ab8746
  Public key (SS58): 5Gv8YYFu8H1btvmrJy9FjjAWfb99wrhV3uhPFoNEr918utyR
  Account ID:        0xd6a3105d6768e956e9e5d41050ac29843f98561410d3a47f9dd5b3b227ab8746
  SS58 Address:      5Gv8YYFu8H1btvmrJy9FjjAWfb99wrhV3uhPFoNEr918utyR
```
要检查从秘密种子派生的公钥，您可以运行类似于以下的命令：
```
subkey inspect 0xc8fa03532fb22ee1f7f6908b9c02b4e72483f0dbd66e4cd456b8f34c6230b849
```
如果您将秘密短语或秘密种子存储在文本文件中，例如，my-secret-key您可以在命令行上指定文件名以传递文件的内容并显示与该秘密短语或秘密种子相关联的公钥。例如，您可以运行类似于以下的命令：
```
subkey inspect my-secret-key
```
要使用十六进制编码的公钥检查公钥，您可以运行类似于以下内容的命令：
```
subkey inspect --public 0xd6a3105d6768e956e9e5d41050ac29843f98561410d3a47f9dd5b3b227ab8746
```
在这种情况下，该命令只显示类似以下的公共信息：
```
Network ID/version: substrate
  Public key (hex):   0xd6a3105d6768e956e9e5d41050ac29843f98561410d3a47f9dd5b3b227ab8746
  Account ID:         0xd6a3105d6768e956e9e5d41050ac29843f98561410d3a47f9dd5b3b227ab8746
  Public key (SS58):  5Gv8YYFu8H1btvmrJy9FjjAWfb99wrhV3uhPFoNEr918utyR
  SS58 Address:       5Gv8YYFu8H1btvmrJy9FjjAWfb99wrhV3uhPFoNEr918utyR
```
该subkey程序根据使用它的网络所需的格式对与公钥/私钥对关联的地址进行不同的编码。如果您在 Kusama 和 Polkadot 网络上使用相同的私钥--network，您可以使用该选项来检查用于特定网络的地址。公钥是相同的，但地址格式是特定于网络的。要检查特定网络的密钥对，请运行类似于以下内容的命令：
```
subkey inspect --network kusama "caution juice atom organ advance problem want pledge someone senior holiday very"
```
在命令输出中，密码短语、秘密种子和公钥是相同的，但是 Kusama 网络的地址是：
`SS58 Address:      HRkCrbmke2XeabJ5fxJdgXWpBRPkXWfWHY8eTeCKwDdf4k6`
要检查 Polkadot 网络上相同私钥的地址，您将运行类似于以下的命令：
```
subkey inspect --network polkadot "caution juice atom organ advance problem want pledge someone senior holiday very"
```
在命令输出中，secret phrase、secret seed、public keys 和 Kusama 网络是一样的，但是 Polkadot 网络的地址是：
`SS58 Address:      15rRgsWxz4H5LTnNGcCFsszfXD8oeAFd8QRsR6MbQE2f6XFF`
要通过指定--password选项和密码来检查受密码保护的密钥，您可以运行类似于以下的命令：
```
subkey inspect "caution juice atom organ advance problem want pledge someone senior holiday very" --password "pencil laptop kitchen cutter"
```
如果--password在命令行中指定选项和密码，则命令输出不会显示使用的密码。
```
Secret phrase `caution juice atom organ advance problem want pledge someone senior holiday very` is account:
  Secret seed:       0xdfc5d5d5235a37fdc907ee1cb720299f96aeb02f9c7c2fcad7ee8c7bfbd2a4db
  Public key (hex):  0xdef8f78b123475265815b65a7c55e105e1ab185f4969954f68d92b7bb67a1045
  Public key (SS58): 5H74SqH1iQCWh5Gumyghh1WJMcmM6TdBHYSK7mKVJbv9NuSK
  Account ID:        0xdef8f78b123475265815b65a7c55e105e1ab185f4969954f68d92b7bb67a1045
  SS58 Address:      5H74SqH1iQCWh5Gumyghh1WJMcmM6TdBHYSK7mKVJbv9NuSK
```
您还可以通过将密码添加///到密码短语来检查受密码保护的密钥。例如，您可以运行类似于以下的命令：
```
subkey inspect "caution juice atom organ advance problem want pledge someone senior holiday very///pencil laptop kitchen cutter"
```
在这种情况下，命令输出显示使用的密码。例如：
```
Secret Key URI `caution juice atom organ advance problem want pledge someone senior holiday very///pencil laptop kitchen cutter` is account:
  Secret seed:       0xdfc5d5d5235a37fdc907ee1cb720299f96aeb02f9c7c2fcad7ee8c7bfbd2a4db
  Public key (hex):  0xdef8f78b123475265815b65a7c55e105e1ab185f4969954f68d92b7bb67a1045
  Public key (SS58): 5H74SqH1iQCWh5Gumyghh1WJMcmM6TdBHYSK7mKVJbv9NuSK
  Account ID:        0xdef8f78b123475265815b65a7c55e105e1ab185f4969954f68d92b7bb67a1045
  SS58 Address:      5H74SqH1iQCWh5Gumyghh1WJMcmM6TdBHYSK7mKVJbv9NuSK
```
子键检查节点键
使用该subkey inspect-node-key命令显示与指定文件名中的节点键对应的节点的对等标识符。在使用此命令之前，您应该以前使用过该subkey generate-node-key命令并将密钥保存到文件中。

基本用法
`subkey inspect-node-key [flags] [options] --file <file-name>`
标志
subkey inspect-node-key您可以在命令中使用以下可选标志。

旗帜	描述
-h,--help	显示使用信息。
-V,--version	显示版本信息。
选项
您可以在命令中使用以下命令行选项subkey inspect-node-key。

选项	描述
`-n, --network <network>`	指定要使用的网络地址格式。例如，kusama或polkadot。有关支持的网络的完整列表，请参阅在线使用信息。
论据
您必须在命令中指定以下必需参数subkey inspect-node-key。

争论	描述
`--file <file-name>`	指定包含为与节点进行对等通信而生成的密钥的文件。
子键符号
subkey sign通过将消息作为标准输入 ( ) 传递来使用命令对消息进行签名stdin。您可以使用您的秘密种子或秘密短语签署消息。

基本用法
`subkey sign [flags] [options]`
标志
subkey sign您可以在命令中使用以下可选标志。

旗帜	描述
-h,--help	显示使用信息。
--hex	表示您指定为标准输入的消息是十六进制编码的消息。
--password-interactive	使您能够在终端中以交互方式输入访问密钥库的密码。
-V,--version	显示版本信息。
选项
您可以在命令中使用以下命令行选项subkey sign。

选项	描述
`--keystore-path <path>`	指定自定义密钥库路径。
`--keystore-uri <keystore-uri>`	为密钥库服务指定要连接的自定义 URI。
`--message <network>`	指定要签名的消息字符串。
`--password <password>`	指定密钥库使用的密码。此选项使您能够将额外的秘密附加到种子。
`--password-filename <path>`	指定包含密钥库使用的密码的文件的名称。
`--scheme <scheme>`	指定密钥的加密签名方案。有效值为Ecdsa, Ed25519, Sr25519。默认方案是Sr25519.
`--suri <secret-seed>`	指定要用于对消息进行签名的密钥 URI。您可以使用其秘密短语、秘密种子（带有派生路径和密码）来指定密钥。如果为--suri选项指定文件名，则文件内容将用作 URI。如果省略此选项，系统将提示您输入 URI。
例子
以下示例使用该echo命令通过管道将测试消息作为subkey sign命令的输入。要在终端中签署文本消息，您可以运行类似于以下的命令：

echo "test message" | subkey sign --suri 0xc8fa03532fb22ee1f7f6908b9c02b4e72483f0dbd66e4cd456b8f34c6230b849

命令输出显示消息的签名。例如：

f052504de653a5617c46eeb1daa73e2dbbf625b6bf8f16d9d8de6767bc40d91dfbd38c13207f8a03594221c9f68c00a158eb3120311b80ab2da563b82a995b86
要签署十六进制编码的消息，请运行类似于以下内容的命令：
```
subkey sign --hex --message 68656c6c6f2c20776f726c64 --suri 0xc8fa03532fb22ee1f7f6908b9c02b4e72483f0dbd66e4cd456b8f34c6230b849
```
命令输出显示消息的签名。例如：
```
9ae07defc0ddb752651836c25ac643fbdf9d45ba180ec6d09e4423ff6446487a52b609d69c06bd1c3ec09b3d06a43f019bacba12dc5a5697291c5e9faab13288
```
子键虚荣
使用该subkey vanity命令创建一个包含指定字符串模式的地址。此命令不会为自定义地址生成密码短语。

基本用法
`subkey vanity [flags] [options] --pattern <pattern>`
标志
subkey vanity您可以在命令中使用以下可选标志。

旗帜	描述
-h,--help	显示使用信息。
-V,--version	显示版本信息。
选项
您可以在命令中使用以下命令行选项subkey vanity。

选项	描述
`-n, --network <network>`	指定要使用的网络地址格式。例如，kusama或polkadot。有关支持的网络的完整列表，请参阅在线使用信息。
`--output-type <format>`	指定要使用的输出格式。有效值为 Json 和 Text。默认输出格式为文本。
`--scheme <scheme>`	指定密钥的加密签名方案。有效值为Ecdsa, Ed25519, Sr25519。默认方案是Sr25519.
论据
您必须在命令中指定以下必需参数subkey vanity。

争论	描述
`--pattern <pattern>`	指定要包含在生成的地址中的字符串。
例子
根据您指定的模式，该subkey vanity命令可能需要一些时间来搜索密钥库并生成包含自定义字符串的地址。通常，您应该使用尽可能少的字符，--pattern并使用--network选项来指定要使用自定义地址的网络，

要生成包含特定字符串的地址，您可以运行类似于以下的命令：
```
subkey vanity --network kusama --pattern DUNE
```
该命令显示类似于以下内容的输出：
```
Generating key containing pattern 'DUNE'
100000 keys searched; best is 187/237 complete
200000 keys searched; best is 189/237 complete
300000 keys searched; best is 221/237 complete
400000 keys searched; best is 221/237 complete
500000 keys searched; best is 221/237 complete
600000 keys searched; best is 221/237 complete
best: 237 == top: 237
Secret Key URI `0x82737756075d15409053afd19a6b29ae2abeed96a3487d71d2af9b3eff19cbfa` is account:
  Secret seed:       0x82737756075d15409053afd19a6b29ae2abeed96a3487d71d2af9b3eff19cbfa
  Public key (hex):  0xe025cc93383436f61f067ff918ec632d0933c2d81da3bc1fbc27c9d33579bc40
  Account ID:        0xe025cc93383436f61f067ff918ec632d0933c2d81da3bc1fbc27c9d33579bc40
  Public key (SS58): HeDUNE7vd4cYtwHadXBWTgYrsKGQXZ5xFLVbgPVo71X1ccF
  SS58 Address:      HeDUNE7vd4cYtwHadXBWTgYrsKGQXZ5xFLVbgPVo71X1ccF
```
生成密钥对后，SS58 地址和公钥都包含自定义字符串DUNE。

子密钥验证
使用该subkey verify命令使用公钥或密钥验证消息的签名。

基本语法
`subkey verify [flags] [options] <signature> <uri>`
标志
subkey verify您可以在命令中使用以下可选标志。

旗帜	描述
-h,--help	显示使用信息。
--hex	表示您指定为标准输入的消息是十六进制编码的消息。
-V,--version	显示版本信息。
选项
您可以在命令中使用以下命令行选项subkey verify。

选项	描述
`--message <message>`	指定要验证的消息。
`--scheme <scheme>`	指定密钥的加密签名方案。有效值为Ecdsa, Ed25519, Sr25519。默认方案是Sr25519.
论据
您必须在命令中指定以下必需参数subkey verify。

争论	描述
`<signature>`	指定要验证的十六进制编码签名。
`<uri>`	指定要用于验证消息的公钥或私钥 URI。如果为 指定文件名uri，则文件内容将用作 URI。如果省略此选项，系统将提示您输入 URI。
例子
以下示例使用该echo命令通过管道将测试消息作为subkey verify命令的输入。
```
echo "test message" | subkey verify f052504de653a5617c46eeb1daa73e2dbbf625b6bf8f16d9d8de6767bc40d91dfbd38c13207f8a03594221c9f68c00a158eb3120311b80ab2da563b82a995b86 5Gv8YYFu8H1btvmrJy9FjjAWfb99wrhV3uhPFoNEr918utyR
```
如果消息签名通过验证，则回显确认签名，例如：

Signature verifies correctly.
要验证十六进制编码消息的签名，请运行类似于以下内容的命令：
```
subkey verify --hex --message 68656c6c6f2c20776f726c64 4e9d84c9d67241f916272c3f39cd145d847cfeed322b3a4fcba67e1113f8b21440396cb7624113c14af2cd76850fc8445ec538005d7d39ce664e5fb0d926a48f 5Gv8YYFu8H1btvmrJy9FjjAWfb99wrhV3uhPFoNEr918utyR
```
如果消息签名通过验证，则回显确认签名，例如：

Signature verifies correctly.
使用派生键
在 Substrate 中，分层确定性派生密钥根据它们的派生方式分为硬键或软键。例如，硬密钥只能使用父私钥和派生路径派生。父公钥不能用于派生硬密钥。

可以使用父私钥或父公钥和导出路径导出软密钥。因为可以使用父公钥导出软密钥，所以它们可以用于识别父密钥而不暴露父种子。subkey您可以通过在命令中使用不同的语法来派生硬键或软键。然后，您可以使用与派生密钥关联的地址对消息进行签名，其安全性与由根密钥签名的消息相同。

派生一个硬密钥
要派生一个硬子密钥对，//在与其父密钥关联的秘密短语之后添加两个斜杠 ( )、一个派生路径和一个索引。因为您从先前生成的密钥中派生子密钥对和地址，所以您使用该subkey inspect命令。例如：
```
subkey inspect "caution juice atom organ advance problem want pledge someone senior holiday very//derived-hard-key//0"
```
该命令显示类似于以下内容的输出：
```
Secret Key URI `caution juice atom organ advance problem want pledge someone senior holiday very//derived-hard-key//0` is account:
  Secret seed:       0x667fe31c1d1d8f00811aa0163001b5b3055b26f11e82ae17e28668d0e08ced51
  Public key (hex):  0xd61bbc562fc43d43d80a3372a25c52e4aa862bbfdbb4aa1a5ec86f042f787f24
  Account ID:        0xd61bbc562fc43d43d80a3372a25c52e4aa862bbfdbb4aa1a5ec86f042f787f24
  Public key (SS58): 5GuSLtVEbYgYT9Q78CX99RSSPuHjsAyAadwC1GmweDvTvFTZ
  SS58 Address:      5GuSLtVEbYgYT9Q78CX99RSSPuHjsAyAadwC1GmweDvTvFTZ
```
导出软键
要从父私钥派生软子密钥对，请/在与父密钥关联的密码短语之后添加一个斜杠 ( )、派生路径和索引。因为您要从先前生成的密钥中派生新的密钥对和地址，所以您使用该subkey inspect命令。例如：
```
subkey inspect "caution juice atom organ advance problem want pledge someone senior holiday very/derived-soft-key/0"
```
该命令显示类似于以下内容的输出：
```
Secret Key URI `caution juice atom organ advance problem want pledge someone senior holiday very/derived-soft-key/0` is account:
  Secret seed:       n/a
  Public key (hex):  0x8826cc3730441dc4b67ea118997780db878ce7848c1548a9d36624ca39cf7c2c
  Account ID:        0x8826cc3730441dc4b67ea118997780db878ce7848c1548a9d36624ca39cf7c2c
  Public key (SS58): 5F9DtrPk3SaFs6U6S8HxnuJcoQ2jF8Wdt3ygwbbBnbVcsdiC
  SS58 Address:      5F9DtrPk3SaFs6U6S8HxnuJcoQ2jF8Wdt3ygwbbBnbVcsdiC
```
要从父公钥派生软子密钥对，您可以使用公共 SS58 地址而不是密码短语。因为您正在派生软键，所以您使用单斜杠 ( /) 来分隔派生路径和索引字段。例如：
```
subkey inspect "5Gv8YYFu8H1btvmrJy9FjjAWfb99wrhV3uhPFoNEr918utyR/derived-public/0"
```
该命令显示类似于以下内容的输出：
```
Public Key URI `5Gv8YYFu8H1btvmrJy9FjjAWfb99wrhV3uhPFoNEr918utyR/derived-public/0` is account:
  Network ID/version: substrate
  Public key (hex):   0xee3792a82ba43fc503bcbdabd7d090df71496a43928e25f87843f1d9d40e8a14
  Account ID:         0xee3792a82ba43fc503bcbdabd7d090df71496a43928e25f87843f1d9d40e8a14
  Public key (SS58):  5HT3mAg8NnpgeZFUsM9aUYVdS5iq6ZWVUoNcTDiuqVvjkgKR
  SS58 Address:       5HT3mAg8NnpgeZFUsM9aUYVdS5iq6ZWVUoNcTDiuqVvjkgKR
```
如果使用相同的派生路径和索引，则无论使用父私钥还是父公共地址，软子密钥都是相同的。如果您更改派生路径（例如，from derived-soft-keyto）derived-public或索引（从0to ），您将1派生具有不同地址的不同子键。例如：
```
subkey inspect "5Gv8YYFu8H1btvmrJy9FjjAWfb99wrhV3uhPFoNEr918utyR/derived-soft-key/1"
```
该命令显示类似于以下内容的输出：
```
Public Key URI `5Gv8YYFu8H1btvmrJy9FjjAWfb99wrhV3uhPFoNEr918utyR/derived-soft-key/1` is account:
  Network ID/version: substrate
  Public key (hex):   0x688d5a9761bc2705efd3ff0171a535e97de12aab59659177efae453873b18673
  Account ID:         0x688d5a9761bc2705efd3ff0171a535e97de12aab59659177efae453873b18673
  Public key (SS58):  5ERnpynLaQweDhrBQLe3vz8aWYodKYEeJ92xsbnpgG7GhHvo
  SS58 Address:       5ERnpynLaQweDhrBQLe3vz8aWYodKYEeJ92xsbnpgG7GhHvo
```
结合派生路径和密码
请注意，默认情况下，秘密种子不受密码保护。您可以添加密码作为派生密钥的额外保护。但是，从秘密种子派生的密钥对与使用密码时派生的密钥对不同。如果您使用不同的派生路径或添加密码，相同的秘密种子将派生不同的密钥。如果您使用密码来保护您的密钥对，则需要秘密种子短语和密码才能恢复密钥对。

您可以将软键派生为硬键的子级。这样做使您可以使用派生硬密钥的公共地址（带有可选密码）来派生新的公共地址。例如，以下命令派生一个硬键 ( //derived-hard-key) 和一个软键叶 ( /0)：
```
subkey inspect "caution juice atom organ advance problem want pledge someone senior holiday very//derived-hard-key/0"
```
该命令显示类似于以下内容的输出：
```
Secret Key URI `caution juice atom organ advance problem want pledge someone senior holiday very//derived-hard-key/0` is account:
  Secret seed:       n/a
  Public key (hex):  0x525039c770a07a38d8dc066927ad1f0d2b2113f4bc890c4fd39a37d477d0d336
  Account ID:        0x525039c770a07a38d8dc066927ad1f0d2b2113f4bc890c4fd39a37d477d0d336
  Public key (SS58): 5DvdcfXQe2QcWHNZrUR5Bb2AJQ199BiNH5aHefE4kXSRP1VR
  SS58 Address:      5DvdcfXQe2QcWHNZrUR5Bb2AJQ199BiNH5aHefE4kXSRP1VR
```
为了保护派生的硬密钥，您可以将密码添加到密码短语的末尾：
```
subkey inspect "caution juice atom organ advance problem want pledge someone senior holiday very//derived-hard-key/0///pencil laptop kitchen cutter"
```
该命令显示类似于以下内容的输出：
```
Secret Key URI `caution juice atom organ advance problem want pledge someone senior holiday very//derived-hard-key/0///pencil laptop kitchen cutter` is account:
  Secret seed:       n/a
  Public key (hex):  0x2efb74b4a21294f0031129a1d271c7be00171d207052af567fc76de03a81fe52
  Account ID:        0x2efb74b4a21294f0031129a1d271c7be00171d207052af567fc76de03a81fe52
  Public key (SS58): 5D8JkugWWMDmQ4h2yUuBLWwQXaBi2nBdiDmY4DR7hW76QmuW
  SS58 Address:      5D8JkugWWMDmQ4h2yUuBLWwQXaBi2nBdiDmY4DR7hW76QmuW
```
请注意，为派生密钥添加密码会为同一秘密短语生成不同的公钥。您可以使用此受密码保护的硬密钥，使用硬密钥的公共地址派生软密钥。隐藏的种子、硬密钥派生路径和密码。
```
subkey inspect "5D8JkugWWMDmQ4h2yUuBLWwQXaBi2nBdiDmY4DR7hW76QmuW/0"
```
该命令显示类似于以下内容的输出：
```
Public Key URI `5D8JkugWWMDmQ4h2yUuBLWwQXaBi2nBdiDmY4DR7hW76QmuW/0` is account:
  Network ID/version: substrate
  Public key (hex):   0xcaf1f5ec14b507b5c365c6528cc06de74a5615274694a0c895ed4109c0ff0d32
  Public key (SS58):  5GeoQa3nkeNmzZSfgBFuK3BkAggnTHcX3S1j94sffJYYphrP
  Account ID:         0xcaf1f5ec14b507b5c365c6528cc06de74a5615274694a0c895ed4109c0ff0d32
  SS58 Address:       5GeoQa3nkeNmzZSfgBFuK3BkAggnTHcX3S1j94sffJYYphrP
```
使用这种组合硬密钥和软密钥的策略，您可以在不泄露您的密码短语或密码的情况下显示父公共地址和软派生路径，从而保留对所有派生地址的控制权。

预定义的帐户和密钥
Substrate 包含几个可用于在本地开发环境中进行测试的预定义帐户。这些预定义的帐户都是使用单个秘密短语从同一个种子派生的。用于为所有预定义帐户生成密钥的秘密短语由以下词组成：

`bottom drive obey lake curtain smoke basket hold race lonely fit walk`

您可以使用派生路径检查预定义帐户的密钥。例如：
```
subkey inspect //Alice
```
命令
```
Secret Key URI `//Alice` is account:
  Secret seed:       0xe5be9a5092b81bca64be81d212e7f2f9eba183bb7a90954f7b76361f6edb5c0a
  Public key (hex):  0xd43593c715fdd31c61141abd04a99fd6822c8558854ccde39a5684e7a56da27d
  Account ID:        0xd43593c715fdd31c61141abd04a99fd6822c8558854ccde39a5684e7a56da27d
  Public key (SS58): 5GrwvaEF5zXb26Fz9rcQpDWS57CtERHpNehXCPcNoHGKutQY
  SS58 Address:      5GrwvaEF5zXb26Fz9rcQpDWS57CtERHpNehXCPcNoHGKutQY
```
需要注意的是，//Alice和//alice是不同的派生路径，预定义帐户的秘密短语和派生路径实际上是：

`bottom drive obey lake curtain smoke basket hold race lonely fit walk//Alice`
您可以运行以下命令来验证密钥是否匹配：
```
subkey inspect "bottom drive obey lake curtain smoke basket hold race lonely fit walk//Alice"
```
命令输出显示以下内容：
```
Secret Key URI `bottom drive obey lake curtain smoke basket hold race lonely fit walk//Alice` is account:
  Secret seed:       0xe5be9a5092b81bca64be81d212e7f2f9eba183bb7a90954f7b76361f6edb5c0a
  Public key (hex):  0xd43593c715fdd31c61141abd04a99fd6822c8558854ccde39a5684e7a56da27d
  Account ID:        0xd43593c715fdd31c61141abd04a99fd6822c8558854ccde39a5684e7a56da27d
  Public key (SS58): 5GrwvaEF5zXb26Fz9rcQpDWS57CtERHpNehXCPcNoHGKutQY
  SS58 Address:      5GrwvaEF5zXb26Fz9rcQpDWS57CtERHpNehXCPcNoHGKutQY
```
更多资源
子项自述文件。
PolkadotJS 应用程序用户界面。
密码学解释器。