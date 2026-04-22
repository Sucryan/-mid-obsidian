---
title: Voronoi Diagram 與最近鄰補充
date: 2026-04-20
tags:
  - algorithm
  - voronoi
  - nearest-neighbor
aliases:
  - Voronoi 補充
source_files:
  - /Users/ryansuc/Desktop/G1-1/algo/slide/2026.03.20 Divide and Conquer.pdf
  - /Users/ryansuc/Desktop/G1-1/algo/slide/2026.04.10 Divide and Conquer_更新最後兩頁 (1).pdf
---

# Voronoi Diagram 與最近鄰補充

> [!note]
> 這份補充主要在回答一個問題：為什麼 Voronoi diagram 不只是幾何圖，而是真的能加速 nearest neighbor 問題？

## 1. 最近鄰搜尋問題

給定一組平面點 `P_1, P_2, ..., P_n`，以及查詢點 `P`，想找出距離 `P` 最近的站點。

### 直接做法

- 把 `P` 跟所有點都比一次
- 時間複雜度：`O(n)`

### 使用 Voronoi diagram

- 先建好整張 Voronoi diagram
- 查詢時先判斷 `P` 落在哪一個 Voronoi region
- 該 region 對應的站點就是 `P` 的最近鄰

### 複雜度概念

- 預處理：建圖通常是 `O(n log n)`
- 查詢：若搭配 point-location 結構，可做到 `O(log n)` 等級

## 2. 為什麼這個想法成立

Voronoi region 的定義就是：

- 區域內所有點
- 都比其它站點更接近某一個固定站點

因此如果查詢點落在 `R_i`，那它的最近點必然就是 `P_i`。

這本質上是把「找最近點」改寫成「先找區域」。

## 3. All Nearest Neighbor 問題

投影片還提到更進一步的版本：

- 不只查一個 query point
- 而是對每個輸入點 `P_i`
- 都找出它在集合中的最近鄰

### 關鍵性質

若 `P_j` 是 `P_i` 的最近鄰，則 `P_i` 和 `P_j` 會共享同一條 Voronoi edge。

因此：

- 不必拿 `P_i` 去和所有點比較
- 只要檢查與 `P_i` 對應 cell 相鄰的站點即可

這也是 Voronoi diagram 很適合處理鄰近關係的原因。

## 4. 與 Divide and Conquer 的關聯

在 [[02 Divide and Conquer]] 中，Voronoi diagram 的重點是「怎麼建」。

本筆記的重點則是「建好之後能做什麼」：

- nearest neighbor search
- all nearest neighbor
- 幾何鄰接結構分析

## 5. 投影片列出的應用

- Clustering，例如 DBSCAN
- Anomaly detection
- Mesh processing / computer graphics
- Bioinformatics / pattern recognition

### 為什麼這些領域會用到它

因為它們都在問某種「鄰近性」問題：

- 哪些點彼此接近？
- 某點落在哪個 influence region？
- 區域邊界在哪裡？

## 6. 一句話總結

> [!summary]
> Voronoi diagram 的價值，在於把「距離比較」轉成「區域定位」與「鄰接結構」問題。

如果你在考題中看到：

- 最近鄰
- 空間分割
- 點與點之間的鄰接關係

就可以開始聯想到 Voronoi diagram。
