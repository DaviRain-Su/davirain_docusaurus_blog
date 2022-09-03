# 应用程序开发

大多数在 Substrate 区块链上运行的应用程序都需要某种形式的前端或面向用户的界面——例如浏览器、桌面、移动设备或硬件客户端——使用户或其他程序能够访问和修改区块链存储的数据. 例如，您可能会开发基于浏览器的交互式游戏应用程序或特定于硬件的应用程序来实现硬件钱包。根据您的需要，存在不同的库来构建这些类型的应用程序。本文解释了查询 Substrate 节点并使用它公开的元数据的过程，以帮助您了解在创建前端客户端应用程序和使用特定于客户端的库时如何使用元数据。

元数据系统
Substrate 节点提供一个 RPC 调用，state_getMetadata它返回当前运行时中所有类型的完整描述。客户端应用程序使用元数据与节点交互、解析响应以及格式化发送到节点的消息负载。此元数据包括有关托盘存储项目、事务、事件、错误和常量的信息。当前的元数据版本 (V14) 与其前身有很大不同，因为它包含更丰富的类型信息。如果运行时包含具有自定义类型的托盘，则类型信息将作为返回的元数据的一部分包含在内。Polkadot 使用 V14 元数据，从运行时规范版本 9110开始，块号 7229126和 Kusama 从运行时规范版本 9111开始，在块号 9625129。这对于打算与使用旧元数据版本的运行时交互的开发人员很有用。有关从 V13 到 V14 的迁移指南，请参阅此文档。

当前的元数据模式scale-info在编译节点时使用 crate 在运行时获取托盘的类型信息。

元数据的当前实现需要前端 API 使用SCALE 编解码器库来编码和解码 RPC 有效负载以发送和接收事务。以下步骤总结了元数据是如何生成、公开和用于从运行时发出和接收调用的：

可调用的托盘函数以及类型、参数和文档由运行时公开。
frame-metadatacrate 描述了将提供有关如何与运行时通信的信息的结构。该信息采用由 提供的类型注册表的形式scale-info，以及有关存在哪些托盘（以及注册表中每个托盘的相关类型是什么）等信息。
crate 用于在scale-info运行时注释类型，并使得构建运行时类型的注册表成为可能。此类型信息足够详细，我们可以使用它来找出如何正确 SCALE 编码或解码给定类型的某些值。
中描述的结构frame-metadata填充了来自运行时的信息，然后通过state_getMetadataRPC 调用对其进行 SCALE 编码并使其可用。
自定义 RPC API 使用元数据接口并提供调用运行时的方法。需要一个 SCALE 编解码器库来对与 API 之间的调用和数据进行编码和解码。
每个 Substrate 链都存储他们正在使用的元数据系统的版本号，这使得应用程序知道如何处理某个块公开的元数据很有用。如前所述，最新的元数据版本 (V14) 对链能够生成的元数据进行了重大增强。但是，如果应用程序想要与使用低于 V14 的版本创建的块进行交互怎么办？好吧，它需要设置一个遵循旧元数据系统的前端接口，从而需要识别自定义类型并将其手动包含在前端代码的一部分中。desub如果需要，请了解如何使用该工具来完成此操作。

元数据中捆绑的类型信息使应用程序能够与跨不同链的节点进行通信，每个链都可能公开不同的调用、事件、类型和存储。它还允许库生成与给定 Substrate 节点通信所需的几乎所有代码，从而为库提供了subxt生成特定于目标链的前端接口的可能性。

使用这个系统，可以查询任何运行时的可用运行时调用、类型和参数。
元数据还公开了预期如何解码类型，使外部应用程序更容易检索和处理此信息。

元数据格式
查询state_getMetadataRPC 函数将返回使用frame-metadata和parity-scale-codec库解码的 SCALE 编码字节向量。

RPC返回的十六进制 blobstate_getMetadata取决于元数据版本，但通常具有以下结构：

一个硬编码的幻数，0x6d657461表示纯文本中的“元”。
一个 32 位整数，表示正在使用的元数据格式的版本，例如14或0x0e十六进制。
十六进制编码类型和元数据信息。在 V14 中，这部分将包含类型信息的注册表（由scale-infocrate 生成）。在以前的版本中，这部分包含托盘的数量，后跟每个托盘公开的元数据。
这是使用 V14 元数据系统（使用生成subxt）的运行时解码元数据的精简版本：

[
  1635018093, // the magic number
  {
    "V14": {
      // the metadata version
      "types": {
        // type information
        "types": []
      },
      "pallets": [
        // metadata exposes by pallets
      ],
      "extrinsic": {
        // the format of an extrinsic  and its signed extensions
        "ty": 111,
        "version": 4, // the transaction version used to encode and decode an extrinsic
        "signed_extensions": []
      },
      "ty": 125 // the type ID for the system pallet
    }
  }
]

如上所述，整数1635018093是在纯文本中表示“元”的“幻数”。其余的元数据有两个部分：pallets和extrinsic. 该pallets部分包含有关运行时托盘的信息，而该extrinsic部分描述了运行时正在使用的外部变量的版本。不同的外部版本可能有不同的格式，尤其是在考虑签名交易时。

托盘
这是数组中单个元素的精简示例pallets：

{
  "name": "System", // name of the pallet, the System pallet for example
  "storage": {
    // storage entries
  },
  "calls": [
    // index for this pallet's call types
  ],
  "event": [
    // index for this pallet's event types
  ],
  "constants": [
    // pallet constants
  ],
  "error": [
    // index for this pallet's error types
  ],
  "index": 0 // the index of the pallet in the runtime
}

每个元素都包含它所代表的托盘的名称，以及一个storage对象、calls数组、event数组和error数组。如果callsorevents为空，它们将被表示为null，如果constantsorerrors为空，它们将被表示为一个空数组。

每个项目的类型索引只是u32用于访问该项目的类型信息的整数。例如，calls系统托盘中的类型 ID 是 145。查询类型 ID 将为您提供有关系统托盘可用调用的信息，包括每个调用的文档。对于每个字段，您可以访问以下内容的类型信息和元数据：

存储元数据：为区块链客户端提供查询存储 RPC以获取特定存储项信息所需的信息。
调用元数据：包括有关由#[pallet]宏定义的运行时调用的信息，包括调用名称、参数和文档。
事件元数据：提供#[pallet::event]宏生成的元数据，包括托盘事件的名称、参数和文档
常量元数据提供#[pallet::constant]宏生成的元数据，包括常量的名称、类型和十六进制编码值。
错误元数据：提供#[pallet::error]宏生成的元数据，包括该托盘中每种错误类型的名称和文档。
请注意，使用的 ID 随着时间的推移并不稳定：它们可能会从一个版本跳转到下一个版本，这意味着开发人员应该避免依赖固定类型的 ID 来证明他们的应用程序的未来。

外在
外部元数据由运行时生成，并提供有关如何格式化事务的有用信息。返回的解码后的元数据包含交易版本和签名扩展，如下所示：

      "extrinsic": {
        "ty": 111,
        "version": 4,
        "signed_extensions": [
          {
            "identifier": "CheckSpecVersion",
            "ty": 117,
            "additional_signed": 4
          },
          {
            "identifier": "CheckTxVersion",
            "ty": 118,
            "additional_signed": 4
          },
          {
            "identifier": "CheckGenesis",
            "ty": 119,
            "additional_signed": 9
          },
          {
            "identifier": "CheckMortality",
            "ty": 120,
            "additional_signed": 9
          },
          {
            "identifier": "CheckNonce",
            "ty": 122,
            "additional_signed": 34
          },
          {
            "identifier": "CheckWeight",
            "ty": 123,
            "additional_signed": 34
          },
          {
            "identifier": "ChargeTransactionPayment",
            "ty": 124,
            "additional_signed": 34
          }
        ]
      }

类型系统是复合的，这意味着每个类型 ID 都包含对某个类型或另一个类型 ID 的引用，它可以访问相关的原始类型。例如，我们可以编码的一种类型是BitVec<Order, Store>类型：要正确解码它，我们需要知道使用的类型Order和Store类型，可以使用解码后的 JSON 中的“路径”访问该类型 ID。

RPC API
Substrate 带有以下 API 来与节点交互：

AuthorApi：用于调用完整节点的 API，包括编写外部数据和验证会话密钥。
ChainApi：用于检索块头和确定性信息的 API。
OffchainApi：用于为链下工作人员进行 RPC 调用的 API。
StateApi：用于查询有关链上状态信息的 API，例如运行时版本、存储项和证明。
SystemApi：用于检索有关网络状态的信息的 API，例如已连接的对等点和节点角色。
连接到节点
查询 Substrate 节点可以通过使用基于超文本传输​​协议 (HTTP) 或 WebSocket (WS) 的 JSON-RPC 客户端来完成。WS（在大多数应用程序中使用）的主要优点是单个连接可以重用于与节点之间的许多消息，而典型的 HTTP 连接只允许来自单个消息，然后一次响应客户端. 出于这个原因，如果您想订阅一些可能导致向客户端返回多条消息的 RPC 端点，您必须使用 websocket 连接而不是 HTTP 连接。通过 HTTP 连接通常用于在链下工作人员中获取数据 - 在Offchain 操作中了解更多信息。

连接到 Substrate 节点的另一种（仍然是实验性的）方法是使用Substrate Connect，它允许应用程序生成自己的轻客户端并直接连接到暴露的 JSON-RPC 端点。这些应用程序将依赖浏览器内的本地内存来建立与轻客户端的连接。

开始建造
Parity 维护以下构建在JSON-RPC API之上的库，用于与 Substrate 节点交互：

subxt提供了一种为特定链构建的静态前端创建接口的方法。
Polkadot JS API提供了一个库来为任何 Substrate 构建的区块链构建动态接口。
Substrate Connect提供了一个库和一个浏览器扩展来构建应用程序，这些应用程序直接与为其目标链创建的浏览器内轻客户端连接。作为一个使用 Polkadot JS API 的库，Connect 对于需要连接到多个链的应用程序非常有用，在与同一应用程序的多个链交互时为最终用户提供单一体验。
前端用例
姓名	描述	语	用例
波卡 JS API	用于与 Substrate 链交互的 Javascript 库。	Javascript	需要动态适应节点变化的应用程序，例如区块浏览器或与链无关的接口。
波卡 JS 扩展	用于与使用 Polkadot JS API 构建的浏览器扩展交互的 API。	Javascript	浏览器扩展。
Substrate Connect	一个供开发人员构建应用程序的库，这些应用程序充当他们自己的目标链的轻客户端。它还提供了一个浏览器扩展，旨在从单个应用程序（Web 或桌面浏览器）连接到多个链。	Javascript	任何浏览器应用程序。
subxt	“submit extrinsics”的缩写，subxt是一个库，它生成一个静态类型的 Rust 接口，以根据目标链的元数据与节点的 RPC API 进行交互。	锈	构建较低级别的应用程序，例如非浏览器图形用户界面、特定于链的 CLI 或需要在节点和生成的界面之间进行类型安全通信的面向用户的应用程序，防止用户构建具有错误输入的事务或提交不正确的调用不存在。
txwrapper	用于离线生成 Substrate 交易的 Javascript 库。	Javascript	编写脚本以向节点生成签名交易，这对于测试和解码交易很有用。
下一步去哪里
基板连接
安装前端模板
生成元数据二维码
获取向后兼容的元数据（desub）