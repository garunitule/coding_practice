# 進め方
- step1: 5分考えて分からなかったら答えを見る。答えを理解したら、答えを隠して書く。筆が進まず5分立ったら答えを見る。答えを送信して正解するまで。
- step2: コードを読みやすく整える。動くコードになったら終了。
- step3: 時間を計りながら書く。10分以内に3回連続でアクセプトされるまで。

# step1: 15分
## 方針を考える
ぱっと見累積和を使えばO(n**2)で計算出来そう
動的計画法で解く場合はdp[i][j]をiからjまでの和とすれば、O(n**2)で計算できそう
動的計画法のほうがオーバーヘッドが大きそうなので、累積和で実装してみる

実装できたけどTLEした。follow upを見たらO(n)の解法があるらしいので、少し考えてみる。
```python
class Solution:
    def maxSubArray(self, nums: List[int]) -> int:
        if not nums:
            return 0

        prefix_sum = [nums[0]]
        n = len(nums)
        for i in range(1, n):
            prefix_sum.append(prefix_sum[i - 1] + nums[i])
        
        max_sub_array_sum = max(prefix_sum)
        for i in range(1, n):
            for j in range(i, n):
                max_sub_array_sum = max(max_sub_array_sum, prefix_sum[j] - prefix_sum[i - 1])

        return max_sub_array_sum
```

累積和の最大値のみ保持しておいて、max(最大値, 最大値 - nums[i])を繰り返せばできそう？
いや、できなそう。
最初は全ての累積和の中の最大値を取ればいいけど、次はprefix_sum[0]以外の最大値を考えなきゃいけないのにそれができない

動的計画法でdp[i][j]から改善する方法も思いつかない

最初に累積和を計算しておけば、うまいこと次回以降計算を省略できそうな感覚はあるが、具体的な方法が思いつかない
ので回答を見る

# step2: 30分
## 他の人のPRやコメントを踏まえて実装
leetcodeの回答を見た
- current_subarray_sum: 現在の部分配列
- max_subarray_sum: 現時点での部分配列の最大値

current_subarray_sumは負になったらnumsから再開する。負になるなら含めていても意味がないため。
max_subarray_sumで最大値を保持しているので、current_subarray_sum[i:j]が真の最大値だったとして、current_subarray_sumの先頭が変わらずにcurrent_subarray_sum[i:]まで計算して終わったとしても、真の最大値を保持している

```python
class Solution:
    def maxSubArray(self, nums: List[int]) -> int:
        if not nums:
            return 0

        subarray_sum = nums[0]
        max_subarray_sum = nums[0]
        for i in range(1, len(nums)):
            subarray_sum = max(nums[i], subarray_sum + nums[i])
            max_subarray_sum = max(max_subarray_sum, subarray_sum)
        
        return max_subarray_sum
```

分割統治の解法も載ってる。下記の三つにパターン分けして、それぞれの最大値を取る
- midを含まない、左半分の中で作れる部分配列の和の最大値
- midを含む部分配列の和の最大値
- midを含まない、右半分の中で作れる部分配列の和の最大値
今回の問題だと分割方法がやや無理やりな感はあるけど、分割統治自体はちゃんと使えるようになった方が良さそう

```python
class Solution:
    def maxSubArray(self, nums: List[int]) -> int:
        def find_max_crossing_mid_sum(nums: List[int], left: int, mid: int, right: int) -> int:
            max_left_half_sum = 0
            left_sum = 0
            for i in range(mid - 1, left - 1, -1):
                left_sum += nums[i]
                max_left_half_sum = max(max_left_half_sum, left_sum)
            
            max_right_half_sum = 0
            right_sum = 0
            for i in range(mid + 1, right + 1):
                right_sum += nums[i]
                max_right_half_sum = max(max_right_half_sum, right_sum)
            
            return max_left_half_sum + nums[mid] + max_right_half_sum
            

        def find_max_sub_array_sum(nums: List[int], left: int, right: int) -> int:
            if left > right:
                return float('-inf')

            mid = (left + right) // 2
            max_crossing_mid_sum = find_max_crossing_mid_sum(nums, left, mid, right)
            max_left_half_sum = find_max_sub_array_sum(nums, left, mid - 1)
            max_right_half_sum = find_max_sub_array_sum(nums, mid + 1, right)

            return max(max_left_half_sum, max_crossing_mid_sum, max_right_half_sum)

        return find_max_sub_array_sum(nums, 0, len(nums) - 1)
```

# step3: 15分
※間違えがあればn回目を増やす

部分配列の和が負になったらリセットする方式で実装する

## 1回目
```python
class Solution:
    def maxSubArray(self, nums: List[int]) -> int:
        if not nums:
            return 0

        sub_array_sum = nums[0]
        max_sub_array_sum = nums[0]
        for i in range(1, len(nums)):
            sub_array_sum = max(nums[i], sub_array_sum + nums[i])
            max_sub_array_sum = max(max_sub_array_sum, sub_array_sum)

        return max_sub_array_sum
```

## 2回目
```python
class Solution:
    def maxSubArray(self, nums: List[int]) -> int:
        if not nums:
            return 0
        
        sub_array_sum = nums[0]
        max_sub_array_sum = nums[0]
        for i in range(1, len(nums)):
            sub_array_sum = max(nums[i], sub_array_sum + nums[i])
            max_sub_array_sum = max(max_sub_array_sum, sub_array_sum)

        return max_sub_array_sum
```

## 3回目
```python
class Solution:
    def maxSubArray(self, nums: List[int]) -> int:
        if not nums:
            return 0
        
        sub_array_sum = nums[0]
        max_sub_array_sum = nums[0]
        for i in range(1, len(nums)):
            sub_array_sum = max(nums[i], sub_array_sum + nums[i])
            max_sub_array_sum = max(max_sub_array_sum, sub_array_sum)

        return max_sub_array_sum
```