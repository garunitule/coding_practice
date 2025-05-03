## 取り組み方
step1: 5分考えて分からなかったら答えを見る。答えを理解したら、答えを隠して書く。筆が進まず5分立ったら答えを見る。答えを送信して正解するまで。
step2: コードを読みやすく整える。動くコードになったら終了。
step3: 時間を計りながら書く。10分以内に3回連続でアクセプトされるまで。

## step1
key: nums[i], value: frequency
となるdictを作る。
そのあと(frequency, nums[i])のtupleを作る。
ソートして上からk個取得する。
O(nlogn)となる解法。
問題下部のfollow upを見るとO(nlogn)より良い解法があるらしい。

dict, tuple作るところまでは一緒で、ヒープで保存すればよいのか。
そうすればO(nlogk)になるはず。
nlargestで頻度が大きい値をk個取得してかえせばよさそう。
ここまで7分くらい。

動くコードを実装できた。5分くらい。
```python
from collections import defaultdict
import heapq

class Solution:
    def topKFrequent(self, nums: List[int], k: int) -> List[int]:
        if k == len(nums):
            return nums

        num_to_freq = defaultdict(int)
        for num in nums:
            num_to_freq[num] += 1
        
        num_freq_list = [(num_to_freq[num], num) for num in num_to_freq]
        top_k_num_freq_list = heapq.nlargest(k, num_freq_list)
        return [num for freq, num in top_k_num_freq_list]
```

## step2
### step1を終えて改善できそうな部分
- dictの繰り返し処理をするときに、もっと分かりやすく書けないか？
- そういえばCounterとかがあった気がする？
- 変数の命名がわかりづらい？

>dictの繰り返し処理をするときに、もっと分かりやすく書けないか？
https://docs.python.org/ja/3.13/library/heapq.html#heapq.nlargest
nlargestでiterableにCounter(nums), keyにlambda x:x[1]を指定すれば結構シンプルに書けそう
書けた
前の問題でnlargestの実装を読んだ感じ、O(nlogk)になるはず。
```python
from collections import Counter
import heapq
class Solution:
    def topKFrequent(self, nums: List[int], k: int) -> List[int]:
        freq = Counter(nums)
        return heapq.nlargest(k, freq, key=freq.get)
```


>そういえばCounterとかがあった気がする？
https://docs.python.org/ja/3.13/library/collections.html#collections.Counter
Counterを使えば要素ごとの頻度は出せる
most_commonsを使えば最も多いn要素を順に並べたリストを返せる
今回の問題ならmost_commonsだけで解決する
さっと書けるとはいえ、どれくらいの計算量なのかは実装を読んで理解しておこう。中身がわからないものを使うのは怖い。

```python
from collections import Counter
class Solution:
    def topKFrequent(self, nums: List[int], k: int) -> List[int]:
        freq = Counter(nums)
        return [num for num, _ in freq.most_common(k)]
```

>変数の命名がわかりづらい？
上記2方針にすれば変数は減って変数名を考えなくてよい

### 他のPRを見る
https://github.com/nittoco/leetcode/pull/48/files
たしかにsortedでもできるな
most_commonの実装も調べてある


https://github.com/python/cpython/blob/3d4ac1a2c2b610f35a9e164878d67185e4a3546f/Lib/collections/__init__.py#L625

nが与えられていない場合はsortedを、そうじゃない場合はheapq.nlargestを利用している。
_itemgetter(1)がわからない。
operator.itemgetterを利用している。
https://docs.python.org/ja/3.13/library/operator.html#operator.itemgetter
単純にself.items()で取得した際に1番目を取得してるってことか。

## step3
```python
from collections import Counter
class Solution:
    def topKFrequent(self, nums: List[int], k: int) -> List[int]:
        freq = Counter(nums)
        return [num for num, _ in freq.most_common(k)]
```