# 能力 (abilities)

能力是 Move 语言中的一种类型特性，用于控制对给定类型的值允许哪些操作。 该系统对值的“线性”类型行为以及值如何在全局存储中使用提供细粒度控制。这是通过对某些字节码指令的进行访问控制来实现的，因此对于要与字节码指令一起使用的值，它必须具有所需的能力(如果需要的话，并非每条指令都由能力控制)

## 四种能力 (The Four Abilities)

这四种能力分别是：

* [`copy`](#copy) 复制
    * 允许此类型的值被复制

* [`drop`](#drop) 丢弃
    * 允许此类型的值被弹出/丢弃

* [`store`](#store) 存储
    * 允许此类型的值存在于全局存储的某个结构体中

* [`key`](#key) 键值
    * 允许此类型作为全局存储中的键(具有 `key` 能力的类型才能保存到全局存储中)


### `copy`

`copy` 能力允许具有此能力的类型的值被复制。 它限制了从本地变量通过 [`copy`](./variables.md#.move-and-copy)能力复制值以及通过 [`dereference *e`](./chapter_8_references.html#reading-and-writing-through-references)复制值这两种情况之外的复制操作。

如果一个值具有 `copy` 能力，那么这个值内部的所有值都有 `copy` 能力。

### `drop`

`drop` 能力允许类型的值被丢弃。丢弃的意思程序执行后值会被有效的销毁而不必被转移。因此，这个能力限制在多个位置忽略使用值的可能性，包括：
* 未被使用的局部变量或者参数
* 未被使用的 [`sequence` via `;`](./variables.md#expression-blocks)中的值
* 覆盖[赋值(assignments)](./chapter_10_variables.html#assignments)变量中的值
* [写入(writing) `*e1 = e2`](https://move-language.github.io/move/references.html#reading-and-writing-through-references) 时通过引用覆盖的值。

如果一个值具有 `drop` 能力，那么这个值内部的所有值都有 `drop` 能力。

### `store`

`store` 能力允许具有这种能力的类型的值位于[全局存储](./global-storage-operators.html)中的结构体(资源)内, *但不一定是* 全局存储中的顶级资源。这是唯一不直接限制操作的能力。相反，当(`store`)与 `key` 一起使用时，它对全局存储中的可行性进行把关。。

如果一个值具有 `store` 能力，那么这个值内部的所有值都有 `store` 能力。

### `key`

`key` 能力允许此类型作为[全局存储](./global-storage-operators.html)中的键。它会限制所有[全局存储](./global-storage-operators.html)中的操作，因此一个类型如果与 `move_to`, `borrow_global`, `move_from` 等一起使用，那么这个类型必须具备 `key` 能力。请注意，这些操作仍然必须在定义 `key` 类型的模块中使用(从某种意义上说，这些操作是此模块的私有操作)。

如果有一个值有 `key` 能力，那么这个值包含的所有字段值也都具有 `store` 能力，`key` 能力是唯一一个具有非对称的能力。

## Builtin Types (内置类型)


几乎所有内置的基本类型具都有 `copy`，`drop`，以及 `store` 能力，`singer` 除外，它只有 `drop` 能力(原文是 `store` 有误，译者注)

* `bool`, `u8`, `u64`, `u128`, `address` 都具有 `copy`, `drop`, 以及 `store` 能力。
* `signer` 具有 `drop` 能力。 不能被复制以及不能被存放在全局存储中
* `vector<T>` 可能具有 `copy`，`drop`，以及`store` 能力，这依赖于 `T` 具有的能力。 查看 [条件能力与泛型类型](#conditional-abilities-and-generic-types)获取详情
* 不可变引用 `&` 和可变引用 `&mut` 都具有 `copy` 和 `drop` 能力。
    * 这是指复制和删除引用本身，而不是它们所引用的内容。
    * 引用不能出现在全局存储中，因此它们没有 `store` 能力。

所有基本类型都没有 `key`，这意味着它们都不能直接用于[全局存储操作](./global-storage-operators.html)。

## Annotating Structs (标注结构体)

要声明一个 `struct` 具有某个能力，它在结构体名称之后, 在字段之前用 `has <ability>` 声明。例如：

```move
struct Ignorable has drop { f: u64 }
struct Pair has copy, drop, store { x: u64, y: u64 }
```


在这个例子中：`Ignorable` 具有 `drop` 能力。 `Pair` 具有 `copy`、`drop` 和 `store` 能力。

所有这些能力对这些访问操作都有强有力的保证。只有具有该能力，才能对值执行对应的操作；即使该值深层嵌套在其他集合中！


因此：在声明结构体的能力时，对字段提出了某些要求。所有字段都必须满足这些约束。这些规则是必要的，以便结构体满足上述功能的可达性规则。如果一个结构被声明为具有某能力：

* `copy`， 所有的字段必须具有 `copy` 能力。
* `drop`，所有的字段必须具有 `drop` 能力。
* `store`，所有的字段必须具有 `store` 能力。
* `key`，所有的字段必须具有 `store` 能力。`key` 是目前唯一不需要包含自身的能力。

例如:

```move
// A struct without any abilities
struct NoAbilities {}

struct WantsCopy has copy {
    f: NoAbilities, // ERROR 'NoAbilities' does not have 'copy'
}
```

类似的：

```move
// A struct without any abilities
struct NoAbilities {}

struct MyResource has key {
    f: NoAbilities, // Error 'NoAbilities' does not have 'store'
}
```

## Conditional Abilities and Generic Types (条件能力与泛型类型)

在泛型类型上标注能力时，并非该类型的所有实例都保证具有该能力。考虑这个结构体声明：

```move
struct Cup<T> has copy, drop, store, key { item: T }
```

如果 `Cup` 可以容纳任何类型，可能会很有帮助，不管它的能力如何。类型系统可以 *看到* 类型参数，因此，如果它 *发现* 一个类型参数违反了对该能力的保证，它应该能够从 `Cup` 中删除能力。

这种行为一开始可能听起来有点令人困惑，但如果我们考虑一下集合类型，它可能会更容易理解。我们可以认为内置类型 `Vector` 具有以下类型声明：

```move
vector<T> has copy, drop, store;
```

我们希望 `vector` 适用于任何类型。我们不希望针对不同的能力使用不同的 `vector` 类型。那么我们想要的规则是什么？与上面的字段规则完全相同。因此，仅当可以复制内部元素时，复制`vector` 值才是安全的。仅当可以忽略/丢弃内部元素时，忽略 `vector` 值才是安全的。而且，仅当内部元素可以在全局存储中时，将向量放入全局存储中才是安全的。

为了具有这种额外的表现力，一个类型可能不具备它声明的所有能力，具体取决于该类型的实例化；相反，一个类型的能力取决于它的声明 **和** 它的类型参数。对于任何类型，类型参数都被悲观地假定为在结构体内部使用，因此只有在类型参数满足上述字段要求时才授予这些能力。以上面的 `Cup` 为例：

* `Cup` 拥有 `copy` 能力 仅当 `T` 拥有 `copy` 能力时。
* `Cup` 拥有 `drop` 能力 仅当 `T` 拥有 `drop` 能力时。
* `Cup` 拥有 `store` 能力 仅当 `T` 拥有 `store` 能力时。
* `Cup` 拥有 `key` 能力 仅当 `T` 拥有 `store` 能力时。


以下是每个能力的条件系统的示例：

### Example: conditional `copy`

```move
struct NoAbilities {}
struct S has copy, drop { f: bool }
struct Cup<T> has copy, drop, store { item: T }

fun example(c_x: Cup<u64>, c_s: Cup<S>) {
    // Valid, 'Cup<u64>' has 'copy' because 'u64' has 'copy'
    let c_x2 = copy c_x;
    // Valid, 'Cup<S>' has 'copy' because 'S' has 'copy'
    let c_s2 = copy c_s;
}

fun invalid(c_account: Cup<signer>, c_n: Cup<NoAbilities>) {
    // Invalid, 'Cup<signer>' does not have 'copy'.
    // Even though 'Cup' was declared with copy, the instance does not have 'copy'
    // because 'signer' does not have 'copy'
    let c_account2 = copy c_account;
    // Invalid, 'Cup<NoAbilities>' does not have 'copy'
    // because 'NoAbilities' does not have 'copy'
    let c_n2 = copy c_n;
}
```

### Example: conditional `drop`

```move
struct NoAbilities {}
struct S has copy, drop { f: bool }
struct Cup<T> has copy, drop, store { item: T }

fun unused() {
    Cup<bool> { item: true }; // Valid, 'Cup<bool>' has 'drop'
    Cup<S> { item: S { f: false }}; // Valid, 'Cup<S>' has 'drop'
}

fun left_in_local(c_account: Cup<signer>): u64 {
    let c_b = Cup<bool> { item: true };
    let c_s = Cup<S> { item: S { f: false }};
    // Valid return: 'c_account', 'c_b', and 'c_s' have values
    // but 'Cup<signer>', 'Cup<bool>', and 'Cup<S>' have 'drop'
    0
}

fun invalid_unused() {
    // Invalid, Cannot ignore 'Cup<NoAbilities>' because it does not have 'drop'.
    // Even though 'Cup' was declared with 'drop', the instance does not have 'drop'
    // because 'NoAbilities' does not have 'drop'
    Cup<NoAbilities> { item: NoAbilities {}};
}

fun invalid_left_in_local(): u64 {
    let n = Cup<NoAbilities> { item: NoAbilities {}};
    // Invalid return: 'c_n' has a value
    // and 'Cup<NoAbilities>' does not have 'drop'
    0
}
```

### Example: conditional `store`

```move
struct Cup<T> has copy, drop, store { item: T }

// 'MyInnerResource' is declared with 'store' so all fields need 'store'
struct MyInnerResource has store {
    yes: Cup<u64>, // Valid, 'Cup<u64>' has 'store'
    // no: Cup<signer>, Invalid, 'Cup<signer>' does not have 'store'
}

// 'MyResource' is declared with 'key' so all fields need 'store'
struct MyResource has key {
    yes: Cup<u64>, // Valid, 'Cup<u64>' has 'store'
    inner: Cup<MyInnerResource>, // Valid, 'Cup<MyInnerResource>' has 'store'
    // no: Cup<signer>, Invalid, 'Cup<signer>' does not have 'store'
}
```

### Example: conditional `key`

```move
struct NoAbilities {}
struct MyResource<T> has key { f: T }

fun valid(account: &signer) acquires MyResource {
    let addr = signer::address_of(account);
     // Valid, 'MyResource<u64>' has 'key'
    let has_resource = exists<MyResource<u64>>(addr);
    if (!has_resource) {
         // Valid, 'MyResource<u64>' has 'key'
        move_to(account, MyResource<u64> { f: 0 })
    };
    // Valid, 'MyResource<u64>' has 'key'
    let r = borrow_global_mut<MyResource<u64>>(addr)
    r.f = r.f + 1;
}

fun invalid(account: &signer) {
   // Invalid, 'MyResource<NoAbilities>' does not have 'key'
   let has_it = exists<MyResource<NoAbilities>>(addr);
   // Invalid, 'MyResource<NoAbilities>' does not have 'key'
   let NoAbilities {} = move_from<NoAbilities>(addr);
   // Invalid, 'MyResource<NoAbilities>' does not have 'key'
   move_to(account, NoAbilities {});
   // Invalid, 'MyResource<NoAbilities>' does not have 'key'
   borrow_global<NoAbilities>(addr);
}
```