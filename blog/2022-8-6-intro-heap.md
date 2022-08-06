---
slug: Heap
title: Intro Heap
authors: davirain
tags: [heap, algorithm]
---

# 堆， 堆的概念

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