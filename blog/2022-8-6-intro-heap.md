---
slug: Heap
title: 堆的概念，以及堆排序算法
authors: davirain
tags: [heap, algorithm]
---

# 堆的概念

> 堆的定义，这里是大根堆的定义
>
> 一棵二叉树满足“堆结构特性”和“堆偏序特性”，则称它为一个堆；
> - 堆结构特性，一棵二叉树要么它事完美的，要么它仅比一个完美二叉树在最底层（深度最大的层）少若干节点，并且最底层的节点从做向右紧挨着依次排序。
> - 堆偏序特性，堆节点中存储的元素满足父节点的值大于所有的子节点的值（左右子节点的值之间的大小关系无要求）。

堆是一个数组，可以被看作是一个近似的完全二叉树，树上的每一个节点对应数组中的一个元素。除了最底层以外，该树是完全充满的，而且是从左向右填充的。

堆有两个属性：
- A.length 给出数组元素的个数，
- A.heap-size 表示有多少个堆元素存储在该数组中。

也就是说，虽然`A[1..A.length]`可能都存在数据，但是只有`A[1..A.heap-size]`中存放的是堆的有效元素，`0 <= A.heap-size <= A.length`.

给定一个节点的下标很容易计算他的父节点，左孩子和右孩子下标。这里给定一个节点的i坐标，

- `Parent(i) => return i / 2`
- `Left_child(i) => 2 * i`
- `Right_child(i) => 2 * i + 1`

在堆排序的实现中，这三个函数通常是以内联函数实现的。

可以利用计算机中的位移特性优化上面的计算
- `Parent(i) => return i >> 1`
- `Left_child(i) => i << 1`
- `Right_child(i) =>  1 << 1 + 1`


```rust 
let i = 1;

assert_eq!(2, i << 1);
assert_eq!(0, i >> 1);
```

二叉堆的分类：
- 最大堆
- 最小堆

这两种堆中，节点的值都要满足堆的性质。但是有一些细节上的差别

在最大堆中，最大堆的性质是指除了根以外的所有节点i都要满足：`A[Parent(i)] >= A[i]`.
解释来说就是，某个节点的值至多与其父节点一样大。因此，堆中的最大元素存放在根节点中；并且，在任何一子树中，该子树所包含的所有节点的值都不大于该子树根节点的值。

最小堆的性质和最大堆相反，`A[Parent(i)] <= A[i]`,在最小堆中的最小元素存放在根节点中。


## 堆的修复

对于堆结构特性的修复，由于堆结构的特性变得非常简单：对于
堆顶的缺失，我们只需要找到一个元素放在堆顶则可以修复这一结构问题；堆底层元素要求从左向右依次排列，所以我们可以‘安全’地取走最底层最右边的元素而保持堆结构不被破坏。所以堆结构的修复就是通过高取最底层最右边的元素，放在堆顶的位置来完成。

修复完结构特性之后，我们所面对的是一棵满足堆结构的二叉树，它的左右树是一个合法的堆，但是它的根节点的值与其两个（边界情况下可能只有一个）子节点的值可能不满足偏序特性的要求。为此，我们只需要做如下处理。

- 将父节点与两个子节点的值做比较。假设左子节点的值是3个值中最大的（对于右子节点的值最大的情况是类似的），则只需要将左子节点和父节点的值交换位置。为此，父节点和右子树均满足偏序特性的要求。
- 但是左子树由于一个新的根节点的引入可能违背偏序的特性。为此我们需要堆左子树递归地进行上述修复的过程。

MAX-HEAPFIFY是用于维护最大堆性质的重要过程。它的输入为一个数组A和一个下标i。在调用MAX-HEAPFIFY的时候，我们假定根节点为LEFT(i)和RIGHT(i)的二叉树都是最大堆，但是这时A[i]有可能小于其孩子，这样就违背了最大堆的性质。MAX-HEAPFIFY通过让A[i]的值在最大堆中的"逐级下降“，从而使得以下标i为根节点的子树重新遵循最大堆的性质。


## 堆的构建

将一组节点维护成一个满足堆结构特性的二叉树是很容易的，所以我们重点讨论如何维护堆的偏序特性。假设有一棵满足堆结构特性的二叉树，但是树中的每个节点所存储的值的大小关系完全是杂乱的，我们，我们需要使树中所有父子节点间均满足堆的偏序特性。基于已经实现堆修复操作，采用递归的思想很容易完成堆的构建。

- 从根部开始堆的构建。注意，一个堆结构的左右子树必然还是堆结构，此时如果堆的左右子树均是一个合法的堆，则最多只是根节点有局部的破坏。而这一局部破坏，用堆修复操作可以处理。由此就可以完成整个堆的构建。
- 对于堆结构的左右子树，我们只需要递归地先将他们构建成一个合法的堆。

## 堆排序算法

初始的时候，堆排序算法利用BUILD-MAX-HEAP将输入数组A[1..n]构造成最大堆，其中n = A.length.因为数组中的最大元素总在根节点A[1]中，通过把它与A[n]进行交换。我们让该元素放到正确的位置。这时候，如果我们从堆中去掉节点n（这一操作通过减少A.heap-size 的值来实现），剩余的节点中，原来根的孩子节点仍然是最大堆，而新的根节点可能会违背最大堆的性质。为了维护最大堆的性质，我们要做的是调用MAX-HEAPIFY(A, 1),从而在A[1..n-1]上构造一个新的最大堆。堆排序算法不断重复这一过程，直到堆堆大小从n-1降到2。

## 堆的具体实现

```
MAX-HEAPFIFY(A, i)
    l = LEFT(i)
    r = RIGHT(i)
    if l <= A.heap-size and A[i] > A[i]
        largest = i
    else largest = i 
    if r <= A.heap-size and A[r] > A[largest]
        largest = r
    if largest != i 
        exchange A[i] with A[largest]
        MAX-HEAPFIFY(A, largest)
```

```
BUILD-MAX-HEAP(A)
    A.heap-size = A.length
    for i = [A.length / 2] downto 1 
        MAX-HEAPFIFY(A, i)
```

```
HEAP-SORT(A)
    BUILD-MAX-HEAP(A)
    for i = A.length downto 2 
        exchange A[1] witch A[i]
        A.heap-size  = A.heap-size - 1
        MAX-HEAPFIFY(A, 1)
```

## 堆调整的非递归实现


### 向上筛选调整算法， 这里是小根堆

当x[1..n-1]是堆事，在x[n]中放置一个任意的元素可能无法产生heap(1, n).我们使用siftup函数来重新获得堆性质。该函数的名字表明了策略。它尽可能将新元素向上筛选，向上筛选是通过交换该节点与父节点来实现的。

siftup函数的循环不变式：

```
loop 
    // invariant: heap(1, n) except perhaps between i and its parent
```
由于开始时heap(1, n-1)为真，因此可以通过赋值语句i=n初始化循环。
循环中必须检查有没有完成任务，若没有则继续。不变式表明，除了节点i和父节点之间的部分可能不具有堆性质外，其他地方都具有堆性质。如果i == 为真，那么节点i没有父节点，从而所有地方都具有堆性质，因此可以终止循环。当节点i有父节点时，可以通过赋值语句p = i / 2使p成为父节点的下标。如果x[p] <= x[i],那么所有地方都具有堆性质，循环可以终止。

另一方面，如果节点i和其父节点之间的循序不对，那么我们交换x[i]和x[p]. 
```
void siftup(n) 
        pre n > 0 && heap(1, n - 1)
        post heap(1, n)
    i = n
    loop 
         // invariant: heap(1, n) except perhaps between i and its parent
        if i == 1
            break
        p = i / 2 
        if x[p] <= x[i] 
            break
        swap(p, i)
        i = p
```

pre和post是函数调用之前的前置条件和后置条件。前置条件为真，那么函数返回之后后置条件也为真。

### 向下调整算法， 这里是小根堆

当x[1..n]是一个堆时，给x[1]分配一个新值得到heap(2, n).然后用函数siftdown使得heap(1, n)为真。该函数将x[1]向下筛选，直到它没有子节点或者小于等于它的子节点。

shiftdown的循环不变式：
```
loop 
    // invariant: heap(1, n) except perhaps between i and its (0, 1, or 2) children
```

首先检查节点i是否具有子节点。如果没有子节点就终止循环。如果节点i具有子节点，那么把变量c设置为较小的那个子节点的下标。最后，或者满足x[i] <= x[c]终止循环。或者通过交换x[i]和x[c]并赋值i = c 继续进行到循环底部。
```
void siftdown(n)
        pre heap(2, n) && n >= 0
        post heap(1, n)
    i = 1
    loop
        // invariant: heap(1, n) except perhaps between i and its (0, 1, or 2) children
        c = 2 * i
        if c > n 
            break
        // c is the left child of i
        if c + 1 <= n
            // c + 1 is the right child i 
            if x[c + 1] < x[c]
                c += 1
        // c is the lesser child of i 
        if x[i] <= x[c]
            break
        swap(c, i)
        i = c
```

## [Rust 算法实现](https://github.com/DaviRain-Su/algorithms-rs/blob/main/src/heap_sort.rs)

```rust
use std::fmt::{Debug, Display};

#[allow(dead_code)]
fn parent(i: usize) -> usize {
    i / 2
}

fn left(i: usize) -> usize {
    ((i + 1) << 1) - 1
}

fn right(i: usize) -> usize {
    (i + 1) << 1
}

/// Heap
#[derive(Debug)]
pub struct Heap<T> {
    /// heap data
    data: Vec<T>,
    /// heap size
    size: usize,
}

impl<T: Clone + PartialOrd + Default + Display + Debug> Heap<T> {
    /// create a heap from vector
    pub fn from_vector(array: &[T]) -> Self {
        Self {
            data: array.into(),
            size: array.len() - 1,
        }
    }

    /// the present heap size
    pub fn len(&self) -> usize {
        self.size
    }

    pub fn is_empty(&self) -> bool {
        self.len() == 0
    }

    /// max heap heapify
    pub fn max_heapify(&mut self, index: usize) {
        // setting largest is index
        let mut largest = index;
        let left = left(index);
        let right = right(index);

        // if left > largest then larget = left
        if left <= self.len() && self.data.get(largest) < self.data.get(left) {
            largest = left;
        }

        // if right > largest then largest = right
        if right <= self.len() && self.data.get(largest) < self.data.get(right) {
            largest = right;
        }

        if largest != index {
            // swap vector index , largest value
            self.data.swap(index, largest);
            // rec call max_heapify
            self.max_heapify(largest);
        }
    }

    /// the min heap heapify
    pub fn min_heapify(&mut self, index: usize) {
        // setting min is index
        let mut min = index;
        let left = left(index);
        let right = right(index);

        // if min > left then min = left
        if left <= self.len() && self.data.get(min) > self.data.get(left) {
            min = left;
        }

        // if min > right then min = right
        if right <= self.len() && self.data.get(min) > self.data.get(right) {
            min = right;
        }

        if min != index {
            // swap vector index, min value
            self.data.swap(index, min);
            // rec call min_heapify
            self.min_heapify(min);
        }
    }

    /// build Max Heap
    pub fn build_max_heap(&mut self) {
        for index in (0..(self.len() / 2)).rev() {
            self.max_heapify(index);
        }
    }

    /// build Min Heap
    pub fn build_min_heap(&mut self) {
        for index in (0..(self.len() / 2)).rev() {
            self.min_heapify(index);
        }
    }

    /// asc sort by Max Heap
    pub fn heap_sort(&mut self) {
        self.build_max_heap();
        for index in (1..self.data.len()).rev() {
            self.data.swap(0, index);
            self.size -= 1;
            self.max_heapify(0);
        }
    }

    /// dec sort by Min Heap
    pub fn heap_sort_by_min_heap(&mut self) {
        self.build_min_heap();
        for index in (1..self.data.len()).rev() {
            self.data.swap(0, index);
            self.size -= 1;
            self.min_heapify(0);
        }
    }
}
```
