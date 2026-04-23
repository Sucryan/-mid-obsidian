---
title: LeetCode Part 演算法總整理
date: 2026-04-23
tags:
  - algorithm
  - leetcode
  - exam-review
aliases:
  - LeetCode 演算法口述整理
source_repo: /Users/ryansuc/Desktop/G1-1/algo/LeetCode
updated: 2026-04-23
---

# LeetCode Part 演算法總整理

> [!summary]
> 這份筆記整理 `/Users/ryansuc/Desktop/G1-1/algo/LeetCode` 中排除 Medium 題後的考試版核心演算法概念。目標不是背 pseudo code，而是考試時可以用描述講出「題目怎麼轉換、資料結構維護什麼、為什麼複雜度是這樣」。

## 口述答題模板

每題盡量照這個順序講：

1. **問題轉換**：原題其實在找什麼量，或要維護什麼 invariant。
2. **核心策略**：用 BFS、DFS、divide and conquer、heap、segment tree、monotonic deque、binary search、sweep line 等哪一類方法。
3. **關鍵維護**：資料結構裡存什麼，更新時怎麼避免重算。
4. **正確性直覺**：為什麼被丟掉的候選不可能再成為答案，或為什麼 merge / combine 後不漏答案。
5. **時間複雜度依據**：不要只寫結論，要說明排序、每層 merge、每次 heap 操作、每個節點訪問次數，或 recurrence。

## 題型快速地圖

| 題型 | Repo 題目 | 看到題目時的辨識點 | 口述重點 |
| --- | --- | --- | --- |
| Linked List 分組 | LC 25 | 每 `k` 個元素一組處理 | 先確認這組長度夠，再翻轉；不滿 `k` 保持原狀 |
| Heap + lazy deletion | LC 3510、LC 218 | 每次要取目前最大/最小，但元素會過期 | heap 存候選，pop 時檢查是否仍有效 |
| Divide and Conquer | LC 23、LC 493 | 可以拆半或由小答案構造大答案 | 解子問題後用 merge / counting 合併 |
| Merge Sort Counting | LC 493 | 要算跨左右半部的 pair 數量 | 左右排序後用雙指標數 cross pair，再做正常 merge |
| Segment Tree | LC 3165、LC 3721 | 單點/區間更新後仍要快速查詢全域性質 | node 存足夠 state，更新時沿路 recompute 或 lazy propagate |
| Sweep Line | LC 218 | 事件依照座標發生，答案只在事件點改變 | 排序所有 start/end events，用 heap 維護目前最高建築 |
| Prune and Search | LC 154 | 每一步可排除半個區間或一個重複端點 | 說清楚比較後哪一側不可能含答案 |
| Monotonic Deque | LC 1499、LC 995 | sliding window 內要快速找最大/最小或維護 flip 影響 | deque 前端是目前有效最佳候選，後端移除未來不會贏的候選 |
| Hash Mapping 壓縮取樣 | LC 710 | 要避開 blacklist 做均勻 random | 把白區黑名單映射到黑區白名單，抽 `[0, w)` 即可 |

## Week 1

### LC 25 Reverse Nodes in k-Group

**題目在問什麼**：給一條 linked list 與整數 `k`，每 `k` 個節點一組反轉；如果最後剩下的節點不足 `k` 個，就保持原本順序。

**核心概念：分組處理 linked list。**

每一輪先從目前節點往後看 `k` 個，如果不足 `k` 就直接結束，因為題目要求尾端不足一組不翻轉。此 repo 的寫法是把這 `k` 個值收進 vector 後反向寫回；口述時也可以補充標準 linked-list 解法是翻轉指標，而不是只交換值。

**複雜度依據**：每個節點被掃過常數次，時間 `O(n)`。目前寫法每組用 `O(k)` 暫存；若用指標翻轉可做到額外空間 `O(1)`。

### LC 3510 Minimum Pair Removal to Sort Array II

**題目在問什麼**：給一個陣列；每次可以選擇相鄰兩個數中「sum 最小」的 pair，把它們合併成一個數。問最少做幾次操作可以讓整個陣列變成 non-decreasing。

**核心概念：min-heap + doubly linked list simulation + lazy deletion。**

題目每次要合併相鄰 pair 中 sum 最小的一組。直接掃描會太慢，所以用 min-heap 存 `(pair_sum, left_index)`，用 `prev` / `next` 陣列模擬目前還活著的相鄰關係。合併後只有 `pre-left` 與 `left-nxt` 這些附近 pair 會改變，因此只需局部更新。heap 裡舊紀錄可能失效，所以取出時檢查左右節點是否還存在、sum 是否仍一致。

另外用 `sorted` 記錄目前有幾條相鄰邊滿足 non-decreasing。合併前先扣掉受影響舊邊，合併後再加回新邊；當 `sorted == rem - 1`，代表所有活著的相鄰邊都合法。

**複雜度依據**：每次合併會造成常數個 heap push / pop，共最多 `n-1` 次合併；heap 操作 `O(log n)`，總時間 `O(n log n)`，空間 `O(n)`。

## Week 2

### LC 23 Merge k Sorted Lists

**題目在問什麼**：給 `k` 條已經排序好的 linked lists，把它們合併成一條仍然排序好的 linked list。

**核心概念：balanced divide and conquer merge。**

如果把 list 一條一條併進同一條長 list，前面合併出的長 list 會被反覆掃描，容易變成較差的複雜度。repo 寫法改成兩兩 merge：第一輪把 `k` 條變 `k/2` 條，第二輪再變 `k/4` 條，直到剩一條。每個 level 中所有節點總共只被掃過一次。

**複雜度依據**：共有 `log k` 個 merge level，每層總處理 `N` 個節點，所以時間 `O(N log k)`；若只重接指標，額外空間主要是每輪 list 指標容器，約 `O(k)`。

### LC 493 Reverse Pairs

**題目在問什麼**：給一個整數陣列，計算有多少組 index pair `(i, j)` 滿足 `i < j` 且 `nums[i] > 2 * nums[j]`。

**核心概念：merge sort counting。**

條件是找 `i < j` 且 `nums[i] > 2 * nums[j]`。在 merge sort 中，左半與右半各自排序後，跨左右的 pair 可以用雙指標一次算完：對每個左半 `i`，右半指標 `j` 只往右移，因為左半也按順序增加，已經符合條件的右半前綴不需要重來。算完 cross count 後再做正常 merge，保持回傳區間排序。

**複雜度依據**：recurrence 是 $T(n)=2T(n/2)+O(n)$，每層 counting + merge 都是線性，因此時間 `O(n log n)`；暫存陣列空間 `O(n)`。

## Week 3

### LC 3165 Maximum Sum of Subsequence With Non-adjacent Elements

**題目在問什麼**：給陣列 `nums` 與多筆更新 query；每次更新一個位置的值後，要求目前陣列中「不能選相鄰元素」的 subsequence 最大和，並把每次結果加總取模。

**核心概念：segment tree + DP state combine。**

每個 segment tree node 不只存一個最大值，而是存 `selected[2][2]`：這段區間的左端點是否被選、右端點是否被選時，能得到的最大 non-adjacent subsequence sum。合併左右 child 時，要枚舉左右端點狀態，但排除「左 child 的右端點與右 child 的左端點同時被選」的情況。單點 update 後，只要沿著 root path 重新 combine。

**複雜度依據**：建樹 `O(n)`；每次 update 走 `O(log n)` 個節點，每個 combine 的 state 數是常數，所以每次 query/update `O(log n)`。若有 `q` 筆更新，總時間 `O((n+q)\log n)` 或更精確寫 `O(n+q log n)`，空間 `O(n)`。

### LC 3721 Longest Balanced Subarray Variant

**題目在問什麼**：給一個整數陣列，找最長 subarray，使其中「distinct even numbers」的數量等於「distinct odd numbers」的數量。

**核心概念：distinct occurrence + prefix balance + lazy segment tree。**

這題把偶數視為 `+1`、奇數視為 `-1`，但同一個值在目前 window 中只應貢獻一次，所以不能用一般 prefix sum 直接做。repo 寫法先用 occurrence queue 知道每個值下一次出現的位置，再用 segment tree 維護「從目前左端點出發時，各右端點的 balance」。當左端點右移一格時，移除這個值在 `[i+1, next_pos-1]` 區間的貢獻，這是區間加法；接著在 segment tree 中找最後一個 balance 為 `0` 的位置。

**複雜度依據**：每個左端點做一次區間更新與一次查找，segment tree 每次 `O(log n)`；總時間 `O(n log n)`，空間 `O(n)`。

## Week 4

### LC 218 The Skyline Problem

**題目在問什麼**：給一堆建築物 `[left, right, height]`，把它們疊在同一條水平線上，輸出城市天際線的關鍵轉折點，也就是高度發生改變的位置與新高度。

**核心概念：sweep line + max-heap + lazy deletion。**

答案只會在建築的左邊界或右邊界改變，所以先把每棟建築轉成 events：左邊界加入高度，右邊界加入一個結束事件。按照 `x` 排序掃描，heap 裡維護目前覆蓋此 `x` 的建築高度，並用右邊界判斷 heap top 是否過期。當目前最大高度與上一個答案高度不同，就產生 skyline key point。

**複雜度依據**：events 有 `2n` 個，排序 `O(n log n)`；每棟建築進出 heap 至多一次，heap 操作 `O(log n)`，總時間 `O(n log n)`，空間 `O(n)`。

### LC 761 Special Binary String

**題目在問什麼**：給一個 special binary string。這種字串中 `1` 和 `0` 數量相同，且任一 prefix 的 `1` 數量都不少於 `0`。可以交換相鄰的 special substring，問能得到的字典序最大字串。

**核心概念：recursive decomposition + greedy sorting。**

special string 可以視為括號結構：`1` 是左括號，`0` 是右括號。掃描時用 counter 找出最外層的獨立合法 chunk；每個 chunk 去掉外層 `1...0` 後，內部再遞迴排成最大。當同一層的 chunks 都最佳化後，因為題目允許交換相鄰 special chunks，所以把 chunks 依字典序降序排序就是當層最佳。

**複雜度依據**：每層會掃描字串並排序 chunks；保守可寫 `O(n^2)`，因為遞迴切分與字串組合可能反覆處理同一批字元。空間來自遞迴與 chunks，`O(n)` 到 `O(n^2)` 取決於語言字串複製成本。

## Week 5

### LC 1499 Max Value of Equation

**題目在問什麼**：給按照 `x` 遞增排序的 points 與距離限制 `k`，找兩個點 `i < j`，且 `x_j - x_i <= k`，最大化 `y_i + y_j + |x_i - x_j|`。

**核心概念：公式拆解 + monotonic deque。**

因為 points 已按 `x` 排序，對目前點 `(x_j, y_j)`，式子可改寫成：

$$
y_i+y_j+|x_i-x_j|=(y_i-x_i)+(y_j+x_j)
$$

其中 `j` 是目前點，前面的候選點只需要在 `x_j-x_i<=k` 的 window 內找最大的 `y_i-x_i`。deque 前端維護目前 window 中最大的 `y-x`；過期點從前端移除，新點進來前，從後端移除 `y-x` 不如新點大的候選，因為它們未來不可能贏。

**複雜度依據**：每個點進 deque 一次、出 deque 一次，時間 `O(n)`，空間 `O(n)`，通常也可說 window 內 `O(k)` 候選但實作上最壞 `O(n)`。

### LC 995 Minimum Number of K Consecutive Bit Flips

**題目在問什麼**：給一個只含 `0/1` 的陣列與整數 `k`；一次操作可以翻轉長度剛好為 `k` 的連續區間。問最少幾次操作能把所有元素變成 `1`，不可能則回傳 `-1`。

**核心概念：sliding window parity + greedy。**

從左到右掃描。當前位置受到多少個尚未過期的 flip 影響，只需要知道 flip 次數的奇偶性。若套用目前 flip parity 後，這個位置仍是 `0`，那就必須從這裡開始翻，因為之後的 flip 起點都在右邊，已經救不了目前位置。用 deque 存每個 flip 起點，超過長度 `k` 的 flip 從前端移除。

**複雜度依據**：每個 flip 起點進出 deque 一次，掃描一次陣列，時間 `O(n)`，空間 `O(n)` 或以差分陣列/計數可優化到 `O(1)` 額外狀態。

## Week 6

### LC 154 Find Minimum in Rotated Sorted Array II

**題目在問什麼**：給一個原本遞增排序、但被旋轉過的陣列，陣列中可能有重複值，找出最小元素。

**核心概念：binary search with duplicates。**

比較 `nums[mid]` 與 `nums[r]`。若 `nums[mid] > nums[r]`，最小值一定在右半，因為旋轉斷點落在 `mid` 右側；若 `nums[mid] < nums[r]`，最小值在左半含 `mid`；若兩者相等，無法判斷哪一半，但可以安全地 `r -= 1`，因為右端這個 duplicate 不是唯一必要資訊。

**複雜度依據**：一般情況每次砍半是 `O(log n)`；但 duplicates 很多時可能只做 `r -= 1`，最壞時間 `O(n)`。空間 `O(1)`。

### LC 710 Random Pick with Blacklist

**題目在問什麼**：給整數範圍 `[0, n)` 與 blacklist。要設計 `pick()`，每次等機率回傳一個不在 blacklist 的數字，而且不能每次靠重抽浪費時間。

**核心概念：hash mapping compression。**

有效答案數是 `w = n - len(blacklist)`。只從 `[0, w-1]` 這段白區抽 random，若抽到的數字不在 blacklist，直接回傳；若白區內有 blacklist，就把它映射到 `[w, n-1]` 中不是 blacklist 的數字。這樣每個合法數字仍然被等機率抽到，而且 `pick()` 不需要 retry。

**複雜度依據**：建構時掃 blacklist 並找替補位置，時間 `O(b)`，空間 `O(b)`；每次 `pick()` 是一次 random 加一次 hash lookup，平均 `O(1)`。

## 跨題型口述重點

### 1. Divide and Conquer 的複雜度講法

LC 23、LC 493 都有「由小問題組大問題」的味道，但複雜度依據不同：

- LC 23：`log k` 層，每層掃所有 linked-list nodes 一次，所以 `O(N log k)`。
- LC 493：標準 recurrence $T(n)=2T(n/2)+O(n)$，所以 `O(n log n)`。

### 2. Prune and Search 的講法

LC 154 可以和 [[04 Prune and Search]] 連起來看：

- LC 154 每次比較 `mid` 與 `r` 後，通常能刪掉半個區間；遇到 duplicate 時只能刪掉一個不影響答案的端點。
- 說明 prune 題時一定要補「為什麼被刪掉那邊不可能有答案」。

### 3. Heap / Deque / Segment Tree 的差別

- Heap：適合「一直拿目前最大/最小」，但不能直接刪任意元素，所以常配 lazy deletion。例：LC 3510、LC 218。
- Monotonic deque：適合 sliding window 裡找最佳候選，且新元素能支配舊元素。例：LC 1499、LC 995。
- Segment tree：適合更新後還要查詢全域狀態，node 必須存足夠合併的摘要資訊。例：LC 3165、LC 3721。

### 4. 考試最容易漏講的點

- 只說用了 heap，但沒說 heap 裡的舊資料如何判斷過期。
- 只說用了 segment tree，但沒說 node state 代表什麼。
- 只說 binary search，但沒說比較結果如何排除區間。
- 只寫 `O(n log n)`，但沒講排序、merge level、heap operation 或 recurrence 來源。
