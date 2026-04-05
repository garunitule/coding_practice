# 進め方
- step1: 5分考えて分からなかったら答えを見る。答えを理解したら、答えを隠して書く。筆が進まず5分立ったら答えを見る。答えを送信して正解するまで。
- step2: コードを読みやすく整える。動くコードになったら終了。
- step3: 時間を計りながら書く。10分以内に3回連続でアクセプトされるまで。

# step1: 15分
## 方針を考える
この制約を守りながら実装する必要がある。
>The replacement must be in place and use only constant extra memory.

色々試してみて、
[1,2,3,4] -> 3,4を入れ替える。
[1,3,2,4] -> 2,4を入れ替える。
[1,2,4,3] -> 3,2を入れ替える
[1,4,3,2]　-> 1,2を入れ替えて、nums[1:]を昇順にソートする。
[2,4,3,1] -> 3を先頭に持ってきて、nums[1:]を昇順にソートする。
だと分かった。まとめると下記の2パターンに集約できそうと気づいた。
パターン1
- nums[-1]より小さい値かつ最大のindexを特定する。
- そのindexの一つ左とnums[-1]を入れ替えて、nums[index:]を昇順にソートする。
パターン2（[2,4,3,1]が該当）
- nums[0]の次に大きい値を特定する
- その値を先頭に持ってきて、nums[1:]を昇順にソートする。

## 実装

動かないコード。[3,2,1]の入力に対応できてない。
```python
class Solution:
    def nextPermutation(self, nums: List[int]) -> None:
        """
        Do not return anything, modify nums in-place instead.
        """
        left = len(nums) - 1
        right = len(nums) - 1
        while 0 <= left and nums[left] >= nums[right]:
            left -= 1
        
        if left == -1:
            head = nums[0]
            max_num = max(nums)
            if head == max_num:
                next_head = min(nums)
            else:
                next_head = max(nums)
                for num in nums:
                    if first < num and num < next_head:
                        next_head = num
            nums[0] = next_head
            nums[1:right+1] = sorted(nums[1:right+1])
            return
        
        nums[left], nums[right] = nums[right], nums[left]
        nums[left+1:right+1] = sorted(nums[left+1:right+1])
            
```

動かないコード。[5,4,7,5,3,2]に対して動かない。重複を考慮できていない。
```python
class Solution:
    def nextPermutation(self, nums: List[int]) -> None:
        """
        Do not return anything, modify nums in-place instead.
        """
        left = len(nums) - 1
        right = len(nums) - 1
        while 0 <= left and nums[left] >= nums[right]:
            left -= 1
        
        if left == -1:
            head = nums[0]
            max_index = 0
            for i in range(len(nums)):
                if nums[max_index] < nums[i]:
                    max_index = i

            next_head_index = max_index
            if head == nums[max_index]:
                for i in range(len(nums)):
                    if nums[i] < nums[next_head_index]:
                        next_head_index = i 
            else:
                for i in range(len(nums)):
                    if head < nums[i] and nums[i] < nums[next_head_index]:
                        next_head_index = i

            nums[0], nums[next_head_index] = nums[next_head_index], nums[0]
            nums[1:right+1] = sorted(nums[1:right+1])
            return
        
        nums[left], nums[right] = nums[right], nums[left]
        nums[left+1:right+1] = sorted(nums[left+1:right+1])
            
```

時間をかけているので解答を見る。

# step2: 30分
iを見つけた時点で右側が非増加になっていることは分かる。 
次にjを見つける。num[i] < num[i + 1]なので必ずjは見つかります。少なくともi+1は該当するので。
次にnums[i]とnums[j]を入れ替える。入れ替えた後もiより右側が非増加になっていることは成立する。なぜなら、jの位置はnums[i]より大きい値の中で最も右の位置のため。
- 非増加列の中に値の重複があっても、もともとのnums[i]と同じ値が非増加列になっても成立する
  - 例えば、nums[i] = 4, nums[i+1:]=[100, 99, ..., 5, 5, 5, 4, 4, 3]だとする。
  - この時jの位置はlen(nums) - 4
  - nums[i]とnums[j]を入れ替えても非増加列になっていることは成立する。
このあとはreverseすればよい。

命名はこのPRをさんこうにした。
https://github.com/mamo3gr/arai60/pull/53/changes

jについて良い命名があれば、pivotはdescending_startにしたかった。

```python
class Solution:
    def nextPermutation(self, nums: List[int]) -> None:
        """
        Do not return anything, modify nums in-place instead.
        """
        descending_start = self.find_descending_start(nums)
        pivot = descending_start - 1
        if pivot != -1:
            j = len(nums) - 1
            while nums[j] <= nums[pivot]:
                j -= 1
            nums[j], nums[pivot] = nums[pivot], nums[j]
        self.reverse(nums, descending_start)
    
    def find_descending_start(self, nums: List[int]) -> int:
        i = len(nums) - 2
        while 0 <= i and nums[i + 1] <= nums[i]:
            i -= 1
        return i + 1
    
    def reverse(self, nums: List[int], start: int) -> None:
        end = len(nums) - 1
        while start < end:
            nums[start], nums[end] = nums[end], nums[start]
            start += 1
            end -= 1
```

# step3: 15分
※間違えがあればn回目を増やす

## 1回目
```python
class Solution:
    def nextPermutation(self, nums: List[int]) -> None:
        """
        Do not return anything, modify nums in-place instead.
        """
        descending_start = self.find_descending_start(nums)
        pivot = descending_start - 1
        if pivot != -1:
            j = len(nums) - 1
            while nums[j] <= nums[pivot]:
                j -= 1
            nums[j], nums[pivot] = nums[pivot], nums[j]
        self.reverse(nums, descending_start)
    
    def find_descending_start(self, nums: List[int]) -> int:
        i = len(nums) - 2
        while 0 <= i and nums[i + 1] <= nums[i]:
            i -= 1
        return i + 1
    
    def reverse(self, nums: List[int], start: int) -> None:
        end = len(nums) - 1
        while start < end:
            nums[start], nums[end] = nums[end], nums[start]
            start += 1
            end -= 1
```

## 2回目
```python
class Solution:
    def nextPermutation(self, nums: List[int]) -> None:
        """
        Do not return anything, modify nums in-place instead.
        """
        descending_start = self.find_descending_start(nums)
        pivot = descending_start - 1
        if pivot != -1:
            j = len(nums) - 1
            while nums[j] <= nums[pivot]:
                j -= 1
            nums[j], nums[pivot] = nums[pivot], nums[j]
        self.reverse(nums, descending_start)
    
    def find_descending_start(self, nums: List[int]) -> int:
        i = len(nums) - 2
        while 0 <= i and nums[i + 1] <= nums[i]:
            i -= 1
        return i + 1
    
    def reverse(self, nums: List[int], start: int) -> None:
        end = len(nums) - 1
        while start < end:
            nums[start], nums[end] = nums[end], nums[start]
            start += 1
            end -= 1
```

## 3回目
```python
class Solution:
    def nextPermutation(self, nums: List[int]) -> None:
        """
        Do not return anything, modify nums in-place instead.
        """
        descending_start = self.find_descending_start(nums)
        pivot = descending_start - 1
        if pivot != -1:
            j = len(nums) - 1
            while nums[j] <= nums[pivot]:
                j -= 1
            nums[j], nums[pivot] = nums[pivot], nums[j]
        self.reverse(nums, descending_start)
    
    def find_descending_start(self, nums: List[int]) -> int:
        i = len(nums) - 2
        while 0 <= i and nums[i + 1] <= nums[i]:
            i -= 1
        return i + 1
    
    def reverse(self, nums: List[int], start: int) -> None:
        end = len(nums) - 1
        while start < end:
            nums[start], nums[end] = nums[end], nums[start]
            start += 1
            end -= 1
```