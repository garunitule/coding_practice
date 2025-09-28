# 進め方
- step1: 5分考えて分からなかったら答えを見る。答えを理解したら、答えを隠して書く。筆が進まず5分立ったら答えを見る。答えを送信して正解するまで。
- step2: コードを読みやすく整える。動くコードになったら終了。
- step3: 時間を計りながら書く。10分以内に3回連続でアクセプトされるまで。

# step1: 15分
## 方針を考える
一つ飛ばしと二つ飛ばしの遷移を考える。
更新時は最大値を取ればよさそう

max_amounts[i]: i番目の家のお金を盗むときの最大の合計強盗額
- 初期値
  - max_amount[0]: nums[0]
  - max_amount[1]: nums[1]
- 遷移
  - max_amount[i]: max(max_amount[i - 2], max_amount[i - 3]) + nums[i]
- 返り値
  - max(max_amount): 最後の家のお金を盗むときが最大とは限らないので

この方法だと
- 時間計算量: O(len(nums))
- 空間計算量: O(len(nums))
で解ける

これ空間計算量ももっと減らせそう
保持する必要があるのは、i - 3, i - 2, i - 1だけ
遷移の計算の観点だと上記があればok
最終的に返り値を返す際も上記があればok
0 <= nums[i]という制約があるので

## 実装
まず時間計算量および空間計算量がO(len(nums))の場合の実装
```python
class Solution:
    def rob(self, nums: List[int]) -> int:
        if not nums:
            return 0
        if len(nums) == 1:
            return nums[0]
        
        num_houses = len(nums)
        max_amounts = [0] * num_houses
        max_amounts[0] = nums[0]
        max_amounts[1] = nums[1]
        for i in range(2, num_houses):
            max_amounts[i] = max_amounts[i - 2]
            if 2 < i:
                max_amounts[i] = max(max_amounts[i], max_amounts[i - 3])
            max_amounts[i] += nums[i]

        return max(max_amounts)
```

次にiに対してi - 3, i - 2, i - 1を保持するやり方
これを実装できるとO(1)にできる
Listで保持するか迷ったけど、変数で実装した
```python
class Solution:
    def rob(self, nums: List[int]) -> int:
        if not nums:
            return 0
        if len(nums) == 1:
            return nums[0]
        
        num_houses = len(nums)
        max_robbed_amount_one_house_ago = nums[1]
        max_robbed_amount_two_houses_ago = nums[0]
        max_robbed_amount_three_houses_ago = nums[0]
        for i in range(2, num_houses):
            robbed_amount = max_robbed_amount_two_houses_ago
            if 2 < i:
                robbed_amount = max(robbed_amount, max_robbed_amount_three_houses_ago)
            robbed_amount += nums[i]
            max_robbed_amount_three_houses_ago = max_robbed_amount_two_houses_ago
            max_robbed_amount_two_houses_ago = max_robbed_amount_one_house_ago
            max_robbed_amount_one_house_ago = robbed_amount
        
        return max(max_robbed_amount_three_houses_ago, max_robbed_amount_two_houses_ago, max_robbed_amount_one_house_ago)
            
```
# step2: 30分
## 他の人のPRやコメントを踏まえて実装
leetcodeの回答を見ると
- 再帰
- DP
- DPの最適化（空間計算量をO(1)にする）
がある

そもそもdpの置き方が違う。
まず後ろから遷移しているという点。これはどちらでも良い気がしている。問題にとって考えやすい、シンプルにしやすい方を選択肢ればいいと思う。
次に「家iまでを考慮した強盗額の最大値」を保持している点（このとき家iを強盗するとは限らない）
だから、
max_robbed_amount[i] = max(max_robbed_amount[i + 1], max_robbed_amount[i + 2] + nums[i])
自分の実装は「家iを強盗したときの強盗額の最大値」を保持してるので、2つ前と3つ前を保持する必要があったうえに最後に最大値を返す必要があった
「家iまで」と設定した方が、保持する変数も少ないし意味も分かりやすいし最後に返す値も最後(または最初)の要素を返すだけなので分かりやすいと思った

https://github.com/h1rosaka/arai60/pull/37/files
やっぱり
max_robbed_amount[i] = max(max_robbed_amount[i - 1], max_robbed_amount[i + 2] + nums[i])
のほうが分かりやすい

https://github.com/shintaro1993/arai60/pull/39/files
max_two_steps_beforeのように、amountが不要でもhouse -> stepでも分かりやすいか
max_money_robbed_previous, max_money_skipped_previourもかなり元の意図に近くて分かりやすそう

# step3: 15分
※間違えがあればn回目を増やす

## 1回目
```python
class Solution:
    def rob(self, nums: List[int]) -> int:
        if not nums:
            return 0
        if len(nums) <= 2:
            return max(nums)
        
        max_one_step_ago = max(nums[:2])
        max_two_step_ago = nums[0]
        for i in range(2, len(nums)):
            robbed_amount = max(max_one_step_ago, max_two_step_ago + nums[i])
            max_two_step_ago = max_one_step_ago
            max_one_step_ago = robbed_amount

        return max_one_step_ago
```

## 2回目
```python
class Solution:
    def rob(self, nums: List[int]) -> int:
        if not nums:
            return 0
        if len(nums) <= 2:
            return max(nums)
        
        max_one_step_ago = max(nums[:2])
        max_two_step_ago = nums[0]
        for i in range(2, len(nums)):
            robbed_amount = max(max_one_step_ago, max_two_step_ago + nums[i])
            max_two_step_ago = max_one_step_ago
            max_one_step_ago = robbed_amount

        return max_one_step_ago
```

## 3回目
```python
class Solution:
    def rob(self, nums: List[int]) -> int:
        if not nums:
            return 0
        if len(nums) <= 2:
            return max(nums)
        
        max_one_step_ago = max(nums[:2])
        max_two_step_ago = nums[0]
        for i in range(2, len(nums)):
            robbed_amount = max(max_one_step_ago, max_two_step_ago + nums[i])
            max_two_step_ago = max_one_step_ago
            max_one_step_ago = robbed_amount
        
        return max_one_step_ago
```