# 结构体和资源 (Structs and Resources)

结构体是包含类型字段的用户自定义数据结构。结构体可以存储任何非引用类型，包括其他结构体。


如果结构体不能复制也不能删除，我们经常将它们称为资源。在这种情况下，资源必须在函数结束时转让所有权。这个属性使资源特别适合定义全局存储模式或表示重要值(如token)。


默认情况下，结构体是线性和短暂的。它们不能复制，不能丢弃，不能存储在全局存储中。这意味着所有值都必须拥有被转移(线性类型)的所有权，并且值必须在程序执行结束时处理(短暂的)。我们可以通过赋予结构体[能力](./abilities.md)来简化这种行为，允许值被复制或删除，以及存储在全局存储中或定义全局存储的模式。

## 定义结构体 (Defining Structs)


结构体必须在模块内定义：

```move
address 0x2 {
module m {
    struct Foo { x: u64, y: bool }
    struct Bar {}
    struct Baz { foo: Foo, }
    //                   ^ note: it is fine to have a trailing comma
}
}
```

结构体不能递归，因此以下定义无效：

```move=
struct Foo { x: Foo }
//              ^ error! Foo cannot contain Foo
```


如上所述：默认情况下，结构体声明是线性和短暂的。因此，为了允许值用于某些操作(复制、删除、存储在全局存储中或用作存储模式)，结构体可以通过 `has <ability>` 标注来授予它们[能力](./abilities.md)。

```move=
address 0x2 {
    module m {
        struct Foo has copy, drop { x: u64, y: bool }
    }
}
```

有关更多详细信息，请参阅 [注释结构体](./abilities.md#annotating-structs) 部分。

### 命名 (Naming)


结构体必须以大写字母 `A` 到 `Z` 开头。在第一个字母之后，常量名称可以包含下划线 `_`、字母 `a` 到 `z`、字母 `A` 到 `Z` 或数字 `0`到 `9`。

```move
struct Foo {}
struct BAR {}
struct B_a_z_4_2 {}
```

这种从 `A` 到 `Z` 开头的命名限制已经生效，这是为未来的move语言特性留出空间。此限制未来可能会保留或删除。

## 使用结构体 (Using Structs)

### 创建结构体 (Creating Structs)

可以通过指示结构体名称来创建(或“打包”)结构体类型的值。

```move=
address 0x2 {
    module m {
        struct Foo has drop { x: u64, y: bool }
        struct Baz has drop { foo: Foo }

        fun example() {
            let foo = Foo { x: 0, y: false };
            let baz = Baz { foo: foo };
        }
    }
}
```

如果你使用名称与字段相同的本地变量初始化结构字段，你可以使用以下简写：

```move
let baz = Baz { foo: foo };
// is equivalent to
let baz = Baz { foo };
```

这有时被称为“字段名双关语”。

### 通过模式匹配销毁结构体 (Destroying Structs via Pattern Matching)

结构值可以通过绑定或分配模式来销毁。

```move=
address 0x2 {
    module m {
        struct Foo { x: u64, y: bool }
        struct Bar { foo: Foo }
        struct Baz {}

        fun example_destroy_foo() {
            let foo = Foo { x: 3, y: false };
            let Foo { x, y: foo_y } = foo;
            //        ^ shorthand for `x: x`

            // two new bindings
            //   x: u64 = 3
            //   foo_y: bool = false
        }

        fun example_destroy_foo_wildcard() {
            let foo = Foo { x: 3, y: false };
            let Foo { x, y: _ } = foo;
            // only one new binding since y was bound to a wildcard
            //   x: u64 = 3
        }

        fun example_destroy_foo_assignment() {
            let x: u64;
            let y: bool;
            Foo { x, y } = Foo { x: 3, y: false };
            // mutating existing variables x & y
            //   x = 3, y = false
        }

        fun example_foo_ref() {
            let foo = Foo { x: 3, y: false };
            let Foo { x, y } = &foo;
            // two new bindings
            //   x: &u64
            //   y: &bool
        }

        fun example_foo_ref_mut() {
            let foo = Foo { x: 3, y: false };
            let Foo { x, y } = &mut foo;
            // two new bindings
            //   x: &mut u64
            //   y: &mut bool
        }

        fun example_destroy_bar() {
            let bar = Bar { foo: Foo { x: 3, y: false } };
            let Bar { foo: Foo { x, y } } = bar;
            //             ^ nested pattern
            // two new bindings
            //   x: u64 = 3
            //   foo_y: bool = false
        }

        fun example_destroy_baz() {
            let baz = Baz {};
            let Baz {} = baz;
        }
    }
}
```

### 借用结构体和字段 (Borrowing Structs and Fields)

`&` 和 `&mut` 运算符可用于创建对结构或字段的引用。这些例子包括一些可选的类型标注(例如：`: &Foo`)来演示操作的类型。

```move=
let foo = Foo { x: 3, y: true };
let foo_ref: &Foo = &foo;
let y: bool = foo_ref.y;          // 通过引用读取结构体的字段
let x_ref: &u64 = &foo.x;

let x_ref_mut: &mut u64 = &mut foo.x;
*x_ref_mut = 42;            // 通过可引用修改字段
```

可以借用嵌套数据结构的内部字段

```move=
let foo = Foo { x: 3, y: true };
let bar = Bar { foo };

let x_ref = &bar.foo.x;
```

你还可以通过结构体引用来借用字段。

```move=
let foo = Foo { x: 3, y: true };
let foo_ref = &foo;
let x_ref = &foo_ref.x;
// this has the same effect as let x_ref = &foo.x
```

### 读写字段 (Reading and Writing Fields)

如果你需要读取和复制字段的值，则可以解引用借用的字段

```move=
let foo = Foo { x: 3, y: true };
let bar = Bar { foo: copy foo };
let x: u64 = *&foo.x;
let y: bool = *&foo.y;
let foo2: Foo = *&bar.foo;
```

如果该字段可以隐式复制，则可以使用点运算符读取结构的字段，而无需任何借用(只有具有 `copy` 能力的标量值才能隐式复制)。

```move=
let foo = Foo { x: 3, y: true };
let x = foo.x;  // x == 3
let y = foo.y;  // y == true
```

点运算符可以链式访问嵌套字段。

```move=
let baz = Baz { foo: Foo { x: 3, y: true } };
let x = baz.foo.x; // x = 3;
```


但是，对于包含非原始类型(例如向量或其他结构体类型)的字段，这是不允许的

```move=
let foo = Foo { x: 3, y: true };
let bar = Bar { foo };
let foo2: Foo = *&bar.foo;
let foo3: Foo = bar.foo; // error! add an explicit copy with *&
```


这个设计决定背后的原因是复制一个向量或另一个结构可能是一个昂贵的操作。对于程序员来说使用显式语法 `*&` 注意到这个复制很重要，同时引起其他人重视。

除了从字段中读取之外，点语法还可用于修改字段，不管字段是原始类型还是其他结构体

```move=
let foo = Foo { x: 3, y: true };
foo.x = 42;     // foo = Foo { x: 42, y: true }
foo.y = !foo.y; // foo = Foo { x: 42, y: false }
let bar = Bar { foo };            // bar = Bar { foo: Foo { x: 42, y: false } }
bar.foo.x = 52;                   // bar = Bar { foo: Foo { x: 52, y: false } }
bar.foo = Foo { x: 62, y: true }; // bar = Bar { foo: Foo { x: 62, y: true } }
```

点语法对结构的引用也有适用

```move=
let foo = Foo { x: 3, y: true };
let foo_ref = &mut foo;
foo_ref.x = foo_ref.x + 1;
```

## 特权结构体操作 (Privileged Struct Operations)

结构体类型 `T` 上的大多数结构操作只能在其声明的本模块内操作

- 结构类型只能在定义的模块内创建(“打包”)、销毁(“解包”)结构体。
- 结构的字段只能在定义结构的模块内访问。

按照这些规则，如果你想在模块外修改你的结构，你需要为他们提供公共 API。本章的最后包含了这方面的一些例子。

但是， 结构体类型始终对其他模块或脚本可见：

```move=
// m.move
address 0x2 {
    module m {
        struct Foo has drop { x: u64 }

        public fun new_foo(): Foo {
            Foo { x: 42 }
        }
    }
}
```

```move=
// n.move
address 0x2 {
    module n {
        use 0x2::m;

        struct Wrapper has drop {
            foo: m::Foo
        }

        fun f1(foo: m::Foo) {
            let x = foo.x;
            //      ^ error! cannot access fields of `foo` here
        }

        fun f2() {
            let foo_wrapper = Wrapper { foo: m::new_foo() };
        }
    }
}
```

请注意，结构没有可见性修饰符(例如，`public` 或 `private`)。

## 所有权 (Ownership)


正如上面 [Defining Structs](#defining-structs) 中提到的，结构体默认是线性的，并且短暂的。这意味着它们不能被复制或删除。此属性对于现实世界中的资源(例如货币(money))的建模非常有用，因为你不希望货币被复制或流通时丢失。

```move=
address 0x2 {
    module m {
        struct Foo { x: u64 }

        public fun copying_resource() {
            let foo = Foo { x: 100 };
            let foo_copy = copy foo; // error! 'copy'-ing requires the 'copy' ability
            let foo_ref = &foo;
            let another_copy = *foo_ref // error! dereference requires the 'copy' ability
        }

        public fun destroying_resource1() {
            let foo = Foo { x: 100 };

            // error! when the function returns, foo still contains a value.
            // This destruction requires the 'drop' ability
        }

        public fun destroying_resource2(f: &mut Foo) {
            *f = Foo { x: 100 } // error!
                                // destroying the old value via a write requires the 'drop' ability
        }
    }
}
```

要修复第二个示例(`fun dropping_resource`)，您需要手动“解包”资源：

```move=
address 0x2 {
    module m {
        struct Foo { x: u64 }

        public fun destroying_resource1_fixed() {
            let foo = Foo { x: 100 };
            let Foo { x: _ } = foo;
        }
    }
}
```

回想一下，您只能在定义资源的模块中解构资源。这可以用来在系统中强制执行某些不变量，例如货币守恒。

另一方面，如果您的结构不代表有价值的东西，您可以添加功能 `copy` 和 `drop` 来获得一个结构体，这感觉可能会与其他编程语言更相似.

```move=
address 0x2 {
    module m {
        struct Foo has copy, drop { x: u64 }

        public fun run() {
            let foo = Foo { x: 100 };
            let foo_copy = copy foo;
            // ^ this code copies foo, whereas `let x = foo` or
            // `let x = move foo` both move foo

            let x = foo.x;            // x = 100
            let x_copy = foo_copy.x;  // x = 100

            // both foo and foo_copy are implicitly discarded when the function returns
        }
    }
}
```

## 在全局存储中存储资源 (Storing Resources in Global Storage)


只有具有 `key` 能力的结构体才可以直接保存在[全局存储](./global-storage-operators.md)。存储在这些 `key` 中的所有结构体的值必须具有 `store` 能力。请参阅 [能力(abilities)](./chapter_19_abilities] 和[全局存储](./global-storage-operators.md) 章节了解更多详细信息

## Examples


以下是两个简短的示例，说明如何使用结构体来表示有价值的数据(例如 `Coin(代币)`)或更经典的数据(例如：`Point` 和 `Circle`)：

### Example 1: Coin

### Example 1: 代币上

<!-- TODO link to access control for mint -->

```move=
address 0x2 {
    module m {
        // We do not want the Coin to be copied because that would be duplicating this "money",
        // so we do not give the struct the 'copy' ability.
        // Similarly, we do not want programmers to destroy coins, so we do not give the struct the
        // 'drop' ability.
        // However, we *want* users of the modules to be able to store this coin in persistent global
        // storage, so we grant the struct the 'store' ability. This struct will only be inside of
        // other resources inside of global storage, so we do not give the struct the 'key' ability.

        // 我们不希望代币被复制，因为这会复制这笔“钱”，
        // 因此，我们不赋予结构体 `copy` 能力。
        // 同样，我们不希望程序员销毁硬币，所以我们不给结构体 `drop` 能力，
        // 然而，我们*希望*模块的用户能够将此代币存储在持久的全局存储中，所以我们授予结构体 `store` 能力。
        // 此结构体仅位于全局存储内的其他资源中，因此我们不会赋予该结构体 `key` 能力。
        struct Coin has store {
            value: u64,
        }

        public fun mint(value: u64): Coin {
            // You would want to gate this function with some form of access control to prevent anyone using this module from minting an infinite amount of coins
            // 你可能希望通过某种形式的访问控制来关闭此功能，以防止使用此模块的任何人铸造无限数量的货币
            Coin { value }
        }

        public fun withdraw(coin: &mut Coin, amount: u64): Coin {
            assert!(coin.balance >= amount, 1000);
            coin.value = coin.value - amount;
            Coin { value: amount }
        }

        public fun deposit(coin: &mut Coin, other: Coin) {
            let Coin { value } = other;
            coin.value = coin.value + value;
        }

        public fun split(coin: Coin, amount: u64): (Coin, Coin) {
            let other = withdraw(&mut coin, amount);
            (coin, other)
        }

        public fun merge(coin1: Coin, coin2: Coin): Coin {
            deposit(&mut coin1, coin2);
            coin1
        }

        public fun destroy_zero(coin: Coin) {
            let Coin { value } = coin;
            assert!(value == 0, 1001);
        }
    }
}
```

### Example 2: Geometry

### Example 2: 几何上

```move=
address 0x2 {
    module point {
        struct Point has copy, drop, store {
            x: u64,
            y: u64,
        }

        public fun new(x: u64, y: u64): Point {
            Point {
                x, y
            }
        }

        public fun x(p: &Point): u64 {
            p.x
        }

        public fun y(p: &Point): u64 {
            p.y
        }

        fun abs_sub(a: u64, b: u64): u64 {
            if (a < b) {
                b - a
            }
            else {
                a - b
            }
        }

        public fun dist_squared(p1: &Point, p2: &Point): u64 {
            let dx = abs_sub(p1.x, p2.x);
            let dy = abs_sub(p1.y, p2.y);
            dx*dx + dy*dy
        }
    }
}
```

```move=
address 0x2 {
    module circle {
        use 0x2::Point::{Self, Point};

        struct Circle has copy, drop, store {
            center: Point,
            radius: u64,
        }

        public fun new(center: Point, radius: u64): Circle {
            Circle { center, radius }
        }

        public fun overlaps(c1: &Circle, c2: &Circle): bool {
            let d = Point::dist_squared(&c1.center, &c2.center);
            let r1 = c1.radius;
            let r2 = c2.radius;
            d*d <= r1*r1 + 2*r1*r2 + r2*r2
        }
    }
}
```