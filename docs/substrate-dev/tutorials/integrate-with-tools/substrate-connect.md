# 集成轻客户端节点

本教程演示了如何使用在浏览器中运行的 WebAssembly 轻客户端连接到基于 Substrate 的区块链。在本教程中，您将使用 Substrate Connect 浏览器扩展与区块链进行交互，而无需使用 RPC 服务器。

什么是基板连接
Substrate Connect 是一个基于 WebAssembly 的轻客户端，可以直接在浏览器中运行。Substrate Connect 轻客户端的核心软件组件是smoldot。该软件比完整节点需要更少的资源，因此它可以在资源受限的环境中运行，包括浏览器、移动端点和物联网设备。轻客户端可以通过连接到全节点来同步链中的数据，而不是作为对等点运行并直接连接到区块链来编写或导入块。

安全同步
与允许用户通过信任中间第三方节点与区块链进行交互的软件钱包不同，轻客户端从完整节点下载块头，以便他们可以使用块头中的 Merkle trie 根来验证正在同步的信息是否被同步篡改。Merkle trie 根充当加密证明，证明信息没有被更改，而无需轻客户端信任完整节点。

Substrate Connect 作为浏览器扩展
因为轻客户端不参与区块创作或共识，所以它们不需要在线并与网络保持持续通信。但是，如果您将轻客户端作为浏览器扩展运行，则可以同时运行多个轻客户端并在浏览器会话中保持同步，只要浏览器保持打开状态。

将轻客户端作为浏览器扩展运行还消除了全节点使用传输层安全 (TLS) 和安全套接字层 (SSL) 证书的需要。使用 Substrate Connect，同步在后台发生，而无需通过某些浏览器作为不安全连接阻止的 WebSocket 端口。将 Substrate Connect 作为浏览器扩展运行还可以提供更好的应用程序性能和响应速度更快的用户体验。

Substrate Connect 的应用程序和用户体验
如果您使用 Substrate Connect 构建应用程序，smoldot客户端可以检测用户是否拥有浏览器扩展，如果可用，将自动使用浏览器扩展。如果用户没有安装浏览器扩展，smoldot将在你的 Web 应用程序的上下文中自动创建一个 WebAssembly 轻客户端。尽管将 Substrate Connect 作为浏览器扩展运行是可选的，但该扩展提供了以下优点：

更好的资源使用

多个浏览器选项卡可以共享到同一链的单个连接，而不是每个浏览器选项卡或窗口打开自己的连接。

更好的同步速度

一旦打开浏览器选项卡，浏览器扩展程序就会自动开始与链同步并维护缓存，以便用户打开的每个新选项卡或浏览器窗口与链的连接和同步几乎是即时的。如果没有浏览器扩展，同步链可能需要 10 到 30 秒。

更好的连接性

浏览器扩展可以连接到没有安装 TLS/SSL 证书的节点。

在你开始之前
在开始之前，请验证以下内容：

通过安装Rust 和 Rust 工具链，您已经为 Substrate 开发配置了环境。
您已经下载并编译了 Substrate 节点模板。
教程目标
通过完成本教程，您将实现以下目标：

下载并安装 Substrate Connect 浏览器扩展。
使用 Substrate Connect 浏览器扩展连接到中继链。
了解如何指定要使用的 Substrate Connect 的自定义链规范文件。
从浏览器扩展连接到与自定义链规范关联的平行链。
下载基板连接
由于 Substrate Connect 浏览器扩展提供的优势，本教程的第一步是安装浏览器扩展。

使用 Chrome 或 Firefox Web 浏览器导航到https://substrate.io/developers/substrate-connect/ 。
单击Chrome或Firefox。
单击添加到 Chrome或添加到 Firefox，然后确认您要将扩展程序添加到浏览器。
连接知名连锁店
在 Substrate Connect 轻客户端可以连接到网络之前，您必须有一个 Web 应用程序来指定轻客户端应该连接到的网络、与之通信的节点以及它在创世时必须具有的共识关键状态。此信息在网络的链规范文件中可用。

Substrate Connect 预配置为识别WellKnownChain枚举列表中定义的多个链。这些知名的连锁店是：

Polkadot 标识为polkadot
草间弥生被认定为ksmcc3
洛可可被认定为rococo_v2_2
韦斯特德确定为westend2
要连接到这些链之一：

在您的计算机上打开一个新的终端外壳。
empty-webapp通过运行以下命令克隆模板，创建一个 Web 应用程序以使用 Substrate Connect ：
```shell
git clone https://github.com/bernardoaraujor/empty-webapp
```
empty-webapp通过运行以下命令切换到目录：
```shell
cd empty-webapp
```
通过运行以下命令从 Polkadot-JS RPC 提供程序安装依赖项：
```shell
yarn add @polkadot/rpc-provider
```
通过运行以下命令从 Polkadot-JS API 安装依赖项：
```shell
yarn add @polkadot/api
```
安装这些依赖项后，您可以在示例应用程序中使用它们。

empty-webapp/index.ts在文本编辑器中打开文件。
复制并粘贴以下应用程序代码以创建一个 Substrate Connect 实例，作为提供者使用链规范文件substrate-connect连接到 Polkadot 中继链。polkadot
```js
import {
  ScProvider,
  WellKnownChain,
} from "@polkadot/rpc-provider/substrate-connect";
import { ApiPromise } from "@polkadot/api";

window.onload = () => {
  void (async () => {
    try {
      const provider = new ScProvider(WellKnownChain.polkadot);
      
      await provider.connect();
      const api = await ApiPromise.create({ provider });
      await api.rpc.chain.subscribeNewHeads(
        (lastHeader: { number: unknown; hash: unknown }) => {
          console.log(
            `New block #${lastHeader.number} has hash ${lastHeader.hash}`
          );
        }
      );
    } catch (error) {
      console.error(<Error>error);
    }
  })();
};
```
在 Polkadot-JS API 中，您可以像这样创建一个实例：
```js
// Import
import { ApiPromise, WsProvider } from '@polkadot/api';

// Construct
const wsProvider = new WsProvider('wss://rpc.polkadot.io');
const api = await ApiPromise.create({ provider: wsProvider });
```
对于 Substrate Connect，您将 WebSocket ( WsProvider) 提供程序替换为 Substrate Connect ( ScProvider)，而不是 WebSocket URL 客户端地址，而是指定 Polkadot 网络 ( WellKnownChain.polkadot) 的链规范。

通过运行以下命令安装任何剩余的依赖项：
```shell
yarn
```
通过运行以下命令启动 Web 应用程序：
```shell
yarn dev
```
如果在启动本地服务器时出现编译器错误，则可能缺少当前yarn配置未考虑的依赖项。如果缺少依赖项，您可以通过运行类似于以下的命令来添加包：
```shell
yarn add -D buffer
```
验证浏览器打开 URL http://localhost:3001/。
打开浏览器的浏览器控制台。

您导航到和打开浏览器控制台的方式因您使用的浏览器和操作系统而异。例如，在 Chrome 上，选择更多工具、开发者工具，然后单击控制台。

验证smoldot进程是否已初始化，然后是来自 Polkadot 的传入块的哈希值。

例如，控制台应显示类似于以下内容的日志条目：
```shell
[smoldot] Smoldot v0.6.25
smoldot-light.js:41 [smoldot] Chain initialization complete for polkadot. Name: "Polkadot". Genesis hash: 0x91b1…90c3. State root hash: 0x29d0d972cd27cbc511e9589fcb7a4506d5eb6a9e8df205f00472e5ab354a4e17. Network identity: 12D3KooWRse9u6Z9ukP4C92YCCH2gXziNm8ThRch2owaaFh9H6D1. Chain specification or database starting at: 0xae3e…f81d (#11228238)
...
New block #11322769 has hash 0x464c0199ede92a89920c54c21abc741ea47daca1d62d61d7b9af78062f04c7a3 index.ts:10 
New block #11322770 has hash 0xd66c61e5417249df228798f38535a6dd17b8b268c165e0a6b0e72ba74e954f9d index.ts:10
```
这个简单的 Web 应用程序仅连接到 Polkadot 以检索块哈希。此应用程序的主要目的是演示在不使用网络的集中入口点（例如特定 RPC 节点的 URL）的情况下连接到链。但是，您可以扩展此应用程序以做更多事情，因为在替换为之后WsProvider，您可以简单地使用现有的Polkadot-JS APIScProvider为您的应用程序编写代码。

smoldot按 Control-c停止轻客户端节点。
连接到自定义链规范
连接到自定义链规范或可公开访问的平行链类似于连接到知名链之一。代码的主要区别在于您必须明确标识要使用的 Substrate Connect 的链规范。

本教程说明了如何通过连接到 Statemint 平行链来连接到自定义链规范。Statemint 是一种与 Polkadot 相连的公益平行链，并具有可公开访问的链规范文件。

要连接到此链：

从cumulus 存储库下载自定义链规范文件。
将下载的链规范复制到empty-webapp您在Connect to a well-known chain中创建的目录。
index.ts在文本编辑器中打开文件。
删除当前内容。
复制并粘贴以下应用程序代码。
```js
import { ScProvider, WellKnownChain } from "@polkadot/rpc-provider/substrate-connect";import { ApiPromise } from "@polkadot/api";
import jsonParachainSpec from "./statemint.json";

window.onload = () => {
void (async () => {
  try {
    const relayProvider = new ScProvider(WellKnownChain.polkadot);
    const parachainSpec = JSON.stringify(jsonParachainSpec);
    const provider = new ScProvider(parachainSpec, relayProvider);
    
    await provider.connect();
    const api = await ApiPromise.create({ provider });
    await api.rpc.chain.subscribeNewHeads((lastHeader: { number: unknown; hash: unknown }) => {
      console.log(`New block #${lastHeader.number} has hash ${lastHeader.hash}`);
    });
  } catch (error) {
    console.error(<Error>error);
  }
})();
};
```

如您所见，这段代码有一些重要的区别。

链规范文件被statemint.json导入到jsonParachainSpec对象中。
链规范被转换为 JSON 编码的字符串并存储在parachainSpec变量中，以便可以与 Web 服务器进行交换。
提供ScProvider者是为polkadot中继链创建的，但用作创建和连接到平行链提供者的参数。Substrate Connect 需要此信息来确定平行链与之通信的中继链。
通过运行以下命令启动 Web 应用程序：
```shell
yarn dev
```
验证浏览器打开 URL http://localhost:3001/。
打开浏览器的浏览器控制台。
验证smoldot进程是否已初始化，然后是来自 Polkadot 的传入块的哈希值。

例如，控制台应显示类似于以下内容的日志条目：
```shell
[smoldot] Parachain initialization complete for statemint. Name: "Statemint". Genesis hash: 0x68d5…de2f. State root hash: 0xc1ef26b567de07159e4ecd415fbbb0340c56a09c4d72c82516d0f3bc2b782c80. Network identity: 12D3KooWArq3iZHdK2jtRZSJzJkkWrKm17JTa9kjwjZkq9Htx5xR. Relay chain: polkadot (id: 1000 smoldot-light.js:41 
[smoldot] Smoldot v0.6.25. Current memory usage: 140 MiB. Average download: 35.4 kiB/s. Average upload: 423 B/s.
New block #1785421 has hash 0x88885ed331f94b4324c5f2eae8413cd36170808ef904b0ec0867646fa53770f7 index.ts:13 
New block #1785422 has hash 0x2ad4d96e061a681e27403694f1d870bb0c4e5c77b5be232a18c7a2e0b7fb2555 index.ts:13 
```
高级应用程序开发
之所以使用本教程中的示例，@polkadot/rpc-provider/substrate-connect是因为此提供程序可以直接创建使用Polkadot-JS API与链交互的应用程序。对于不依赖 Polkadot-JS API 的更高级的应用程序开发，您可以安装并使用@substrate-connect. 例如，如果您正在构建自己的应用程序库或编程接口，您应该通过运行以下命令来安装 Substrate Connect 依赖项：
```shell
yarn add @substrate/connect
```
