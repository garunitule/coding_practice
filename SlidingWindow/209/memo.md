# 進め方
- step1: 5分考えて分からなかったら答えを見る。答えを理解したら、答えを隠して書く。筆が進まず5分立ったら答えを見る。答えを送信して正解するまで。
- step2: コードを読みやすく整える。動くコードになったら終了。
- step3: 時間を計りながら書く。10分以内に3回連続でアクセプトされるまで。

# step1: 15分
TLEする実装。
O(n ** 3)
```python
from math import isinf

class Solution:
    def minSubArrayLen(self, target: int, nums: List[int]) -> int:
        min_length = float("inf")
        for i in range(len(nums)):
            for j in range(i, len(nums)):
                if target <= sum(nums[i:j+1]):
                    min_length = min(min_length, j - i + 1)

        if isinf(min_length):
            return 0
        return min_length
```

和の計算を毎回行っているので効率化したが、TLEする。
O(n ** 2)なので、10 ** 10程度ではタイムアウトする。

```python
from math import isinf

class Solution:
    def minSubArrayLen(self, target: int, nums: List[int]) -> int:
        min_length = float("inf")
        for i in range(len(nums)):
            sub_array_sum = 0
            for j in range(i, len(nums)):
                sub_array_sum += nums[j]
                if target <= sub_array_sum:
                    min_length = min(min_length, j - i + 1)
                    break

        if isinf(min_length):
            return 0
        return min_length
```

不変条件として常にtargetより小さいという条件でスライディングウィンドウしたらO(n)になる。

```python
from math import isinf


class Solution:
    def minSubArrayLen(self, target: int, nums: List[int]) -> int:
        start = end = 0
        min_length = float("inf")
        sub_array_sum = 0
        while end < len(nums):
            sub_array_sum += nums[end]
            while start <= end and target <= sub_array_sum:
                min_length = min(min_length, end - start + 1)
                sub_array_sum -= nums[start]
                start += 1
            end += 1

        if isinf(min_length):
            return 0
        return min_length
```

# step2: 30分
https://github.com/h1rosaka/arai60/pull/50/files
読んだ

# step3: 15分
※間違えがあればn回目を増やす

## 1回目
```python
import math


class Solution:
    def minSubArrayLen(self, target: int, nums: List[int]) -> int:
        start = end = 0
        sub_array_sum = 0
        min_length = float("inf")
        while end < len(nums):
            sub_array_sum += nums[end]
            while start <= end and target <= sub_array_sum:
                min_length = min(min_length, end - start + 1)
                sub_array_sum -= nums[start]
                start += 1
            end += 1

        if math.isinf(min_length):
            return 0
        return min_length
```

## 2回目
ループ開始時にendをインクリメントした方が自然だと考えfor文にした

```python
import math


class Solution:
    def minSubArrayLen(self, target: int, nums: List[int]) -> int:
        start = 0
        sub_array_sum = 0
        min_length = float("inf")
        for end in range(len(nums)):
            sub_array_sum += nums[end]
            while start <= end and target <= sub_array_sum:
                min_length = min(min_length, end - start + 1)
                sub_array_sum -= nums[start]
                start += 1
        
        if math.isinf(min_length):
            return 0
        return min_length
```

## 3回目
```python
import math


class Solution:
    def minSubArrayLen(self, target: int, nums: List[int]) -> int:
        start = 0
        sub_array_sum = 0
        min_length = float("inf")
        for end in range(len(nums)):
            sub_array_sum += nums[end]
            while start <= end and target <= sub_array_sum:
                min_length = min(min_length, end - start + 1)
                sub_array_sum -= nums[start]
                start += 1
        
        if math.isinf(min_length):
            return 0
        return min_length
```