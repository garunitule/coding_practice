## 取り組み方
step1: 5分考えて分からなかったら答えを見る。答えを理解したら、答えを隠して書く。筆が進まず5分立ったら答えを見る。答えを送信して正解するまで。
step2: コードを読みやすく整える。動くコードになったら終了。
step3: 時間を計りながら書く。10分以内に3回連続でアクセプトされるまで。

## step1
ソートされた要素を全部保持しておくのは計算量が大きくなってしまう
ソートしてk番目以降を保持しておけばよさそう
ヒープで保持しておいて、addメソッドではヒープに追加して最小要素を返す実装にすればよい

heapqパッケージを確認する
initの実装方法は
- nlargestを使う
- addを使う
の2つがありそう
nlargestのほうがシンプルに実現できそうなのでこちらを採用する

addの実装方法は
- heappushpopを使う
かな

なおadd一回あたりの計算量は、k番目まで大きい値を保持するとすれば、
- 時間計算量：O(logk)
- 空間計算量：O(k)
となるはず。
ただ、あとでPythonのheapqの実装は確認しておく。

1 <= k <= nums.length + 1
なのでaddした後に保持するheapの長さより大きいkとなることはない
そのため、addの実装はheappushpopしたあとの先頭の要素を返せばよい

最初に提出したコード。add時に `IndexError: list index out of range` が出ている
```python
import heapq
class KthLargest:
    heap_nums: List[int]

    def __init__(self, k: int, nums: List[int]):
        self.heap_nums = heapq.nlargest(k, nums)
        self.heap_nums.sort()     
 
    def add(self, val: int) -> int:
        heapq.heappushpop(self.heap_nums, val)
        return self.heap_nums[0]
```

下記の入力のとき。
```
[[1,[]],[-3],[-2],[-4],[0],[4]]
```

たしかにこのときはheappushして、そのあとheap_nums[0]を返す必要がある。

kとnumsの長さの関係に考慮漏れがあった。

実装は通った
```python
import heapq
class KthLargest:
    heap_nums: List[int]
    k: int

    def __init__(self, k: int, nums: List[int]):
        self.k = k
        self.heap_nums = heapq.nlargest(k, nums)
        self.heap_nums.sort()     
 
    def add(self, val: int) -> int:
        if len(self.heap_nums) < self.k:
            heapq.heappush(self.heap_nums, val)
        else:
            heapq.heappushpop(self.heap_nums, val)
        return self.heap_nums[0]

```

## step2
### step1で気になったこと考える
良いコードにするために、処理を自然言語でちゃんと書く
まずaddについて考える。
addはk番目に大きい数を返す必要がある。
ということは、
- ヒープの長さがkに満たない場合は、引数valをヒープに追加する必要がある
- ヒープの長さがkの場合は、引数valをヒープに追加してポップする必要がある
ただ、
- ヒープの長さがkの場合かつ、引数val <= ヒープの先頭要素、の場合ヒープに追加しなくてよい。heappushを呼び出しても結果は一緒だから。

次に__init__について考える。
変数名heap_numsはどうなんだろう。
役割は大きい順にk番目まで最小ヒープ形式で保持すること
となると、
- top_k_min_heap
とかかな？
データ構造を示さなくてよいなら、top_k_valsとかもありそう。
今回はヒープであることを明示したいので、top_k_min_heapがよさそう。

### 他のPR
https://github.com/Satorien/LeetCode/pull/8/files
https://github.com/shintaro1993/arai60/pull/12/files
2分探索で解く方法もある。
addにO(logn)（nはnumsの要素数）かかる。k<=n+1なのでヒープを使った解法のほうが早そう。
と思ったけど、最初にソートしてk番目までの最大値をソートされたまま保持しておけばO(logk)でaddできるか

__init__の実装方針もいくつかある。
- addを使う
- nlargest後にsortを呼ぶ
- heapify後に長さがkになるようにpopする
あたり。
時間計算量はnをnumsの要素数とすれば、
- O(nlogk)（ただし、場合によっては何もしないケースもある）
- O(nlogk) （後述の調査を参照）
  - nlargest: O(nlogk)
  - sort: O(klogk)
  - 1 <= k <= n + 1
- O(n + (n - k)logn)
  - heapify: O(n)
  - pop: O((n - k)logn)
となる
kが大きいほどheapifyを利用した方式のほうが早いし、kが小さいほどheapifyを利用した方式のほうが遅い

nlargestの計算量だけ確認する。CPythonだと下記。
https://github.com/python/cpython/blob/f2379535fe2d2219b71653782d5e31defd9b5556/Lib/heapq.py#L523

n==1なら、O(N)
n>=len(iterable)なら、O(NlogN)
key is Noneなら、O(Nlogn)
- n + n + (N - n)logn + nlogn + n
- n <= N
それ以外なら、key次第。。

今回のパターンだと、key is Noneの場合になるので、O(Nlogn)
今回問題に変数を合わせれば、
O(nlogk)
となる

もっと調べるなら定数項の影響まで調べたほうが良いんだろうけど、これ以上は時間がかかりすぎるので調査しない。
もっと低レイヤの最適化とかもあるかもしれないが、いったん調査はここまで。
シンプルなnlargestを使った解法にする

https://docs.python.org/ja/3.13/library/heapq.html#heapq.heappushpop
heappushpopはheappushとheappopを使うよりも早いらしい

### 調査を踏まえた実装
addについては速度を優先するため、シンプルな記述よりも速度を重視しました。
- val <= self.top_k_min_heap[0]のときにO(1)で操作できるようにする
- heappushpopを利用する

KthLargestというクラスを実務で実装する場合は標準ライブラリというかutilのような立ち位置になると想定しました。
色んな場所で使われることを想定すると、速度は速い方が良いと考えました（標準ライブラリも同様の考えで最適化がされていると思っている）。

```python
import heapq
class KthLargest:

    def __init__(self, k: int, nums: List[int]):
        self.k = k
        self.top_k_min_heap = heapq.nlargest(k, nums)
        self.top_k_min_heap.sort()

    def add(self, val: int) -> int:
        if len(self.top_k_min_heap) < self.k:
            heapq.heappush(self.top_k_min_heap, val)
            return self.top_k_min_heap[0]
        
        if val <= self.top_k_min_heap[0]:
            return self.top_k_min_heap[0]
        
        heapq.heappushpop(self.top_k_min_heap, val)
        return self.top_k_min_heap[0]

# Your KthLargest object will be instantiated and called as such:
# obj = KthLargest(k, nums)
# param_1 = obj.add(val)
```


こっちのほうがすっきりはするけど分かりづらいかな。
上に書いた例だと後半の節ほど重要度が上がるようになっていてわかりやすい。
```python
import heapq
class KthLargest:

    def __init__(self, k: int, nums: List[int]):
        self.k = k
        self.top_k_min_heap = sorted(heapq.nlargest(k, nums))

    def add(self, val: int) -> int:
        if len(self.top_k_min_heap) < self.k:
            heapq.heappush(self.top_k_min_heap, val)
        elif self.top_k_min_heap[0] < val:
            heapq.heappushpop(self.top_k_min_heap, val)
        
        return self.top_k_min_heap[0]


# Your KthLargest object will be instantiated and called as such:
# obj = KthLargest(k, nums)
# param_1 = obj.add(val)
```

## step3
```python
import heapq
class KthLargest:

    def __init__(self, k: int, nums: List[int]):
        self.k = k
        self.top_k_min_heap = sorted(heapq.nlargest(k, nums))

    def add(self, val: int) -> int:
        if len(self.top_k_min_heap) < self.k:
            heapq.heappush(self.top_k_min_heap, val)
            return self.top_k_min_heap[0]
        
        if val <= self.top_k_min_heap[0]:
            return self.top_k_min_heap[0]
        
        heapq.heappushpop(self.top_k_min_heap, val)
        return self.top_k_min_heap[0]


# Your KthLargest object will be instantiated and called as such:
# obj = KthLargest(k, nums)
# param_1 = obj.add(val)
```

## レビューコメントを反映したコード
対応内容
- __init__でヒープを作る際にaddを利用する
  - 理由：nlargestとソートを使った方法だと、一瞬ヒープかどうか迷わせてしまう。それよりは分かりやすい方法を選択した方が良いと判断した。
  - heapifyしてpopする方法もあったけど、addで作る方がより素直だと思いaddで作る方法を選択した。

```python
import heapq
class KthLargest:

    def __init__(self, k: int, nums: List[int]):
        self.k = k
        self.top_k_min_heap = []
        for num in nums:
            self.add(num)

    def add(self, val: int) -> int:
        if len(self.top_k_min_heap) < self.k:
            heapq.heappush(self.top_k_min_heap, val)
            return self.top_k_min_heap[0]
        
        if val <= self.top_k_min_heap[0]:
            return self.top_k_min_heap[0]
        
        heapq.heappushpop(self.top_k_min_heap, val)
        return self.top_k_min_heap[0]


# Your KthLargest object will be instantiated and called as such:
# obj = KthLargest(k, nums)
# param_1 = obj.add(val)
```

## レビューコメントを反映したコード2
### QuickSelect
#### アルゴリズムを調査
いただいたレビューコメント
https://github.com/garunitule/coding_practice/pull/8/files#r2072361537

#### 実装
クイックセレクトを利用するなら、nをもともとのnumsの長さ、mをadd回数とすれば、
- numsにadd: O(1)
- numsからk番目に大きい数をクイックセレクトによって取得：O(n + m)

なお、addを利用してヒープを初期化した場合、add1回あたりの時間計算量はO(logk)。

https://github.com/hayashi-ay/leetcode/pull/11/files
https://github.com/ichika0615/arai60/pull/7#discussion_r1874546511

メソッドは
- _quick_select：left, rightを変えながらpatitionを繰り返し、ある指定された順位の数を取得する
- _partition：pivotより小さい領域とpivot以上の領域にわけ、pivotのindexを返す
の2つに分けて実装する。

TLEするがクイックセレクトで動くコードを実装してみた。
今回の問題のケースで使うかどうかはさておき、「k番目の要素を取得する」という実現したいことを見たときに選択肢の一つとして浮かぶべきだったし実装出来ないのはまずいので勉強もかねて実装した。

```python
import random

class KthLargest:

    def __init__(self, k: int, nums: List[int]):
        self.k = k
        self.nums = nums

    def add(self, val: int) -> int:
        self.nums.append(val)
        return self._quick_select(0, len(self.nums) - 1, len(self.nums) - self.k)

    def _quick_select(self, left: int, right: int, k_smallest_idx: int) -> int:
        if left == right:
            return self.nums[left]
        
        while left < right:
            pivot_idx = random.randint(left, right)
            pivot_idx = self._partition(left, right, pivot_idx)

            if pivot_idx == k_smallest_idx:
                return self.nums[pivot_idx]
            elif pivot_idx < k_smallest_idx:
                left = pivot_idx + 1
            else:
                right = pivot_idx - 1
        
        return self.nums[left]

    def _partition(self, left: int, right: int, pivot_idx: int) -> int:
        pivot_val = self.nums[pivot_idx]
        self.nums[pivot_idx], self.nums[right] = self.nums[right], self.nums[pivot_idx]
        partition_idx = left

        for i in range(left, right):
            if self.nums[i] < pivot_val:
                self.nums[partition_idx], self.nums[i] = self.nums[i], self.nums[partition_idx]
                partition_idx += 1
        
        self.nums[partition_idx], self.nums[right] = self.nums[right], self.nums[partition_idx]
        return partition_idx


# Your KthLargest object will be instantiated and called as such:
# obj = KthLargest(k, nums)
# param_1 = obj.add(val)
```


### ソートされた状態での追加・削除を調査
#### アルゴリズムを調査
いただいたレビューコメント
https://github.com/garunitule/coding_practice/pull/8/files#r2072361948

内部実装は読んでいないが、APIの仕様書を見ると大まかな時間計算量は分かる
https://grantjenks.com/docs/sortedcontainers/sortedlist.html#sortedcontainers.SortedList.index

#### 実装
SortedListを使ったシンプルな実装

```python
from sortedcontainers import SortedList

class KthLargest:

    def __init__(self, k: int, nums: List[int]):
        self.k = k
        self.sorted_nums = SortedList(nums)

    def add(self, val: int) -> int:
        self.sorted_nums.add(val)
        return self.sorted_nums[len(self.sorted_nums) - self.k]
```

k個だけ保持するように改良する

```python
from sortedcontainers import SortedList

class KthLargest:

    def __init__(self, k: int, nums: List[int]):
        self.k = k
        self.top_k_sorted_vals = SortedList(nums).isslice()
        while self.k < len(self.top_k_sorted_vals):
            self.top_k_sorted_vals.pop(0)

    def add(self, val: int) -> int:
        self.top_k_sorted_vals.add(val)
        if len(self.top_k_sorted_vals) > self.k:
            self.top_k_sorted_vals.pop(0)
        return self.top_k_sorted_vals[0]

# Your KthLargest object will be instantiated and called as such:
# obj = KthLargest(k, nums)
# param_1 = obj.add(val)
```