---
title: Greedy 練習題 Week 7
date: 2026-04-20
tags:
  - algorithm
  - greedy
  - practice
aliases:
  - Week 7 Greedy
source_files:
  - /Users/ryansuc/Desktop/G1-1/algo/slide/Algorithm HW Week 7_114-2.pdf
---

# Greedy 練習題 Week 7

> [!note]
> 這份不是理論講義，而是作業題單。整理方式以「解題思路」為主，方便考前快速複習。

## 1. Jump Game II

### 題意

給定陣列 `nums`，`nums[i]` 表示從位置 `i` 最多可往前跳幾步。要求最少跳幾次到最後一格。

### Greedy 想法

把每一輪可到達的範圍想成一層。

- `current_end`：這一跳最遠能覆蓋到哪
- `farthest`：在目前層中，下一跳最遠能延伸到哪

當掃描指標走到 `current_end`，就代表必須做一次跳躍，並把範圍更新成 `farthest`。

### 為什麼是 greedy

因為在同一層內，真正重要的不是「現在跳到哪一格」，而是「這一層裡哪個位置能把下一層推得最遠」。

### 複雜度

- 時間：`O(n)`
- 空間：`O(1)`

## 2. Maximum Performance of a Team

### 題意

選至多 `k` 位工程師，使

$$
\text{performance} = (\text{speed sum}) \times (\text{minimum efficiency})
$$

最大。

### Greedy + Heap 想法

先按 `efficiency` 由大到小排序。

當枚舉到某位工程師時：

- 她的 efficiency 就是目前團隊的最低 efficiency 候選
- 問題變成：在此前提下，如何讓 speed sum 最大

做法：

- 用 min-heap 維持目前選到的 speed
- 若人數超過 `k`，就丟掉最小 speed
- 每一步都計算一次當前 performance

### 為什麼這樣正確

因為一旦按照 efficiency 由大到小掃描，當前工程師的 efficiency 會決定這一輪的下限；剩下要最佳化的只剩 speed sum。

### 複雜度

- 時間：`O(n log k)`
- 空間：`O(k)`

## 3. Candy

### 題意

每個孩子至少 1 顆糖，且評分較高的孩子必須比相鄰孩子拿更多。

### Greedy 想法

做兩次掃描：

1. 左到右  
   若 `ratings[i] > ratings[i-1]`，則 `left[i] = left[i-1] + 1`
2. 右到左  
   若 `ratings[i] > ratings[i+1]`，則右掃時也必須保證更多

最後每個位置取兩邊需求的最大值。

### 為什麼需要雙向

只看左邊無法保證右側條件，只看右邊也不行。  
雙向各處理一種相鄰約束，最後合併才能同時滿足。

### 複雜度

- 時間：`O(n)`
- 空間：`O(n)`  
  若進一步優化，可壓到 `O(1)` 額外空間的變形寫法，但觀念上雙陣列最好懂。

## 4. 這份作業單在考什麼

這三題雖然形式差很多，但都在考同一件事：

- 能不能抓到真正要貪心的「局部指標」

### 對照表

| 題目 | 真正要貪心的量 |
| --- | --- |
| Jump Game II | 下一層可延伸到的最遠邊界 |
| Maximum Performance of a Team | 在固定最低 efficiency 下維持最大的 speed sum |
| Candy | 單方向鄰接約束，最後再合併左右需求 |

## 5. 複習建議

- 若題目目標是「最少步數 / 最少次數」，先想能不能用區間或層次掃描做 greedy。
- 若題目同時有排序條件與動態維持集合，常搭配 heap。
- 若限制同時來自左右鄰居，往往需要雙向 greedy。
