# 标准库（Standard Library）

Move标准库公开了实现以下功能的接口:
* [向量的基本操作](#向量).
* [Option类型与基本操作](#option).
* [终止码的常见错误编码接口](#errors).
* [32位精确定点数字](#fixed_point32).

## 向量（vector）

`向量`模块在原生类型[`向量`](../primitive-type/vector.md)上定义了许多操作。该模块以命名地址`Std`发布，并由许多原生函数以及在Move中定义的函数组成。此模块的API如下所示:

### 函数（Functions）

---------------------------------------------------------------------------

创建一个空的[`向量`](../primitive-type/vector.md)。
`Element`类型可以是`资源`或`可复制`类型。

```move
    native public fun empty<Element>(): vector<Element>;
```

---------------------------------------------------------------------------


创建一个长度为`1`的vector，并且包含传入的`element`。

```move
    public fun singleton<Element>(e: Element): vector<Element>;
```

---------------------------------------------------------------------------

销毁(释放)向量`v`。如果`v`非空操作将终止。
*注意*:空的限制是由于`Element`可以是资源类型，而销毁非空的向量会违反[资源保护机制](../basic-concepts/structs-and-resources.md)。

```move
    native public fun destroy_empty<Element>(v: vector<Element>);
```

---------------------------------------------------------------------------

获取向量`v`的第`i`个元素的[不可变引用](../primitive-type/reference.md)。如果索引`i`超出了向量`v`的范围，操作将会终止。

```move
    native public fun borrow<Element>(v: &vector<Element>, i: u64): &Element;
```

---------------------------------------------------------------------------

获取向量`v`的第`i`个元素的[可变引用](../primitive-type/reference.md)。如果索引`i`超出了向量`v`的范围，操作将会终止。

```move
    native public fun borrow_mut<Element>(v: &mut vector<Element>, i: u64): &mut Element;
```

---------------------------------------------------------------------------

清空并销毁`other`动态数组，并将`other`向量中的每个元素按顺序添加到`lhs`动态数组。

```move
    public fun append<Element>(lhs: &mut vector<Element>, other: vector<Element>);
```

---------------------------------------------------------------------------


将类型为`Element`的元素`e`添加到向量`v`的末尾。可能触发底层向量内存的大小调整。

```move
    native public fun push_back<Element>(v: &mut vector<Element>, e: Element);
```

---------------------------------------------------------------------------

从向量`v`的末尾取出一个元素并返回。如果`v`为空将终止操作。

```move
    native public fun pop_back<Element>(v: &mut vector<Element>): Element;
```

---------------------------------------------------------------------------


移除向量`v`中索引`i`处的元素，并返回之前存储在`v`中的`i`处的值。所有下标大于`i`的元素将向前移动1个位置。如果`i`超出了`v`的范围，操作将会终止。

```move
    public fun remove<Element>(v: &mut vector<Element>, i: u64): Element;
```

---------------------------------------------------------------------------

将向量`v`的第`i`个元素与最后一个元素交换，然后将这个元素从向量的后面取出，并返回之前存储在索引`i`处的所有元素的值。
这个操作时间复杂度是O(1)，但是不保持向量容器中元素的顺序。
如果索引`i`超出了向量`v`的边界，则操作终止。

```move
    public fun swap_remove<Element>(v: &mut vector<Element>, i: u64): Element;
```

---------------------------------------------------------------------------

交换向量`v`中下标为第`i`和第`j`的元素。如果`i`或`j`中的任何一个超出了`v`的范围，则操作将终止。

```move
    native public fun swap<Element>(v: &mut vector<Element>, i: u64, j: u64);
```

---------------------------------------------------------------------------

将向量v中的元素顺序颠倒。

```move
    public fun reverse<Element>(v: &mut vector<Element>);
```

---------------------------------------------------------------------------

返回`v`中第一个与`e`相等的元素的索引。如果找到这样的元素，则返回`(true, index)`，否则返回`(false, 0)`。

```move
    public fun index_of<Element>(v: &vector<Element>, e: &Element): (bool, u64);
```

---------------------------------------------------------------------------

如果向量`v`中存在等于`e`的元素，则返回true, 否则返回false。

```move
    public fun contains<Element>(v: &vector<Element>, e: &Element): bool;
```

---------------------------------------------------------------------------

返回`向量`的长度。

```move
    native public fun length<Element>(v: &vector<Element>): u64;
```

---------------------------------------------------------------------------

如果向量`v`中没有元素，则返回true, 否则返回false。

```move
    public fun is_empty<Element>(v: &vector<Element>): bool;
```

---------------------------------------------------------------------------

## 选项（option）

`option`模块定义了一个泛型option类型`Option<T>`，它表示类型为`T`的值可能存在，也可能不存在。它发布在命名地址`Std`下。

Move option类型在内部表示为一个单例向量，可能包含`资源`或`可复制`类型的值。如果你熟悉其他语言中的option类型，Move `Option`的行为与那些类似，但有几个显著的例外，因为option可以包含一个类型为`资源`的值。
特别地，某些操作如`get_with_default`和`destroy_with_default`要求元素类型`T`为`可复制`类型。

`option`模块的API如下所示:

### 类型（Types）


一个值的泛型类型的抽象，可能存在，也可能不存在。它可以包含`资源`或`可复制`类型的值。

```move
    struct Option<T>;
```

### 函数（Functions）

创建一个可以包含`Element`类型值的空`Option`。

```move
    public fun none<Element>(): Option<Element>;
```

---------------------------------------------------------------------------


创建一个非空的`Option`类型，包含类型为`Element`的值`e`。

```move
    public fun some<Element>(e: T): Option<Element>;
```

---------------------------------------------------------------------------

返回`opt_elem`内部值的不可变引用,如果`opt_elem`不包含值，则将终止操作。

```move
    public fun borrow<Element>(opt_elem: &Option<Element>): &Element;
```

---------------------------------------------------------------------------


如果`opt_elem`中包含值，则返回该值的引用。如果`opt_elem`不包含值，将返回传入的`default_ref`引用。不会终止操作。

```move
    public fun borrow_with_default<Element>(opt_elem: &Option<Element>, default_ref: &Element): &Element;
```

---------------------------------------------------------------------------


返回`opt_elem`内部值的可变引用。如果`opt_elem`不包含值，则操作将终止。

```move
    public fun borrow_mut<Element>(opt_elem: &mut Option<Element>): &mut Element;
```

---------------------------------------------------------------------------

通过删除并返回存储在`opt_elem`中的值，将包含值的`opt_elem`转换为空option类型。
如果`opt_elem`不包含值，则将终止。

```move
    public fun extract<Element>(opt_elem: &mut Option<Element>): Element;
```

---------------------------------------------------------------------------

如果`opt_elem`中包含值，则返回该值。
如果`opt_elem`不包含值，将返回传入的`default`值。`default`类型必须是`可复制`类型，这样该函数才能被调用。

```move
    public fun get_with_default<Element: copyable>(opt_elem: &Option<Element>, default: Element): Element;
```

---------------------------------------------------------------------------

将空option类型`opt_elem`转换为包含值`e`的option类。
如果`opt_elem`已经包含值，则操作将终止。

```move
    public fun fill<Element>(opt_elem: &mut Option<Element>, e: Element);
```

---------------------------------------------------------------------------

将`opt_elem`当前包含的值与`new_elem`交换，并返回先前包含的值。如果`opt_elem`不包含值，则操作将终止。

```move
    public fun swap<Element>(opt_elem: &mut Option<Element>, e: Element): Element;
```

---------------------------------------------------------------------------

如果`opt_elem`包含一个等于`e_ref`的值，则返回`true`。否则，将返回`false`。

```move
    public fun contains<Element>(opt_elem: &Option<Element>, e_ref: &Element): bool;
```

---------------------------------------------------------------------------


如果`opt_elem`不包含值，则返回`true`。

```move
    public fun is_none<Element>(opt_elem: &Option<Element>): bool;
```

---------------------------------------------------------------------------

如果`opt_elem`包含值，则返回`true`。

```move
    public fun is_some<Element>(opt_elem: &Option<Element>): bool;
```

---------------------------------------------------------------------------

解包`opt_elem`并返回它所包含的值。
如果`opt_elem`不包含值，则操作将终止。

```move
    public fun destroy_some<Element>(opt_elem: Option<Element>): Element;
```

---------------------------------------------------------------------------

销毁传入的`opt_elem`。如果`opt_elem`包含值，它将被返回，否则将返回传入的`default`值。

```move
    public fun destroy_with_default<Element: copyable>(opt_elem: Option<Element>, default: Element): Element;
```

---------------------------------------------------------------------------

销毁传入的`opt_elem`，`opt_elem`必须为空且不包含值。如果`opt_elem`包含一个值，则会终止操作。

```move
    public fun destroy_none<Element>(opt_elem: Option<Element>);
```

## 错误（errors）


回想一下，Move中的每个终止代码都表示为无符号64位整数。`errors`模块定义了一个通用接口，可用于"标记"每个终止代码，以便它们既可以表示错误**类别**，也可以表示错误**原因**。


错误类别在`errors`模块中声明为常量，并且对该模块来说是全局唯一的。另一方面，错误原因是特定于模块的错误代码，可以提供关于特定错误条件的更详细的信息(甚至可能是一个特定的_reason_)。每个错误代码的类别和原因的这种表示是通过将终止代码分成两部分来完成的。

终止代码的较低8位保存*错误类别*。终止代码的其余56位包含*错误原因*。
原因应该是相对于引发错误的模块的唯一数字，并且可以用来获取关于当前错误的更多信息。它应该主要用于诊断目的，因为如果模块更新，错误原因可能会随着时间的推移而变化。

| 类型 | 原因 |
|----------|--------|
| 8 bits   | 56 bits|

由于错误类别是全局稳定的，所以它们提供了稳定的API，通常应该由客户端用来确定它们可能向用户提供的消息(而原因则用于诊断目的)。在`errors`模块中有一些公共函数，用于创建每个错误类别的带有特定`原因`号的终止代码(表示为`u64`)。

### 常量（Constants）

系统处于不允许操作的状态。

```move
    const INVALID_STATE: u8 = 1;
```

---------------------------------------------------------------------------

执行操作需要一个特定的帐户地址，但遇到的地址与预期的不同。

```move
    const REQUIRES_ADDRESS: u8 = 2;
```

---------------------------------------------------------------------------

帐户没有此操作的预期角色。用于基于角色访问控制(RBAC)错误。

```move
    const REQUIRES_ROLE: u8 = 3;
```

---------------------------------------------------------------------------

帐户没有所需的能力。用于RBAC错误。

```move
    const REQUIRES_CAPABILITY: u8 = 4;
```

---------------------------------------------------------------------------

地址下不存在期望的资源。

```move
    const NOT_PUBLISHED: u8 = 5;
```

---------------------------------------------------------------------------

试图在已发布资源的地址发布资源。

```move
    const ALREADY_PUBLISHED: u8 = 6;
```

---------------------------------------------------------------------------

为操作提供的参数无效。

```move
    const INVALID_ARGUMENT: u8 = 7;
```

---------------------------------------------------------------------------

超过了一个值的限制。

```move
    const LIMIT_EXCEEDED: u8 = 8;
```

---------------------------------------------------------------------------

发生了内部错误(bug)。

```move
    const INTERNAL: u8 = 10;
```

---------------------------------------------------------------------------

扩展自定义错误类别。

```move
    const CUSTOM: u8 = 255;
```

---------------------------------------------------------------------------

### 函数（Functions）

在遇到无效(全局)状态的情况下应使用。构造一个具有指定的`reason`和类别`INVALID_STATE`的终止代码。如果`reason`不适合56位，将会终止操作。

```move
    public fun invalid_state(reason: u64): u64;
```

---------------------------------------------------------------------------

当账户地址与特定地址不匹配时应使用。构造一个具有指定的`reason`和类别`REQUIRES_ADDRESS`的终止代码。如果`reason`不适合56位，将会终止操作。

```move
    public fun requires_address(reason: u64): u64;
```

---------------------------------------------------------------------------

在使用RBAC时，角色与所需角色不匹配时应使用。构造一个具有指定的`reason`和类别`REQUIRES_ROLE`的终止代码。如果`reason`不适合56位，将会终止操作。

```move
    public fun requires_role(reason: u64): u64;
```

---------------------------------------------------------------------------

在使用RBAC时，帐户没有必要的能力时应使用。构造一个具有指定的`reason`和类别`REQUIRES_CAPABILITY`的终止代码。如果`reason`不适合56位，将会终止操作。

```move
    public fun requires_capability(reason: u64): u64;
```

---------------------------------------------------------------------------

在需要资源的地方不存在资源时应使用。构造一个具有指定的`reason`和类别`NOT_PUBLISHED`的终止代码。如果`reason`不适合56位，将会终止操作。

```move
    public fun not_published(reason: u64): u64;
```

---------------------------------------------------------------------------

要发布资源的地方已经存在资源时使用。构造一个具有指定的`reason`和类别`ALREADY_PUBLISHED`的终止代码。如果`reason`不适合56位，将会终止操作。

```move
    public fun already_published(reason: u64): u64;
```

---------------------------------------------------------------------------

当向函数/操作传递无效参数时使用。构造一个具有指定的`reason`和类别`INVALID_ARGUMENT`的终止代码。如果`reason`不适合56位，将会终止操作。

```move
    public fun invalid_argument(reason: u64): u64;
```

---------------------------------------------------------------------------

当达到特定值的限制时应使用，例如，0减去1。构造一个具有指定的`reason`和类别`LIMIT_EXCEEDED`的终止代码。如果`reason`不适合56位，将会终止操作。

```move
    public fun limit_exceeded(reason: u64): u64;
```

---------------------------------------------------------------------------

在遇到内部错误或错误时使用。构造一个具有指定的`reason`和类别`INTERNAL`的终止代码。如果`reason`不适合56位，将会终止操作。

```move
    public fun internal(reason: u64): u64;
```

---------------------------------------------------------------------------

用于扩展，大多数情况下不应使用。构造一个具有指定的`reason`和类别`CUSTOM`的终止代码。如果`reason`不适合56位，将会终止操作。

```move
    public fun custom(reason: u64): u64;
```

---------------------------------------------------------------------------

## 32位精确定点数字（fixed_point32）

`fixed_point32`模块定义了一个具有32个整数位和32个小数位的定点数值类型。在内部，它被表示为一个`u64`整数，包装在一个结构中，形成一个唯一的`fixed_point32`类型。由于数字表示是二进制的，一些十进制值可能不能完全表示，但它在小数点之前和之后都提供了9位以上的十进制精度(总共18位)。为了进行比较，双精度浮点数的精度小于16位十进制数字，因此在使用浮点数将这些值转换为十进制时应该小心。

### 类型（Types）

表示具有32个小数位的定点数字。

```move
    struct FixedPoint32;
```

### 函数（Functions）

当u64整数乘以定点数，截断乘积的任何小数部分。如果乘积溢出，该操作将终止。

```move
    public fun multiply_u64(val: u64, multiplier: FixedPoint32): u64;
```

---------------------------------------------------------------------------

当u64整数除以定点数，截断商的任何小数部分。如果除数为零或商溢出，该操作将终止。

```move
    public fun divide_u64(val: u64, divisor: FixedPoint32): u64;
```

---------------------------------------------------------------------------

根据分子和分母指定的有理数创建定点值。如果`fixed_point32::create_from_raw_value`函数可用，应优先使用。如果分母为零，该操作将终止。如果分子非零且比值不在$2^{-32}\ldots2^{32}-1$范围内，该操作将终止。指定小数时，请注意四舍五入错误：如果要对小数点后$N$位进行四舍五入，则可以用$10^N$做分母，这样就能避免精确度丢失问题，例如，0.0125将四舍五入到0.012而不是0.013。

```move
    public fun create_from_rational(numerator: u64, denominator: u64): FixedPoint32;
```

---------------------------------------------------------------------------

通过`u64`原始值创建一个定点值。

```move
    public fun create_from_raw_value(value: u64): FixedPoint32;
```

---------------------------------------------------------------------------

如果`num`的十进制值等于0，则返回`true`。

```move
    public fun is_zero(num: FixedPoint32): bool;
```

---------------------------------------------------------------------------

获取`u64`原始值的方法。其他不太常见的操作，例如添加或减去`FixedPoint32`值，可以直接使用原始值来完成。

```move
    public fun get_raw_value(num: FixedPoint32): u64;
```

---------------------------------------------------------------------------