---
title: Prune and Search
date: 2026-04-20
tags:
  - algorithm
  - prune-and-search
  - linear-programming
aliases:
  - 修剪與搜尋
source_files:
  - /Users/ryansuc/Desktop/G1-1/algo/slide/2026.04.10 Prune  Search (1).pdf
  - /Users/ryansuc/Desktop/G1-1/algo/slide/2026.04.17 Pune  Search範例補充_上傳E3用.pdf
---

# Prune and Search

## 1. 什麼是 Prune and Search

Prune and Search 的精神是：

- 先用某種判斷找出答案落在哪一側
- 安全刪去一批「不可能影響最優解」的候選
- 在縮小後的問題上繼續搜尋

若每輪都能刪掉固定比例，常見 recurrence 會像：

$$
T(n)=T((1-c)n)+O(n)
$$

因此總時間通常可做到線性或接近線性。

## 2. Selection Problem

投影片一開始就是典型的「分成 5 個一組」例子，對應到 median-of-medians。

### 核心流程

1. 把元素分成每組 5 個
2. 各組排序並取中位數
3. 對這些中位數再找中位數，作為 pivot
4. 用 pivot 做 partition
5. 只遞迴到真正包含答案的那一側

### 為什麼這招厲害

這個 pivot 品質夠穩定，可以保證每次都刪掉一定比例的元素，因此 worst-case 仍是：

$$
O(n)
$$

## 3. Simplified Two-Variable Linear Programming

投影片的重要主題之一是二變數線性規劃的 prune-and-search 解法。

### 問題型態

可寫成：

$$
\text{minimize } y
$$

subject to

$$
y \ge a_i x + b_i
$$

也就是要在多條直線上方的可行區域裡，找到 `y` 最小的點。

### 關鍵函數

令

$$
F(x)=\max_i(a_i x+b_i)
$$

則問題變成：找使 `F(x)` 最小的 `x`。

### 演算法核心

1. 把限制式兩兩配對，找每對交點的 `x` 座標
2. 取這些交點 `x` 的 median，記作 `x_m`
3. 算 `y_m = F(x_m)`
4. 觀察在 `(x_m, y_m)` 上達到最大值的那些限制式斜率

投影片使用：

- `g_min`：在 `(x_m, y_m)` 上的最小斜率
- `g_max`：在 `(x_m, y_m)` 上的最大斜率

### 決策規則

- 若 `g_min < 0 < g_max`，代表 `x_m` 就是最優解
- 若所有有效斜率都大於 0，最優解在左側
- 若所有有效斜率都小於 0，最優解在右側

### 為什麼可以 prune

一旦知道 `x* < x_m` 或 `x* > x_m`：

- 某些交點若落在錯的那一側
- 其中一條限制式就永遠不可能成為最優邊界
- 可以安全刪掉

這就是 prune-and-search 的核心。

## 4. 一般二變數線性規劃如何化簡

投影片補充了更一般的形式：

$$
\text{minimize } ax+by
$$

subject to linear constraints.

做變數轉換後，可改寫成：

$$
\text{minimize } y
$$

再配合上下包絡線：

- `F_1(x)`：下界直線們的上包絡，為 convex piecewise linear function
- `F_2(x)`：上界直線們的下包絡，為 concave piecewise linear function

最後等價成：

$$
\text{minimize } F_1(x)
$$

subject to

$$
F_1(x)\le F_2(x), \quad a\le x\le b
$$

也就是把一般問題轉回 simplified 版本。

## 5. Constrained 1-Center Problem

### 問題

給定很多平面點，想找一個最小圓覆蓋所有點，但圓心被限制在一條直線上。

### 直覺

對每一對點，會導出某種「半徑需求函數」。

- 某些區段由某些點對主導
- 只要能判定最佳中心在某個 `x_m` 的左或右
- 就能刪去一批不再可能成為主導者的候選

這跟二變數線性規劃的想法非常像：  
重點不在暴力枚舉每個位置，而是在包絡線與交點上做決策。

## 6. Chip Testing Problem

### 題目設定

- 好晶片會誠實回報別人的好壞
- 壞晶片的回報完全不可信
- 已知好晶片比壞晶片多

### 核心做法

把晶片兩兩拿去互測：

- 若兩者都說對方好，保留其中一顆
- 其他情況可以一次丟掉那一對或大幅縮小候選

重複做下去後，能用 `O(n)` 測試找出一顆確定是好的晶片，再用它去驗其他晶片。

### 關鍵理由

因為好晶片數量嚴格多於壞晶片，所以遞迴縮減後，最後不會只剩壞晶片陣營。

## 7. Counterfeit Coin Problem

### 問題

`N` 枚看起來一樣的硬幣裡，有一枚比較輕的偽幣，只能用天秤比較。

### 策略

- 每次盡量分成 3 等份
- 左右兩組上秤
- 根據結果把候選縮到其中 1/3 左右

### 複雜度

$$
O(\log_3 N)
$$

這題雖然常被歸類成分治或資訊理論題，但從「每輪排除固定比例候選」的角度看，也很符合 prune-and-search 精神。

## 8. 本章最該記住的事

> [!summary]
> Prune and Search 的重點不是「試很多次」，而是「每次判斷完都能安全丟掉很多候選」。

- Selection：丟掉固定比例元素
- Linear Programming：丟掉不可能成為最優邊界的限制式
- 1-center：丟掉不可能主導最小半徑的候選
- Chip testing：丟掉不可靠配對
- Counterfeit coin：丟掉不可能含偽幣的組別
