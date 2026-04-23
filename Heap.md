---
title: Heap
date: 2026-04-23
tags:
  - algorithm
  - heap
  - exam-review
aliases:
  - Max Heap
  - Priority Queue Heap
source_files:
  - /Users/ryansuc/Desktop/G1-1/algo/slide/2026.03.13 Time Complexity.pdf
---

# Heap

> [!summary]
> 考試方向提到 heap sort 與 priority queue，所以這份是 [[01 時間複雜度、遞迴式與 Heap]] 的速記版。

## Max-Heapify

- 看左右 child 誰最大。
- 若最大 child 比目前節點大，就交換。
- 對交換後的 child index 繼續做 `MAX-HEAPIFY`。
- 停止條件是左右 child 都沒有比目前節點大。

> [!warning] 先決條件
> `MAX-HEAPIFY(A, i)` 預設 `i` 的左右子樹本來就已經是 max-heap；它只修復 root `i` 可能違反 heap property 的問題。

時間複雜度：

$$
O(\log n)
$$

因為最多沿著樹高一路往下。

## Build-Max-Heap

- 從 `floor(n/2)` 跑到 `1`。
- Leaf 不用跑，因為 leaf 本身就是 heap。
- 從下面 build 上去，才能保證每次呼叫 `MAX-HEAPIFY` 時左右子樹都已經是 max-heap。

時間複雜度：

$$
\sum_{h=0}^{\lfloor\log n\rfloor}\left\lceil\frac{n}{2^{h+1}}\right\rceil O(h)
=O\left(n\sum_{h=0}^{\infty}\frac{h}{2^h}\right)=O(n)
$$

> [!tip]
> 直覺是：底層節點很多但高度小，上層節點高度大但數量少，所以總成本不是 `O(n log n)`，而是 `O(n)`。

## Heap Sort

1. `BUILD-MAX-HEAP(A)`。
2. 重複把 `A[1]` 最大值換到 heap 尾端。
3. `heap-size -= 1`。
4. 對 root 做 `MAX-HEAPIFY(A, 1)`。

時間複雜度：

$$
O(n)+(n-1)O(\log n)=O(n\log n)
$$

## Priority Queue

| 操作 | 核心動作 | 時間 |
| --- | --- | --- |
| `MAXIMUM` | 看 root | `O(1)` |
| `EXTRACT-MAX` | 取 root、用尾端補 root、向下 heapify | `O(\log n)` |
| `INCREASE-KEY` | key 變大後往上交換 | `O(\log n)` |
| `INSERT` | 新增在尾端後往上修復 | `O(\log n)` |

## 易錯點

- Heap 不是 sorted array。
- Max-heap 只保證 parent 大於 children，不保證同層有順序。
- `Build-Max-Heap` 是 `O(n)`，`HeapSort` 是 `O(n log n)`。
