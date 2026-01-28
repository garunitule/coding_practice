# 進め方
- step1: 5分考えて分からなかったら答えを見る。答えを理解したら、答えを隠して書く。筆が進まず5分立ったら答えを見る。答えを送信して正解するまで。
- step2: コードを読みやすく整える。動くコードになったら終了。
- step3: 時間を計りながら書く。10分以内に3回連続でアクセプトされるまで。

# step1: 15分
- 1を含む: [1], [1, 2], [1, 3], [1, 2, 3]
- 1を含まず2を含む: [2], [2, 3]
- 1, 2を含まず3を含む: [3]
- 1, 2, 3を含まない: []

下から受け取ったsubsetsと先頭にnums[0]を加えたものを返すように実装した。
```python
class Solution:
    def subsets(self, nums: List[int]) -> List[List[int]]:
        if not nums:
            return [[]]
        
        child_subsets = self.subsets(nums[1:])
        result = child_subsets[:]
        for subset in child_subsets:
            result.append([nums[0]] + subset)

        return result
```

# step2: 30分
再帰を使わない実装。個人的には nums[0] + nums[1:]以降の各subsetとした方が分かりやすいがどうか。
```python
class Solution:
    def subsets(self, nums: List[int]) -> List[List[int]]:
        result = [[]]
        for num in nums:
            new_subsets = []
            for subset in result:
                new_subsets.append(subset + [num])
            for new_subset in new_subsets:
                result.append(new_subset)
        
        return result
```

バックトラッキングによる実装
```python
class Solution:
    def subsets(self, nums: List[int]) -> List[List[int]]:
        result = []
        def make_subsets(start: int, subset: List[int]):
            result.append(subset[:])
            for i in range(start, len(nums)):
                subset.append(nums[i])
                make_subsets(i + 1, subset)
                subset.pop()
        
        make_subsets(0, [])
        return result
```

bitを使った解法
```python
class Solution:
    def subsets(self, nums: List[int]) -> List[List[int]]:
        result = []
        nums_size = len(nums)
        for i in range(2 ** nums_size, 2 ** (nums_size + 1)):
            selection_bits = bin(i)[3:]
            subset = [nums[j] for j in range(nums_size) if selection_bits[j] == '1']
            result.append(subset)
        
        return result
```

https://github.com/h1rosaka/arai60/pull/52/files
stackから取り出して、現在の要素を含む含まないで積んでいく方法もある。

# step3: 15分
※間違えがあればn回目を増やす
バックトラッキングでやってみる。

## 1回目
```python
class Solution:
    def subsets(self, nums: List[int]) -> List[List[int]]:
        result = []
        def make_subsets(start: int, subset: List[int]):
            result.append(subset[:])
            for i in range(start, len(nums)):
                subset.append(nums[i])
                make_subsets(i + 1, subset)
                subset.pop()

        make_subsets(0, [])
        return result
```

## 2回目
```python
class Solution:
    def subsets(self, nums: List[int]) -> List[List[int]]:
        result = []
        def make_subsets(start: int, subset: List[int]):
            result.append(subset[:])
            for i in range(start, len(nums)):
                subset.append(nums[i])
                make_subsets(i + 1, subset)
                subset.pop()

        make_subsets(0, [])
        return result
```

## 3回目
```python
class Solution:
    def subsets(self, nums: List[int]) -> List[List[int]]:
        result = []
        def make_subsets(start: int, subset: List[int]):
            result.append(subset[:])
            for i in range(start, len(nums)):
                subset.append(nums[i])
                make_subsets(i + 1, subset)
                subset.pop()

        make_subsets(0, [])
        return result
```