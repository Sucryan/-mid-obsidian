---
title: 時間複雜度、遞迴式與 Heap
date: 2026-04-20
tags:
  - algorithm
  - complexity
  - recurrence
  - heap
aliases:
  - Time Complexity
source_files:
  - /Users/ryansuc/Desktop/G1-1/algo/slide/2026.03.13 Time Complexity.pdf
---

# 時間複雜度、遞迴式與 Heap

> [!note]
> 這份投影片的重點不只是在「時間複雜度定義」，而是放在 `recurrence` 的分析方法，以及 heap / priority queue 的基本觀念。

## 1. 分析遞迴式的核心觀念

很多 divide-and-conquer 演算法都會長成：

$$
T(n)=aT(n/b)+f(n)
$$

- `a`：子問題個數
- `b`：問題規模縮小比例
- `f(n)`：切分、合併、額外處理成本

分析時先問三件事：

1. 遞迴樹一共有幾層？
2. 每一層總成本是多少？
3. 葉節點總成本是否可忽略？

## 2. Recursion Tree 範例

投影片用

$$
T(n)=2T(n/2)+cn
$$

當作示範。

### 直覺

- 第 0 層成本：`cn`
- 第 1 層有 2 個子問題，每個成本 `c(n/2)`，總和仍是 `cn`
- 第 2 層有 4 個子問題，每個成本 `c(n/4)`，總和仍是 `cn`
- 如此一路到底，每一層的總成本都差不多是 `cn`

若遞迴高度是 `log_2 n`，則總成本大約是：

$$
cn \cdot \log n + \text{leaf cost}
$$

因此：

$$
T(n)=\Theta(n\log n)
$$

## 3. Master Theorem

對於

$$
T(n)=aT(n/b)+f(n)
$$

比較 `f(n)` 和 `n^{\log_b a}` 的大小關係。

| 情況 | 條件 | 結論 | 直覺 |
| --- | --- | --- | --- |
| Case 1 | $f(n)=O(n^{\log_b a-\varepsilon})$ | $T(n)=\Theta(n^{\log_b a})$ | 葉節點與遞迴展開較重 |
| Case 2 | $f(n)=\Theta(n^{\log_b a})$ | $T(n)=\Theta(n^{\log_b a}\log n)$ | 每一層成本差不多 |
| Case 3 | $f(n)=\Omega(n^{\log_b a+\varepsilon})$ 且滿足 regularity condition | $T(n)=\Theta(f(n))$ | 合併成本主導整體 |

### Regularity condition

Case 3 需要額外確認：

$$
af(n/b)\le cf(n), \quad c<1
$$

意思是：往下一層之後，總成本真的有明顯衰減，才可以說頂層的 `f(n)` 佔主導地位。

## 4. 使用 Master Theorem 的流程

1. 寫出 `a`、`b`、`f(n)`。
2. 算出 `n^{\log_b a}`。
3. 比較 `f(n)` 與 `n^{\log_b a}`。
4. 確認是否需要 regularity condition。
5. 寫出最終的 `Θ` 結果。

### 常見例子

- `T(n)=2T(n/2)+n` -> `Θ(n log n)`
- `T(n)=2T(n/2)+1` -> `Θ(n)`
- `T(n)=8T(n/2)+n^2` -> `Θ(n^3)`

## 5. Heap 的核心觀念

投影片後半段用樹圖示範了 heap 的調整過程。

### Max-Heap 性質

- 每個節點的 key 都大於等於它的孩子。
- 根節點一定是最大值。
- 常用陣列表示，父子索引可直接換算。

### `MAX-HEAPIFY`

`MAX-HEAPIFY` 的工作是：

- 假設左右子樹都已經是 max-heap。
- 只修正某一個節點違反 heap property 的情況。
- 透過向下交換，把較小的值往下沉。

時間複雜度：

$$
O(\log n)
$$

因為最多只會沿著樹高一路往下走。

## 6. Priority Queue

投影片列出的 max-priority queue 基本操作：

- `INSERT(S, x)`：插入元素
- `MAXIMUM(S)`：查最大值
- `EXTRACT-MAX(S)`：取出並刪除最大值
- `INCREASE-KEY(S, x, k)`：把某元素 key 提高

### 為什麼 heap 適合做 priority queue

- 取最大值快：`O(1)`
- 插入、調整、刪最大值都只需沿樹高移動：`O(log n)`
- 結構簡單，陣列即可實作

## 7. 這章最該記住的事

- recurrence 分析的核心是「每層成本」和「樹高」。
- `n^{\log_b a}` 是 Master Theorem 的比較基準。
- heap 的關鍵不是排序完成，而是維持局部結構條件。
- priority queue 的本質是「隨時快速取得最大或最小元素」。

## 8. 易錯點

> [!warning]
> `T(n)=aT(n/b)+f(n)` 不能直接套公式就結束，Case 3 還要檢查 regularity condition。

> [!warning]
> `MAX-HEAPIFY` 只保證修正某個根節點以下的 heap，不等於整棵樹已經建好。

> [!tip]
> 看 recurrence 時，先問「每層成本是不是一樣」；很多題目會直接因此看出是 `Θ(n log n)`。
