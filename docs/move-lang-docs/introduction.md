# 引言 (Introduction)

欢迎来到Move的世界，Move是一种安全、沙盒式和形式化验证的下一代编程语言，
它的第一个用例是 Diem 区块链(当时名字叫Libra, 脸书团队开发的项目, 译者注), Move 为其实现提供了基础。 
Move 允许开发人员编写灵活管理和转移数字资产的程序，同时提供安全保护，防止对那些链上资产的攻击。
不仅如此，Move 也可用于区块链世界之外的开发场景。

Move 的诞生从[Rust](https://www.rust-lang.org/)中吸取了灵感，Move也是因为使用具有移动(move)语义的资源类型作为数字资产(例如货币)的显式表示而得名。

## Move是为谁准备的？(Who is Move for?)

Move语言被设计和创建为安全、可验证， 同时兼顾灵活性的编程语言。
Move的第一个应用场景是用于Diem区块链的开发。
现在，Move语言仍在不断发展中。Move 还有成为其他区块链，甚至非区块链用例开发语言的潜质。

鉴于在 Diem 支付网络 (DPN) [启动](https://diem.com/white-paper/#whats-next)时将不支持自定义 Move 模块(custom Move modules)，我们的目标是早期的 Move 开发人员。

早期的 Move 开发人员应该是具有一定编程经验的程序员，他们愿意了解编程语言核心，并探索它的用法。

## 爱好者 (Hobbyists)

作为(Move语言)爱好者角色，首先需要明白在Diem支付网络上创建自定义模块(custom modules)是不可能的，
其次，你还要对探索这门语言的复杂性保持兴趣。你将了解基本语法、可用的标准库，并编写可以用的Move CLI执行的示例代码。
如果可能，你甚至可以去尝试体验Move虚拟机如何执行你自己编写的代码。

## 核心贡献者 (Core Contributor)

核心贡献者指那些超越爱好者并想在核心编程语言方面保持领先，还直接为 Move 做出[贡献](https://www.diem.com/en-us/cla-sign/)的人。
无论是提交语言改进，甚至未来添加 Diem 支付网络上可用的核心模块等，核心贡献者都将深入了解Move。

## Move不适用于哪些人？(Who Move is currently not targeting)

目前，Move 并不适用那些希望在在 Diem 支付网络上创建自定义模块和合约的开发人员。
我们也不针对期望在测试语言时就能获得完美开发体验的初学开发者。

## 从哪里开始？(Where Do I Start?)

你可以从了解[模块和脚本(modules and scripts)](./getting-started/modules-and-scripts.md)开始，然后跟随[Move教程(Move Tutorial)](./getting-started/move-tutorial.md)进行练习。