---
title: Divide and Conquer
date: 2026-04-20
tags:
  - algorithm
  - divide-and-conquer
  - geometry
aliases:
  - 分治法
source_files:
  - /Users/ryansuc/Desktop/G1-1/algo/slide/2026.03.20 Divide and Conquer.pdf
---

# Divide and Conquer

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

## 2. Maximum Subarray Problem

### 問題轉換

投影片用股價變化當例子：

- 先看每天價格差 `change[i]`
- 找出連續區間總和最大的那段
- 那段區間就對應到「低買高賣」的最佳區間

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

### 複雜度

$$
T(n)=2T(n/2)+\Theta(n)=\Theta(n\log n)
$$

## 3. Closest Pair of Points

### 問題

給定平面上 `n` 個點，求最近的兩點距離。

### 分治流程

1. 依 `x` 與 `y` 排序
2. 用垂直中線切成 `S_L` 與 `S_R`
3. 分別遞迴求左右最近距離 `d_L`、`d_R`
4. 令 `d=min(d_L,d_R)`
5. 再檢查靠近中線、寬度 `2d` 的 strip

### 核心技巧

strip 裡不需要暴力兩兩比較。

- 先依 `y` 排序
- 對每個點只需檢查常數個後繼點
- 因此 merge 階段可壓在線性時間

### 複雜度

$$
\Theta(n\log n)
$$

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
