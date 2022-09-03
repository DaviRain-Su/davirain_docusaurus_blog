# 类型编码 (SCALE)

Substrate 使用轻量级且高效的编码和解码程序来优化通过网络发送和接收数据的方式。用于序列化和反序列化数据的程序称为 SCALE 编解码器，其中 SCALE 是s imple concatenated a ggregate l ittle- ndian的首字母缩写。

SCALE 编解码器是运行时和外部节点之间通信的关键组件。

它专为在资源受限的执行环境（如 Substrate WebAssembly 运行时）中对数据进行高性能、无副本编码和解码而设计。

SCALE 编解码器不以任何方式自描述。它假定解码上下文具有关于编码数据的所有类型知识。Parity 维护的前端库使用parity-scale-codeccrate（它是 SCALE 编解码器的 Rust 实现）来编码和解码 RPC 和运行时之间的交互。

SCALE 编解码器有利于 Substrate 和区块链系统，因为：

相对于像serde这样的通用序列化框架来说，它是轻量级的，后者添加了重要的样板文件，可能会增加二进制文件的大小。
它不使用 Rustlibstd使其与no_std编译为 Wasm 的环境兼容，例如 Substrate 运行时。
它的构建是为了在 Rust 中为新类型派生编解码器逻辑提供强大的支持：#[derive(Encode, Decode)].
重要的是定义 Substrate 中使用的编码方案，而不是重用现有的 Rust 编解码器库，因为该编解码器需要在希望支持 Substrate 区块链之间互操作性的其他平台和语言上重新实现。

下表显示了 Parity SCALE 编解码器的 Rust 实现如何编码不同的类型。

不同类型的 SCALE 编解码器示例

类型	描述	示例 SCALE 解码值	SCALE 编码值
固定宽度整数	基本整数使用固定宽度的小端 (LE) 格式进行编码。	signed 8-bit integer 69	0x45
unsigned 16-bit integer 42	0x2a00
unsigned 32-bit integer 16777215	0xffffff00
紧凑/通用整数1	“紧凑”或通用整数编码足以编码大整数（最多 2**536），并且在编码大多数值方面比固定宽度版本更有效。（尽管对于单字节值，固定宽度的整数永远不会更糟。）	unsigned integer 0	0x00
unsigned integer 1	0x04
unsigned integer 42	0xa8
unsigned integer 69	0x1501
unsigned integer 65535	0xfeff0300
BigInt(100000000000000)	0x0b00407a10f35a
布尔值	布尔值使用单个字节的最低有效位进行编码。	false	0x00
true	0x01
结果2	结果是常用的枚举，指示某些操作是成功还是不成功。	Ok(42)	0x002a
Err(false)	0x0100
选项	特定类型的一个或零个值。	Some	0x01后跟编码值
None	0x00后跟编码值
向量（列表、系列、集合）	对相同类型值的集合进行编码，前缀为项目数量的紧凑编码，然后依次连接每个项目的编码。	无符号 16 位整数向量：[4, 8, 15, 16, 23, 42]	0x18040008000f00100017002a00
字符串	`Vec<u8>`字符串是包含有效 UTF8 序列的字节向量 ( )。		
元组	一系列固定大小的值，每个值都有可能不同但预先确定的固定类型。这只是每个编码值的串联。	紧凑无符号整数和布尔值的元组：(3, false)	0x0c00
结构	对于结构，值是命名的，但这与编码无关（名称被忽略 - 只有顺序很重要）。所有容器连续存储元素。元素的顺序不固定，依赖于容器，解码时不能依赖。这隐含地意味着将某些字节数组解码为强制执行顺序的指定结构，然后对其重新编码可能会导致与被解码的原始字节数组不同的字节数组。	始终具有按升序排列的字节元素的`SortedVecAsc<u8>`结构：`SortedVecAsc::from([3, 5, 2, 8])	[3, 2, 5, 8]`
枚举（标记联合）	固定数量的变体，每个变体互斥并潜在地暗示进一步的值或一系列值。编码为第一个字节，标识该值所在的变体的索引。任何进一步的字节都用于对变体所暗示的任何数据进行编码。因此，支持的变体不超过 256 个。	Int(42)和Bool(true)在哪里enum IntOrBool { Int(u8), Bool(bool),}	0x002a和0x0101
SCALE 编解码器已以其他语言实现，包括：
```
Python：polkascan/py-scale-codec
戈朗：itering/scale.go
C：MatthewDarnell/cScale
C++：soramitsu/scale-codec-cpp
JavaScript：polkadot-js/api
打字稿：scale-ts
汇编脚本：LimeChain/as-scale-codec
哈斯克尔：airalab/hs-web3
爪哇：emeraldpay/polkaj
红宝石：itering/scale.rb
```
紧凑/通用整数用表示模式的两个最低有效位进行编码：

0b00：单字节模式；高六位是值的 LE 编码（仅对 0-63 的值有效）。
0b01: 两字节模式：高六位，后面的字节是值的 LE 编码（仅对 values 有效64-(2**14-1)）。
0b10: 四字节模式：高六位和后面三个字节是 value 的 LE 编码（仅对 values 有效(2**14)-(2**30-1)）。
0b11：大整数模式：高六位是后面的字节数加四。该值包含在后面的字节中，LE 编码。最后（最高有效）字节必须非零。仅对值有效(2**30)-(2**536-1)。
↩
结果编码为：

0x00如果操作成功，后跟编码值。
0x01如果操作不成功，后跟编码错误