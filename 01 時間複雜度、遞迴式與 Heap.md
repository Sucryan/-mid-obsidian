---
title: 時間複雜度、遞迴式與 Heap
date: 2026-04-20
tags:
  - algorithm
  - complexity
  - recurrence
  - heap
  - exam-review
aliases:
  - Time Complexity
source_files:
  - /Users/ryansuc/Desktop/G1-1/algo/slide/2026.03.13 Time Complexity.pdf
exam_priority: high
updated: 2026-04-23
---

# 時間複雜度、遞迴式與 Heap

> [!note]
> 這份投影片的重點不只是在「時間複雜度定義」，而是放在 `recurrence` 的分析方法，以及 heap / priority queue 的基本觀念。

## 考試版重點

> [!important]
> 這章考試方向集中在：Master Theorem 證明骨架、heap sort 怎麼跑、heap 作 priority queue、以及每個演算法的時間複雜度依據。

必須能口頭說出的句子：

- Master Theorem 的比較基準是 $n^{\log_b a}$，因為這是遞迴樹葉端總量的成長級別。
- `Build-Max-Heap` 不是 `n` 次 `O(log n)` 的鬆上界而已；用高度分層加總可證明是 `O(n)`。
- `HeapSort` 的 `Build-Max-Heap` 是 `O(n)`，後面做 `n-1` 次 `MAX-HEAPIFY`，總時間是 `O(n log n)`。
- Priority queue 只保證快速取最大/最小，不保證整個陣列已排序。
- Merge sort / maximum subarray 這類分治題，通常要先寫 recurrence，再用 Master Theorem 或每層成本解釋。

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

### Recursion Tree 頓悟版

> [!abstract]
> 你真正要會的，不是背 Master Theorem 表格，而是先把 recurrence 拆成「每層多少個 subproblems」和「每個 subproblem 各自花多少」。

對

$$
T(n)=aT(n/b)+f(n)
$$

遞迴樹第 `j` 層可以直接記成：

- 節點數：`a^j`
- 每個子問題規模：`n/b^j`
- 第 `j` 層總成本：`a^j f(n/b^j)`

樹高則是把問題一路切到 base case：

$$
b^k=n \Rightarrow k=\log_b n
$$

### 最底層為什麼是 $n^{\log_b a}$

最底層節點數是：

$$
a^{\log_b n}
$$

利用對數換底可化成：

$$
a^{\log_b n}=n^{\log_b a}
$$

若每個葉節點成本是 `Θ(1)`，則葉節點總成本就是：

$$
\Theta(n^{\log_b a})
$$

> [!tip]
> `n^{\log_b a}` 本質上是在算「葉節點總數的量級」，所以它才會成為 Master Theorem 的比較基準。

### 那個 $\sum$ 到底在加什麼

內部節點的總成本只是把每一層加起來：

$$
\sum_{j=0}^{\log_b n-1} a^j f(n/b^j)
$$

白話就是：

- 從第 0 層開始加
- 一路加到最底層的上一層
- 每一項都是「該層總成本」

所以整體時間：

$$
T(n)=\text{internal nodes cost}+\text{leaves cost}
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

### 證明骨架

投影片 p3-p4 放的是 Master Theorem 的 lemma 證明。考試若要「證明精神」，先把展開式寫出來：

$$
g(n)=\sum_{j=0}^{\log_b n-1} a^j f(n/b^j)
$$

再分三種情況說明：

| Case | 證明關鍵 | 最後為什麼成立 |
| --- | --- | --- |
| Case 1 | $f(n)$ 比 $n^{\log_b a}$ 小一個 $n^\varepsilon$ | 每層往下變重，是遞增幾何級數，總和被葉端量級支配 |
| Case 2 | $f(n)$ 和 $n^{\log_b a}$ 同階 | 每一層貢獻同階，一共有 $\log_b n$ 層 |
| Case 3 | $af(n/b)\le cf(n)$，其中 $c<1$ | 每層往下總成本以常數比例下降，是遞減幾何級數，總和由根層 $f(n)$ 支配 |

> [!tip] 證明時的安全寫法
> 先說 `T(n)` 展開成各層成本總和，再說三個 case 分別對應「葉端重、每層一樣重、根端重」。若題目要更正式，再補上 $g(n)$ 的 summation。

### Case 1 你這次打通的點

情境是：

$$
f(n)=O(n^{\log_b a-\varepsilon})
$$

也就是內部節點比葉節點小一截。代回每層總成本：

$$
a^j f(n/b^j)
\le
a^j O\left((n/b^j)^{\log_b a-\varepsilon}\right)
$$

整理後，外面可以提出：

$$
O(n^{\log_b a-\varepsilon})
$$

而 summation 裡會留下公比是 $b^\varepsilon$ 的等比級數。最後分子會出現：

$$
(b^\varepsilon)^{\log_b n}=n^\varepsilon
$$

所以你看到的那個「神奇 merge」其實就是：

$$
n^{\log_b a-\varepsilon}\cdot n^\varepsilon=n^{\log_b a}
$$

因此內部節點總和最多是：

$$
O(n^{\log_b a})
$$

不會超過葉節點量級，所以 Case 1 由 leaves 主導。

### Regularity condition

Case 3 需要額外確認：

$$
af(n/b)\le cf(n), \quad c<1
$$

意思是：往下一層之後，總成本真的有明顯衰減，才可以說頂層的 `f(n)` 佔主導地位。

### Case 3 頓悟版

情境是：

$$
f(n)=\Omega(n^{\log_b a+\varepsilon})
$$

這代表根節點成本非常大，但這還不夠，還必須確認下面每一層真的有穩定縮水，所以才需要 regularity condition：

$$
af(n/b)\le cf(n),\quad c<1
$$

白話文：

- 下一層總成本
- 至多只有上一層的 `c` 倍
- 而且 `c<1`

所以：

- 第 1 層至多是 `cf(n)`
- 第 2 層至多是 `c^2 f(n)`
- 第 `j` 層至多是 `c^j f(n)`

於是內部節點總成本被壓成：

$$
f(n)+cf(n)+c^2f(n)+\cdots
$$

這是收斂等比級數，因此：

$$
\sum_{j\ge 0} c^j f(n)=O(f(n))
$$

又因為根節點本身就有 `f(n)`，所以：

$$
T(n)=\Theta(f(n))
$$

> [!tip]
> Case 3 最重要的直覺不是「`f(n)` 比較大」而已，而是「往下每一層都會固定比例縮水」，所以整棵樹像頭重腳輕，總成本被根節點吃掉。

### 為什麼講義最後會冒出 `O(1)`

> [!question]
> 這通常是因為 regularity condition 只保證在 `sufficiently large n` 時成立。

當遞迴走到非常底層，小到某個程度時，不能再保證

$$
af(n/b)\le cf(n)
$$

還繼續成立。  
但這些失效的底層只剩常數層、常數個節點，所以把它們整包補成一個：

$$
O(1)
$$

這只是數學上的嚴謹補丁，不影響主量級仍然是 `Θ(f(n))`。

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

### `BUILD-MAX-HEAP`

投影片從 `floor(n/2)` 往 `1` 做 `MAX-HEAPIFY`，原因是：

- `floor(n/2)+1` 到 `n` 都是 leaf，不需要 heapify。
- 由下往上做時，每個節點呼叫 `MAX-HEAPIFY` 前，它的左右子樹已經是 max-heap。
- 這剛好滿足 `MAX-HEAPIFY` 的先決條件。

時間複雜度不能只寫 `O(n log n)`；精準分析是：

$$
\sum_{h=0}^{\lfloor \log n \rfloor} \left\lceil \frac{n}{2^{h+1}} \right\rceil O(h)
= O\left(n\sum_{h=0}^{\infty}\frac{h}{2^h}\right)=O(n)
$$

因為高度大的節點很少，高度小的節點很多，總和仍是線性。

### `BUILD-MAX-HEAP` 頓悟版

> [!abstract]
> 你這次最重要的直覺有兩個：起點為什麼是 `floor(n/2)`，以及為什麼要 `downto 1`。

- 為什麼從 `floor(n/2)` 開始：
  因為 `floor(n/2)+1` 到 `n` 全是 leaf。leaf 本身已經是合法 heap，不需要另外處理。
- 為什麼一定要由下往上：
  因為 `MAX-HEAPIFY` 的先決條件是左右子樹都已經是 max-heap。
- 為什麼不是 `O(n log n)`：
  因為這棵樹像金字塔，底層節點最多，但幾乎不用沉；頂層節點最少，才可能沉很多步。

可以直接把這個圖像記成一句話：

> [!tip]
> 節點數量很多的地方，高度很小；高度很大的地方，節點很少。所以 `BUILD-MAX-HEAP` 的總成本會收斂成 `O(n)`。

### `HEAPSORT`

流程：

1. 先 `BUILD-MAX-HEAP(A)`。
2. 每回合把根節點最大值和最後一個 heap 元素交換。
3. 縮小 `heap-size`。
4. 對根節點做 `MAX-HEAPIFY(A, 1)` 修復 heap。

時間複雜度：

$$
O(n) + (n-1)O(\log n)=O(n\log n)
$$

### `HEAPSORT` 頓悟版

你這次抓到的本質很準：

- `BUILD-MAX-HEAP` 是從下往上，平均起來很便宜，所以是 `O(n)`。
- `HEAPSORT` 每一輪都把最後一個元素丟到根節點。
- 根節點是最容易觸發最壞沉降的位置。
- 而且這件事要做 `n-1` 次。

所以排序階段就是老老實實被：

$$
(n-1)\cdot O(\log n)
$$

卡住，整體變成 `O(n log n)`。

> [!warning]
> `BUILD-MAX-HEAP` 是 `O(n)`，但 `HEAPSORT` 是 `O(n log n)`；不要把兩者混成同一個複雜度。

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

### 操作複雜度表

| 操作 | 做什麼 | 時間 |
| --- | --- | --- |
| `MAXIMUM` | 回傳根節點 | `O(1)` |
| `EXTRACT-MAX` | 根與最後元素交換、縮 heap、向下修復 | `O(log n)` |
| `INCREASE-KEY` | key 變大後向上和 parent 交換 | `O(log n)` |
| `INSERT` | 先放 `-∞` 再 `INCREASE-KEY` | `O(log n)` |

### Priority Queue 頓悟版

- `EXTRACT-MAX`：把最後一個元素補到 root，再做 `MAX-HEAPIFY`，所以是「往下沉」，複雜度 `O(log n)`。
- `INCREASE-KEY`：值變大後可能比 parent 還大，所以會一路和 parent 比，是「往上浮」，複雜度 `O(log n)`。
- `INSERT`：先在尾端放 `-\infty`，只是初始化技巧，避免直接呼叫 `INCREASE-KEY` 時觸發「new key is smaller than current key」的錯誤。

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

## 9. 考前自問

- 你能不能不看表，說出 Master Theorem 三個 case 的「誰主導」？
- 你能不能自己寫出第 `j` 層成本 `a^j f(n/b^j)`，並解釋最底層為什麼是 $n^{\log_b a}$？
- 你能不能用「下一層總成本至多是上一層的 `c` 倍」這句白話，講出 Case 3 的證明？
- 你能不能解釋講義 Case 3 最後為什麼會補一個 `O(1)`？
- 你能不能用高度分層說明 `BUILD-MAX-HEAP` 為什麼是 `O(n)`？
- 你能不能說出 `MAX-HEAPIFY` 的先決條件？
- 你能不能把 merge sort 寫成 $T(n)=2T(n/2)+\Theta(n)$ 並套 Case 2？

相關速記可看 [[Heap]]。
