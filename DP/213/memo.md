# 進め方
- step1: 5分考えて分からなかったら答えを見る。答えを理解したら、答えを隠して書く。筆が進まず5分立ったら答えを見る。答えを送信して正解するまで。
- step2: コードを読みやすく整える。動くコードになったら終了。
- step3: 時間を計りながら書く。10分以内に3回連続でアクセプトされるまで。

# step1: 15分
## 方針を考える
末尾を除いた配列と先頭を除いた配列の両方に分けてHouse Robberを適用すれば解けそう。
愚直にやるとO(2^n)かかるので計算量落とすには和をメモする必要があるが、和をメモすると盗んだ家の情報がなくなるのでメモした状態で先頭含んだかどうか判断するのは難しいと思った。

## 実装
最初に書いたコード。nums = [1]が通らない
```python
class Solution:
    def rob(self, nums: List[int]) -> int:
        def max_robbed_amount(nums: List[int]) -> int:
            if not nums:
                return 0

            max_one_step_ago = 0
            max_two_step_ago = 0
            max_amount = 0
            n = len(nums)
            for i in range(n):
                max_amount = max(max_one_step_ago, max_two_step_ago + nums[i])
                max_two_step_ago = max_one_step_ago
                max_one_step_ag0 = max_amount
            
            return max_amount
        
        return max(max_robbed_amount(nums[:-1]), max_robbed_amount(nums[1:]))
```

通るように修正した。len(nums) == 1の扱いを関数内に含めたかったが、分割部分がネックでできなかったので外だししている。
```python
class Solution:
    def rob(self, nums: List[int]) -> int:
        if not nums:
            return 0
        if len(nums) == 1:
            return nums[0]

        def max_robbed_amount(nums: List[int]) -> int:
            max_one_step_ago = 0
            max_two_step_ago = 0
            max_amount = 0
            n = len(nums)
            for i in range(n):
                max_amount = max(max_one_step_ago, max_two_step_ago + nums[i])
                max_two_step_ago = max_one_step_ago
                max_one_step_ago = max_amount
            
            return max_amount
        
        return max(max_robbed_amount(nums[:-1]), max_robbed_amount(nums[1:]))
```

# step2: 30分
## 他の人のPRやコメントを踏まえて実装
いくつか見たがstep1で書いたコードと大体同じだった

# step3: 15分
※間違えがあればn回目を増やす

## 1回目
```python
class Solution:
    def rob(self, nums: List[int]) -> int:
        if not nums:
            return 0
        if len(nums) == 1:
            return nums[0]

        def rob_linear(nums: List[int]) -> int:
            max_one_step_ago = 0
            max_two_step_ago = 0 
            max_amount = 0
            for num in nums:
                max_amount = max(max_one_step_ago, max_two_step_ago + num)
                max_two_step_ago = max_one_step_ago
                max_one_step_ago = max_amount

            return max_amount
        
        return max(rob_linear(nums[:-1]), rob_linear(nums[1:]))
```

## 2回目
```python
class Solution:
    def rob(self, nums: List[int]) -> int:
        if not nums:
            return 0
        if len(nums) == 1:
            return nums[0]
        
        def rob_linear(nums: List[int]) -> int:
            max_amount = 0
            max_one_step_ago = 0
            max_two_step_ago = 0
            for num in nums:
                max_amount = max(max_one_step_ago, max_two_step_ago + num)
                max_two_step_ago = max_one_step_ago
                max_one_step_ago = max_amount
            
            return max_amount
        
        return max(rob_linear(nums[:-1]), rob_linear(nums[1:]))
```

## 3回目
```python
class Solution:
    def rob(self, nums: List[int]) -> int:
        if not nums:
            return 0
        if len(nums) == 1:
            return nums[0]
        
        def rob_linear(nums: List[int]) -> int:
            max_amount = 0
            max_one_step_ago = 0
            max_two_step_ago = 0
            for num in nums:
                max_amount = max(max_one_step_ago, max_two_step_ago + num)
                max_two_step_ago = max_one_step_ago
                max_one_step_ago = max_amount
            
            return max_amount
        
        return max(rob_linear(nums[:-1]), rob_linear(nums[1:]))
```