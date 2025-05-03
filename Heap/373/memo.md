## 取り組み方
step1: 5分考えて分からなかったら答えを見る。答えを理解したら、答えを隠して書く。筆が進まず5分立ったら答えを見る。答えを送信して正解するまで。
step2: コードを読みやすく整える。動くコードになったら終了。
step3: 時間を計りながら書く。10分以内に3回連続でアクセプトされるまで。

## step1
単純にやるなら2重for loopで和の結果を保持しておいて小さい順にk個返せばよさそう。

考えてみる。
どんな場合でも(nums1[0], nums2[0])のペアは最小になる。
次は
(nums1[0], nums2[1]) か (nums1[1], nums2[0])
のいずれかだが計算してみないと分からない。

仮に(nums1[0], nums2[1])だった場合、次に最小となる候補は
(nums1[1], nums2[0]), (nums1[1], nums2[1]), (nums1[0], nums2[2])になる。

こんな感じでどんどん考える候補が増えていく。
再帰っぽいような気もするけど、最終的に和が小さい順にk個取る部分の実装イメージがない。

いや、ヒープでいけるか。
(sum, nums1[i], nums2[j])
をヒープでk個だけ保持しておけばよい。

ヒープを使った場合の時間計算量は
O(len(nums1) * len(nums2) * logk)
になるな。

この方法だと入力がnon-decreasing orderでソートされてることは使えないけど、いったん実装しよう。


TLEしてしまった。。
```python
import heapq

class Solution:
    def kSmallestPairs(self, nums1: List[int], nums2: List[int], k: int) -> List[List[int]]:
        min_heap = []
        for num1 in nums1:
            for num2 in nums2:
                if len(min_heap) < k:
                    heapq.heappush(min_heap, (-num1 - num2, num1, num2))
                    continue
                
                if -num1 - num2 <= min_heap[0][0]:
                    continue
                
                heapq.heappushpop(min_heap, (-num1 - num2, num1, num2))
        
        return [(num1, num2) for _, num1, num2 in min_heap]
```

回答をみよう。
発想は結構惜しかったっぽい。
全パターンで和を計算しないとダメだと思っていたけど、入力がnon-decreasing orderであれば候補になりうるペアは限定されるのでヒープとして管理しておいて、ヒープから取得したペアがk個たまるまで継続するという方法でよかったのか。

思いつけそうだったので悔しい。


TLEせずに通せた
```python
import heapq

class Solution:
    def kSmallestPairs(self, nums1: List[int], nums2: List[int], k: int) -> List[List[int]]:
        result = []
        min_heap = [(nums1[0] + nums2[0], 0, 0)]
        visited = set((0, 0))

        while len(result) < k:
            _, i, j = heapq.heappop(min_heap)
            result.append((nums1[i], nums2[j]))

            if i + 1 < len(nums1) and (i + 1, j) not in visited:
                heapq.heappush(min_heap, (nums1[i + 1] + nums2[j], i + 1, j))
                visited.add((i + 1, j))
            
            if j + 1 < len(nums2) and (i, j + 1) not in visited:
                heapq.heappush(min_heap, (nums1[i] + nums2[j + 1], i, j + 1))
                visited.add((i, j + 1))
        
        return result
```

## step2
### 他のPRを見る
https://github.com/shintaro1993/arai60/pull/14/files
nums1, nums2, kに想定外の入力が来た場合を想定している。
今回は問題の制約上不要だが、そもそも頭に浮かべてなかったのでそれは反省。
頭に浮かんだけど問題の制約上不要と判断することと、そもそも頭に浮かんでないことには差がある。
入出力やパフォーマンスや各種トレードオフを考えるためには、ちゃんとユースケースを想定しないといけない。解くだけにならないように注意。

https://discordapp.com/channels/1084280443945353267/1201211204547383386/1206515949579145216
このコメントも上の話に近い。
- heappopする際にmin_heapが空だった場合にどうするか
- len(num1s) * len(nums2) < k だった場合にどうするか？
  - 問題の制約上存在しないことになっているが、実装で何かしら挙動を担保した方が親切。まずstep1で書いたようなコードが書けるべきだが、こういったユースケースを想定した実装も必要。
- 目的は8,9割の専門家が想定する選択肢を想定したうえで選択したり同じ感覚になることなので、こういった面もちゃんと大事にしたい。

  https://github.com/SuperHotDogCat/coding-interview/pull/44/files
  最初にnums1の各要素ととnums2[0]のペアを作る方法。
  個人的には「(nums1[i], nums2[j])に増える候補は(nums1[i + 1], nums2[j]), (nums1[i], nums2[j + 1])の2つ」という考え方が肝だと思っているので、そっちの方が良いかなと思った。

  https://github.com/SuperHotDogCat/coding-interview/pull/44/files#r1976402009
  (i + 1, j)と(i, j + 1)の追加は似た操作なのでメソッドに切り出す選択肢もある。

  ### 修正したコード
  修正点
  - min_heap, visitedの処理をメソッドに切り出した
  - min_heapが空になった場合、その時点のresultを返すようにした
    - もしlen(nums1) * len(nums2) < kとなるkが与えられた場合、その時点までのresultは返した方が良いと考えた。
    - もちろん分かりやすいように例外を発生させる手もあるが、そこまでする程の状況じゃない気がするので、その時点までのresultを返す方針を選択した。
- nums1が空, nums2が空、kが0の場合に後続が何も必要ないので早期return
  - これはこのコードを読む他のエンジニア向けに加えた処理。処理中に発生するパターンの中で小さい節はなるべく早めに取り除いた方が後の処理で考えることが少なくなって理解しやすくなると思った。

```python
import heapq

class Solution:
    def kSmallestPairs(self, nums1: List[int], nums2: List[int], k: int) -> List[List[int]]:
        if not nums1 or not nums2 or k == 0:
            return []

        min_heap = []
        visited = set()

        def push_pairs(i: int, j: int):
            if i < len(nums1) and j < len(nums2) and (i, j) not in visited:
                heapq.heappush(min_heap, (nums1[i] + nums2[j], i, j))
                visited.add((i, j))

        push_pairs(0, 0)
        result = []

        while len(result) < k:
            if not min_heap:
                return result

            _, i, j = heapq.heappop(min_heap)
            result.append((nums1[i], nums2[j]))

            push_pairs(i + 1, j)
            push_pairs(i, j + 1)
        return result
```

## step3
```python
import heapq

class Solution:
    def kSmallestPairs(self, nums1: List[int], nums2: List[int], k: int) -> List[List[int]]:
        if not nums1 or not nums2 or k == 0:
            return []
        
        min_heap = []
        visited = set()

        def push_pairs(i, j):
            if i < len(nums1) and j < len(nums2) and (i, j) not in visited:
                heapq.heappush(min_heap, (nums1[i] + nums2[j], i, j))
                visited.add((i, j))
        
        push_pairs(0, 0)
        result = []

        while len(result) < k:
            if not min_heap:
                return result
            
            _, i, j = heapq.heappop(min_heap)
            result.append((nums1[i], nums2[j]))

            push_pairs(i + 1, j)
            push_pairs(i, j + 1)
        
        return result
```