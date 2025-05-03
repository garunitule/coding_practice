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