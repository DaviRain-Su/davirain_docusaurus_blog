# 托盘联轴器

耦合一词通常用于描述两个软件模块相互依赖的程度。例如，在面向对象编程中，紧耦合和松耦合用于描述对象类之间的关系：

紧耦合是当两组类相互依赖时。
松耦合是指一个类使用另一个类公开的接口。
在 Substrate 中，紧托盘耦合和松托盘耦合用于描述一个托盘如何调用另一个托盘中的功能。两种技术都以不同的方式实现相同的目标，每种技术都有一定的取舍。

紧密耦合的托盘
因为紧耦合使得使用托盘的灵活性和可扩展性降低，所以只有在托盘需要继承其耦合的对应物作为一个整体而不是特定类型或方法时，您才会使用紧托盘耦合。

在编写需要紧密耦合的托盘时，您明确指定托盘的Config特征与要耦合的托盘的特征绑定Config。

所有 FRAME 托盘都与托盘紧密耦合frame_system。以下示例说明了如何使用Config称为与托盘some_pallet紧密耦合的frame_system托盘的特征：
```
pub trait Config: frame_system::Config + some_pallet::Config {
    // --snip--
}
```
这与在面向对象编程中使用类继承非常相似。提供此特征绑定意味着此托盘只能安装在也安装了some_pallet托盘的运行时中。与 类似frame_system，此示例中的紧密耦合需要您some_pallet在耦合托盘的Cargo.toml文件中指定。

紧耦合有几个缺点开发人员应该考虑：

可维护性：一个托盘的变化通常会导致需要修改另一个托盘。
可重用性：必须包含两个模块才能使用一个模块，这使得集成紧密耦合的托盘变得更加困难。
松散耦合的托盘
在松散托盘耦合中，您可以指定某些类型需要绑定的特征和功能接口。

在运行时配置期间，此类类型的实际实现发生在托盘之外——通常作为文件中定义的代码/runtime/src/lib.rs。通过松散耦合，您可以使用另一个实现了特征的托盘中的类型和接口，或者您可以声明一个全新的结构，实现这些特征，并在运行时实现托盘时分配它。

例如，假设您有一个托盘可以访问帐户余额并转移到另一个帐户。这个pallet定义了一个Currencytrait，它有一个抽象的函数接口，后面会实现实际的传输逻辑。
```
pub trait Currency<AccountId> {
    // -- snip --
    fn transfer(
        source: &AccountId,
        dest: &AccountId,
        value: Self::Balance,
        // don't worry about the last parameter for now
        existence_requirement: ExistenceRequirement,
    ) -> DispatchResult;
}
```
在第二个托盘中，您定义MyCurrency关联类型并通过 trait 绑定它， `Currency<Self::AccountId>`以便您可以通过调用来使用余额转移逻辑`T::MyCurrency::transfer(...)`。
```
pub trait Config: frame_system::Config {
    type MyCurrency: Currency<Self::AccountId>;
}

impl<T: Config> Pallet<T> {
    pub fn my_function() {
        T::MyCurrency::transfer(&buyer, &seller, price, ExistenceRequirement::KeepAlive)?;
    }
}
```
请注意，此时，您尚未指定如何实现Currency::transfer()逻辑。只是同意它将在某个地方实施。

现在，您可以使用运行时配置runtime/src/lib.rs——来实现托盘并将类型指定为Balances.
```
impl my_pallet::Config for Runtime {
    type MyCurrency = Balances;
}
```
Balances类型在construct_runtime!宏中指定为pallet_balances实现Currencytrait的一部分。

通过运行时提供的实现，您可以`Currency<AccountId>`在松散耦合的托盘中使用 trait。

许多 FRAME 托盘以这种方式与此货币特征耦合。

选择托盘耦合策略
一般来说，松耦合比紧耦合提供了更大的灵活性，从系统设计的角度来看，它被认为是一种更好的实践。它保证了代码的更好的可维护性、可重用性和可扩展性。但是，紧密耦合对于不太复杂或方法和类型重叠多于差异的托盘很有用。

在 FRAME 中，有两个托盘紧密耦合到pallet_treasury：

赏金托盘
倾卸托盘
作为一般规则，托盘越复杂，就越不希望将其紧密耦合。这唤起了计算机科学中一个称为内聚力的概念，内聚力是一种用于检查软件系统整体质量的指标。

下一步去哪里
操作方法：使用松散耦合
操作方法：使用紧耦合