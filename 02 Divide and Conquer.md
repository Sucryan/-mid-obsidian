---
title: Divide and Conquer
date: 2026-04-20
tags:
  - algorithm
  - divide-and-conquer
  - geometry
  - exam-review
aliases:
  - 分治法
source_files:
  - /Users/ryansuc/Desktop/G1-1/algo/slide/2026.03.20 Divide and Conquer.pdf
exam_priority: medium
updated: 2026-04-23
---

# Divide and Conquer

## 考試版重點

> [!important]
> 這章不要只背題名；考試通常會看你能不能說出 `Divide`、`Conquer`、`Combine`，再寫出時間複雜度依據。

高頻答題模板：

- `Divide`：怎麼切問題，通常是切成左右兩半。
- `Conquer`：左右子問題是否同型，寫出遞迴呼叫。
- `Combine`：最容易考，必須說明如何避免暴力合併。
- `Complexity`：寫成 recurrence，再連回 [[01 時間複雜度、遞迴式與 Heap#3. Master Theorem]]。

> [!note]
> 考試方向提到「divide and conquer 蠻 hard 用血的、解題思維、時間複雜度計算」。所以本章複習重心是 combine 的設計理由，不是把投影片流程背成條列。

## 1. Divide and Conquer 的通用套路

Divide and Conquer 的典型流程：

1. `Divide`：把原問題拆成較小子問題。
2. `Conquer`：遞迴解子問題。
3. `Combine`：把子解合併成整體答案。

常見 recurrence：

$$
T(n)=aT(n/b)+f(n)
$$

這章的多數問題都能回到 [[01 時間複雜度、遞迴式與 Heap#3. Master Theorem]] 的分析框架。

### Merge Sort 型題目的標準複雜度

很多題目雖然不是直接問 merge sort，但結構會長得一樣：

$$
T(n)=2T(n/2)+\Theta(n)
$$

用 Master Theorem：

- `a=2`
- `b=2`
- $n^{\log_b a}=n$
- $f(n)=\Theta(n)$

所以是 Case 2：

$$
T(n)=\Theta(n\log n)
$$

## 2. Maximum Subarray Problem

### 問題轉換

投影片用股價變化當例子：

- 先看每天價格差 `change[i]`
- 找出連續區間總和最大的那段
- 那段區間就對應到「低買高賣」的最佳區間
- 例如 `[13, -3, -25, 20, -3, -16, -23, 18, 20, -7, 12]`，就是在找總和最大的連續區間

### 分治想法

對區間 `[low, high]`：

1. 解左半部的最佳 subarray
2. 解右半部的最佳 subarray
3. 解「跨越中點」的最佳 subarray
4. 三者取最大

### 關鍵

跨中點的最佳解可在線性時間內求出：

- 從中點往左找最大 suffix sum
- 從中點右邊往右找最大 prefix sum
- 相加就是 crossing subarray

白話說，就是從中點往左一路累加，只要 sum 變大就更新 `max_left`；再從中點往右一路累加，只要 sum 變大就更新 `max_right`。跨中點答案一定是這兩段接起來。

### 複雜度

$$
T(n)=2T(n/2)+\Theta(n)=\Theta(n\log n)
$$

考試寫法：

- `Divide` 是切左右半段。
- `Combine` 是花 `O(n)` 找 crossing subarray。
- 因此可直接套 Master Theorem Case 2。

## 3. Closest Pair of Points

### 問題

給定平面上 `n` 個點，求最近的兩點距離。

### 分治流程

1. 依 `x` 與 `y` 排序
2. 找到 `x` 的中位數，用垂直中線切成 `S_L` 與 `S_R`
3. 分別遞迴求左右最近距離 `d_L`、`d_R`
4. 令 `d=min(d_L,d_R)`
5. 再檢查靠近中線、寬度 `2d` 的 strip

### 核心技巧

strip 裡不需要暴力兩兩比較。

- 先依 `y` 排序
- 因為 strip 裡的點都離中線很近，`x` 座標差距被限制住
- 依 `y` 排序後，對每個點只需檢查常數個後繼點
- 因此 merge 階段可壓在線性時間

這裡的幾何證明考試通常可以直接用結論：在寬度 `2d` 的 strip 中，若兩點的 `y` 差也太大，就不可能比目前的 `d` 更近；真正需要檢查的附近點只有常數個。

### 複雜度

$$
\Theta(n\log n)
$$

複雜度依據：

$$
T(n)=2T(n/2)+O(n)
$$

只要 strip merge 能維持線性，整體就是 `Θ(n log n)`；若 strip 用暴力兩兩比，combine 會炸掉，分治就失去意義。

## 4. Convex Hull

### 定義

Convex hull 是「包住所有點的最小凸多邊形」。

### 投影片中的分治做法

1. 點數很少時直接 brute force
2. 用 median line 把點切成左、右兩半
3. 分別遞迴求 `Hull(S_L)` 與 `Hull(S_R)`
4. 合併兩個 hull

### 合併直覺

投影片搭配了 circular scan / Graham scan 的圖。

可把候選頂點序列合併後，用 scan 去掉 reflexive points，也就是不應該留在凸包上的內凹點。

### 你要記的不是細節，而是：

- 分治先把點集切開
- 合併時只保留外層邊界
- scan 類方法的任務是刪掉「方向轉錯」的點

### 凸包補充讀法

外部補充文章的重點可壓成三句：

- 先依座標排序，掃描時維護一個 stack。
- 用 cross product 判斷三點轉向，轉錯就 pop。
- 上凸包與下凸包分別掃，最後合併。

這和投影片的分治凸包不同，但共同核心都是：用 scan 排除 reflexive / 轉向錯誤的點。

### 投影片圖的實際讀法

投影片中的 circular scan / Graham scan 圖，可以用下面方式理解。這比較像「讀圖流程」，不是唯一的凸包實作方式。

1. 在左側 hull 選一個參考點 $p$，可把它想成用來觀察角度順序的中心。
2. 從 $p$ 往右側 hull 看，找到視角的上下極限點，記成 $v_1,v_2$。投影片範例圖中類似 `d` 和 `a`。
3. `sequence1`：左側 hull 的點，依照繞 $p$ 的逆時針順序排列。
4. `sequence2`：右側 hull 的外側路徑，從 $v_2$ 逆時針走到 $v_1$。
5. `sequence3`：右側 hull 的內側路徑，從 $v_2$ 順時針走到 $v_1$，通常不含 $v_1,v_2$ 本身。
6. 把三段序列按照以 $p$ 為中心的極角順序 merge 起來，得到一圈候選頂點。
7. 對候選序列做 Graham scan：每次看連續三點，外凸包應該維持同一種轉向；若中間點造成右轉或 reflexive turn，就把中間點刪掉，退回去繼續檢查。

為什麼要先分 `sequence1/2/3`？

> [!note]
> 這是為了讓 merge 時比較容易維持環狀順序。真正用來刪點的是後面的 scan；序列拆分只是讓候選點能照正確繞行順序排好。

複雜度可寫成：

$$
T(n)=2T(n/2)+O(n)=O(n\log n)
$$

其中 `Divide` 只是在 median line 左右切分，`Combine` 則是 merge 候選序列並用 scan 刪掉方向轉錯的點。

## 5. Voronoi Diagram

### 定義

對一組點 `S`，Voronoi diagram 會把平面切成多個區域；每個區域內的點都離某個站點最近。

### 分治建構流程

1. 若只剩一個點，直接回傳
2. 用中線分成 `S_L`、`S_R`
3. 遞迴建出 `VD(S_L)`、`VD(S_R)`
4. 建立一條 piecewise linear hyperplane 來合併兩邊
5. 刪去被分界線排除的邊

### 合併觀念

真正重要的是：

- 左右各自的 Voronoi diagram 已經正確
- 只要找出左右兩群點「最近關係」的分界
- 再把不可能屬於最終答案的邊切掉即可

## 6. 這章的共同模式

這幾題雖然長得不同，但骨架都很像：

- 先切成左右兩半
- 左右各做一樣的事
- 最難的是設計 `combine`

### 這章最有代表性的 combine 設計

- Maximum subarray：跨中點區間
- Closest pair：中線附近 strip
- Convex hull：merge + scan
- Voronoi：piecewise linear hyperplane

## 7. 複習摘要

> [!summary]
> Divide and Conquer 的難點通常不是遞迴本身，而是「合併階段如何避免退化成暴力解」。

- Maximum subarray：關鍵在 crossing case
- Closest pair：關鍵在 strip 只檢查常數個點
- Convex hull：關鍵在 merge 後刪除 reflexive points
- Voronoi：關鍵在左右圖的合併邊界

## 8. 延伸閱讀

- 若想補 Voronoi 與最近鄰搜尋，接著看 [[03 Voronoi Diagram 與最近鄰補充]]。
- 凸包掃描線與 cross product 觀念可參考 [WiwiHo 凸包筆記](https://hackmd.io/@wiwiho/CPN-convex-hull)。
