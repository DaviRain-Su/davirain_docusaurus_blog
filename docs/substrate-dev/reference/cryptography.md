# 密码学

本文档提供了 Substrate 中使用的密码学的概念性概述。

散列算法
Substrate 中使用哈希函数将任意大小的数据映射到固定大小的值。

Substrate 提供了两种开箱即用的哈希算法，但可以支持任何实现Hashertrait的哈希算法。

xxHash
xxHash 是一种快速的非加密哈希函数，工作速度接近 RAM 限制。因为 xxHash 在密码学上不是安全的，所以有可能通过修改输入来合理控制散列算法的输出。这可以允许用户通过创建密钥冲突、哈希冲突和不平衡的存储尝试来攻击该算法。

xxHash 用于外部各方无法操纵散列函数输入的地方。例如，它用于生成运行时存储值的密钥，其输入由运行时开发人员控制。

Substrate 使用twox-hashRust 中的实现。

布莱克2
Blake2是一个加密哈希函数。它被认为非常快，也用于Zcash。

Substrate 使用blake2Rust 中的实现。

公钥密码学
Substrate 中使用公钥加密来提供强大的身份验证系统。

Substrate 提供了多种不同的加密方案，并且是通用的，因此它可以支持任何实现Pairtrait的东西。

ECDSA
Substrate 使用secp256k1曲线提供ECDSA签名方案。这与用于保护比特币和以太坊的加密算法相同。

Ed25519
Ed25519是使用Curve25519的 EdDSA 签名方案。它在多个设计和实施级别上经过精心设计，可在不影响安全性的情况下实现非常高的速度。

SR25519
SR25519基于与Ed25519相同的基础曲线。但是，它使用 Schnorr 签名而不是 EdDSA 方案。

Schnorr 签名为ECDSA /EdDSA 方案带来了一些明显的特性：

对于分层确定性密钥派生来说更好。
它允许通过签名聚合实现本机多重签名。
它通常更耐误用。
在 ECDSA 上使用 Schnorr 签名时做出的一项牺牲是两者都需要 64 个字节，但只有 ECDSA 签名才能传达其公钥。

下一步去哪里
帐户、地址和密钥。
Web 3 研究。
Polkadot 声明模块，了解如何在 Substrate 运行时验证以太坊签名。
Hash实现新散列算法的特征。
Pair用于实施新密码方案的特征