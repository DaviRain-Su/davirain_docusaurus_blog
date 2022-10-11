# 全局存储 - 操作（Global Storage - Operators）


Move程序可以使用下面五种指令创建、删除、更新全局存储中的[资源](../basic-concepts/structs-and-resources.md)：

| 操作符                                 | 描述                                                   | 出错                           |
---------------------------------------- |------------------------------------------------------ |---------------------------------- |
|`move_to<T>(&signer,T)`                 | 在 `signer.address` 下发布 `T`                         | 如果 `signer.address` 已经存在 `T` |
|`move_from<T>(address): T`              | 从 `address` 下删除 `T` 并返回                         | 如果 `address` 下没有 `T`           |
|`borrow_global_mut<T>(address): &mut T` | 返回 `address` 下 `T` 的可变引用 mutable reference      | 如果 `address` 下没有 `T`          |
|`borrow_global<T>(address): &T`         | 返回 `address` 下 `T` 的不可变引用 immutable reference  | 如果 `address` 下没有 `T`          |
|`exists<T>(address): bool`              | 返回 `address` 下的 `T`                                | 永远不会                           |
|


每个指令的参数 `T` 都具有 [`key` 能力](../basic-concepts/abilities.md)。然而，类型 `T` *必须在当前模块*中声明。这确保资源只能通过当前模块暴露的 API 来操作。指令在存储 `T` 类型资源的同时，使用 [`address`](../primitive-type/addresses.md) 或 [`&signer`](../primitive-type/signer.md) 表示账户地址。


## 资源参考（References to resources）


`borrow_global` 或 `borrow_global_mut` 指令返回的全局资源引用在大多数情况下类似本地存储的引用：它们可以通过[引用操作](../primitive-type/reference.md)进行拓展、读和写，也可以作为其它函数的参数。然而本地引用和全局引用有个重要差异：**函数不能返回指向全局存储的引用**。例如，下面两个函数编译会失败：


```move
struct R has key { f: u64 }
// 不能编译 // will not compile
fun ret_direct_resource_ref_bad(a: address): &R {
    borrow_global<R>(a) // error!
}
// 也不能编译 // also will not compile
fun ret_resource_field_ref_bad(a: address): &u64 {
    &borrow_global<R>(a).f // error!
}
```


Move必须强制这种限制来保证全局存储引用不会出现空引用。对于感兴趣的读者，[此节](#全局资源引用安全)包含了更多的细节。


## 使用泛型的全局存储操作（Global storage operators with generics）

全局存储操作可以与实例化和未实例化的泛型资源参数使用：


```move
struct Container<T> has key { t: T }

/// 发布用于存储调用者提供 T 类型对象的 Container /// Publish a Container storing a type T of the caller's choosing
fun publish_generic_container<T>(account: &signer, t: T) {
    move_to<Container<T>>(account, Container { t })
}

/// 发布存储 u64 类型的 Container /// Publish a container storing a u64
fun publish_instantiated_generic_container(account: &signer, t: u64) {
    move_to<Container<u64>>(account, Container { t })
}
```


能够通过参数类型在运行时中索引全局存储的能力是 Move 的强大特性，该特性称之为*存储多态性*。关于此特性更多的设计模式，请参考[Move泛型](../basic-concepts/generics.md)这节。

## 示例： `Counter` （Example: `Counter`）

下面简单的 `Counter` 模块使用五种全局存储操作。该模块暴露的API允许：

- 任何人可以在他们的账户下发布 `Counter` 资源。
- 任何人可以检查任何地址下是否包含 `Counter`。
- 任何人可以读或增加任何地址下的 `Counter` 值。
- 存储 `Counter` 资源的账号可以将其重置为 0。
- 存储 `Counter` 资源的账号可以删除该对象。

```move
address 0x42 {
module counter {
    use std::signer;

    /// 包含整数的资源 /// Resource that wraps an integer counter
    struct Counter has key { i: u64 }

    /// 给定账户下发布带有 `i` 值的 `Counter` 资源 /// Publish a `Counter` resource with value `i` under the given `account`
    public fun publish(account: &signer, i: u64) {
      // “打包"（创建）Counter 资源。这是需要授权的操作，只能在声明 `Counter` 资源的此模块内执行。 // "Pack" (create) a Counter resource. This is a privileged operation that can only be done inside the module that declares the `Counter` resource
      move_to(account, Counter { i })
    }

    /// 读取 `addr` 地址下 `Counter` 内的值 /// Read the value in the `Counter` resource stored at `addr`
    public fun get_count(addr: address): u64 acquires Counter {
        borrow_global<Counter>(addr).i
    }

    /// 增加 `addr` 地址下 `Counter` 内的值 /// Increment the value of `addr`'s `Counter` resource
    public fun increment(addr: address) acquires Counter {
        let c_ref = &mut borrow_global_mut<Counter>(addr).i;
        *c_ref = *c_ref + 1
    }

    /// 将 `account` 的 `Counter` 重置为 0 /// Reset the value of `account`'s `Counter` to 0
    public fun reset(account: &signer) acquires Counter {
        let c_ref = &mut borrow_global_mut<Counter>(signer::address_of(account)).i;
        *c_ref = 0
    }

    /// 删除 `account` 的 `Counter` 资源并返回其内值 /// Delete the `Counter` resource under `account` and return its value
    public fun delete(account: &signer): u64 acquires Counter {
        // 删除 Counter 资源 // remove the Counter resource
        let c = move_from<Counter>(signer::address_of(account));
        // 将 `Counter` 资源“拆”为字段。这是需要授权的操作，只能在声明 `Counter` 资源的此模块内执行。 // "Unpack" the `Counter` resource into its fields. This is a privileged operation that can only be done inside the module that declares the `Counter` resource
        let Counter { i } = c;
        i
    }

    /// 如果 `addr` 下包含 `Counter` 资源，则返回 `true`。 /// Return `true` if `addr` contains a `Counter` resource
    public fun exists(addr: address): bool {
        exists<Counter>(addr)
    }
}
}
```

## `acquires` 函数标注（Annotating functions with `acquires`）

在 `counter` 例子中，可以注意到 `get_count`、`increment`、`reset` 和 `delete` 方法都使用 `acquires Counter` 进行标注。函数 `m::f` 在且仅在下述情况必须使用 `acquires T` 进行标注：

- `m::f` 的主体包含 `move_from<T>`、`borrow_global_mut<T>` 或 `borrow_global<T>` 指令调用
- `m::f` 的主体调用了同模块内被 `acquires` 注解的 `m::g` 的函数


例如，下面 `Counter` 内的函数需要使用 `acquires` 标注：

```move
// 由于 `increment` 使用了 `acquires` 标注，所以函数需要 `acquires` // Needs `acquires` because `increment` is annotated with `acquires`
fun call_increment(addr: address): u64 acquires Counter {
    counter::increment(addr)
}
```


然而，在 `Counter` *外面*的函数则不需要进行标注：


```move
address 0x43 {
module m {
   use 0x42::counter;

   // 可以，仅在函数声明在同一模块内时需要标注 // Ok. Only need annotation when resource acquired by callee is declared in the same module
   fun call_increment(addr: address): u64 {
       counter::increment(addr)
   }
}
}
```

如果函数需要多个资源，`acquires` 则需要多个参数：

```move=
address 0x42 {
module two_resources {
    struct R1 has key { f: u64 }
    struct R2 has key { g: u64 }

    fun double_acquires(a: address): u64 acquires R1, R2 {
        borrow_global<R1>(a).f + borrow_global<R2>.g
    }
}
}
```

`acquires` 标注不会将泛型类型参数纳入声明中：


```move=
address 0x42 {
module m {
    struct R<T> has key { t: T }

    // 效果为 `acquires R` 而不是 `acquires R<T>` // `acquires R`, not `acquires R<T>`
    fun acquire_generic_resource<T: store>(a: addr) acquires R {
        let _ = borrow_global<R<T>>(a);
    }

    // 效果为 `acquires R` 而不是 `acquiresR<u64>` // `acquires R`, not `acquires R<u64>
    fun acquire_instantiated_generic_resource(a: addr) acquires R {
        let _ = borrow_global<R<u64>>(a);
    }
}
}
```

最后：不允许使用不必要的 `acquires`。在 `Counter` 内添加下述方法将会导致编译错误：


```move
// 下面代码不会编译，因为函数体没有使用全局存储指令也没调用使用 `acquires` 注解的函数 // This code will not compile because the body of the function does not use a global storage instruction or invoke a function with `acquires`
fun redundant_acquires_bad() acquires Counter {}
```

关于 `acquires` 更多信息，参见 [Move 函数](../basic-concepts/functions.md)。

## 全局资源引用安全（Reference Safety For Global Resources）


Move 禁止返回全局引用并且需要使用 `acquires` 标注来防止空引用。这使 Move 保证了所有[引用](../primitive-type/reference.md)类型的静态引用安全性（例如，没有空引用、不会解引用 `null` 或 `nil` 对象）。


这个例子展示了 Move 类型系统如何通过使用 `acquires` 来防止空引用：

```move=
address 0x42 {
module dangling {
    struct T has key { f: u64 }

    fun borrow_then_remove_bad(a: address) acquires T {
        let t_ref: &mut T = borrow_global_mut<T>(a);
        let t = remove_t(a); // 类型系统不允许 t_ref 这种空引用 // type system complains here
        // t_ref now dangling!
        let uh_oh = *&t_ref.f
    }

    fun remove_t(a: address): T acquires T {
        move_from<T>(a)
    }

}
}
```


代码中第六行获取了 `a` 地址在全局存储中 `T` 类型资源的引用。`remove_t` 调用删除了该值，使 `t_ref` 变成空引用。

幸运的是，由于类型系统拒绝编译程序导致这种情况不会发生。`remove_t` 方法的 `acquires` 标注让类型系统知道第七行是危险的，不需要再分析 `remove_t` 的函数体。

禁止返回全局引用的限制同时也防止了类似却更隐晦的问题：

```move=
address 0x42 {
module m1 {
    struct T has key {}

    public fun ret_t_ref(a: address): &T acquires T {
        borrow_global<T>(a) // 报错 类型系统在这不能继续编译 // error! type system complains here
    }

    public fun remove_t(a: address) acquires T {
        let T {} = move_from<T>(a);
    }
}

module m2 {
    fun borrow_then_remove_bad(a: address) {
        let t_ref = m1::ret_t_ref(a);
        let t = m1::remove_t(a); // t_ref 为空引用 // t_ref now dangling!
    }
}
}
```


第十六行获取了全局资源 `m1::T` 类型的引用，然后第十七行删除了同一资源，这使 `t_ref` 变成空引用。在这个例子中，`acquires` 标注没有帮助到我们，因为 `borrow_then_remove_bad` 函数在声明了 `T` 类型（回顾 `acquires` 标注只用在声明此类型的模块内）的 `m1` 模块外。然而禁止返回全局引用的规则使第六行避免了这个问题。

允许返回全局引用而尽可能不牺牲引用安全的高级类型系统是可行的，我们将会在 Move 未来的迭代过程中考虑此事。我们选择目前的设计方式是因为它很好的平衡了语言表现力、复杂的标注和复杂的类型系统三者的关系。