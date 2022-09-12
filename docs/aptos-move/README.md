# [The Move Programming Language](https://github.com/move-language/move/blob/main/language/documentation/book/translations/move-book-zh/src/SUMMARY.md) (Move 编程语言-Aptos Move实操)

Move 是 Diem 项目 专门为区块链开发的一种安全可靠的智能合约编程语言。
您可以在 Diem 开发者网站 找到它的白皮书，也可以在 开发者社区 找到更多内容，了解为什么 Move 更适合区块链。

作为一种刚刚诞生的语言，介绍它的信息不是很多。我把收集到的信息加工、汇总，以书本的形式呈现给读者，便于初学者参考。

- [引言](./introduction.md)
- Getting Started
  - [模块和脚本](./getting-started/modules-and-scripts.md)
  - [move 教程](./getting-started/move-tutorial.md)
- Primitive Types 
  - [整数](./primitive-type/integers.md)
  - [布尔类型](./primitive-type/bool.md)
  - [地址](./primitive-type/addresses.md)
  - [数组](./primitive-type/vector.md)
  - [签名](./primitive-type/signer.md)
  - [引用](./primitive-type/reference.md)
  - [元组和unit](./primitive-type/tuples.md) 
- Basic Concepts
  - [局部变量和作用域](./basic-concepts/variables.md)
  - [等式](./basic-concepts/equality.md)
  - [中止和断言](./basic-concepts/abort-and-assert.md)
  - [条件语句](./basic-concepts/conditionals.md)
  - [循环](./basic-concepts/loops.md)
  - [函数](./basic-concepts/functions.md)
  - [结构体和资源](./basic-concepts/structs-and-resources.md)
  - [常量](./basic-concepts/constants.md)
  - [范型](./basic-concepts/generics.md)
  - [类型能力](./basic-concepts/abilities.md)
  - [导入和别名](./basic-concepts/uses.md)
  - [友元函数](./basic-concepts/friends.md)
  - [程序包](./basic-concepts/packages.md)
  - [单元测试](./basic-concepts/unit-testing.md)
- Global Storage
  - [全局存储结构](./global-storage/global-storage-structure.md)
  - [全局存储操作](./global-storage/global-storage-operators.md)
- Reference
  - [标准库](./reference/standard-library.md)
  - [Move编程规范](./reference/coding-conventions.md)
  
- 可编程的Resource
  - 发送者和签名者
  - 什么是Resource
  - Resource举例
    - 创建和转移
    - 读取和修改
    - 使用和销毁
    - 下一步
- 实例
  - Erc20 token