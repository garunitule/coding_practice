# 進め方
- step1: 5分考えて分からなかったら答えを見る。答えを理解したら、答えを隠して書く。筆が進まず5分立ったら答えを見る。答えを送信して正解するまで。
- step2: コードを読みやすく整える。動くコードになったら終了。
- step3: 時間を計りながら書く。10分以内に3回連続でアクセプトされるまで。

# step1: 15分
## 方針を考える
nをnumsの長さとして、単純に計算すると、O(2 ** n)かかる
dp[i][j] = last_val
- i: i番目までの文字列
- j: 部分文字列の長さ
- val: 部分文字列の最後の値
のようにすればいいと思ったが、遷移式が複雑になってしまう。
j <= iかつ二つ目のインデックスがj, j - 1である全てを考える必要がある。だが、j - 1の場合とjの場合で保持すべき値が異なってしまう。
dp[i][j] = dp[i - 1][j - 1], dp[i - 1][j], dp[i - 2][j - 1], dp[i - 2][j - 1], ..., dp[j - 1][j - 1]

時間がかかってしまったので回答を見る
回答を見た
- 動的計画法
  - dp[i]: iで終わる最長増加部分列の長さ
  - 初期値: 全て1
  - 遷移: 全てのj < iに対して、nums[j] < nums[i]ならdp[i] = max(dp[i], dp[j] + 1)
  - 答え: max(dp)

## 実装
回答を見て実装した
```python
class Solution:
    def lengthOfLIS(self, nums: List[int]) -> int:
        n = len(nums)
        lengths_of_subsequence = [1] * n
        for i in range(n):
            for j in range(i):
                if nums[j] < nums[i]:
                    lengths_of_subsequence[i] = max(lengths_of_subsequence[i], lengths_of_subsequence[j] + 1)

        return max(lengths_of_subsequence)
```

## （必要に応じて）最適化
回答を見ると他の解法があるようだが、step2で検討する

# step2: 30分
## 他の人のPRやコメントを踏まえて実装
leetcodeの回答を見ると
- 貪欲法
- 貪欲法 + 2分探索

貪欲法による解法は腑に落ちきってないが、何となくは理解できたと思う
- sub: 各長さrのLISに対する末尾の最小値を保持
- 更新方法
  - nums[i] > sub[-1]の場合：長さlen(sub) + 1のLISが作れるので、末尾に追加する
  - nums[i] <= sub[-1]の場合：sub[j] <= nums[i] < sub[j + 1]を満たすjに対してsub[j]の値をnums[i]に更新する。更新処理途中のsubは末尾の最小値候補を保持しているので、より小さい候補があればその値で更新する必要があるため（更新することで後で登場する値を末尾に追加できるかもしれない、追加できるとLISの長さを増やせる）。

貪欲法 + 2分探索はnums[i] <= sub[-1]のときの更新方法を2分探索にして時間計算量を改善する案

貪欲法で実装した動かないコード
nums[0] > nums[1]の場合というか、minimum_list_end_valueの長さが2以上じゃないと正しく動かない
leetcodeの解法にした方が良い
```python
class Solution:
    def lengthOfLIS(self, nums: List[int]) -> int:
        if not nums:
            return 0

        minimum_lis_end_value = [nums[0]]
        for i in range(1, len(nums)):
            if minimum_lis_end_value[-1] < nums[i]:
                minimum_lis_end_value.append(nums[i])
                continue
            for j in range(len(minimum_lis_end_value) - 1):
                if minimum_lis_end_value[j] <= nums[i] and nums[i] < minimum_lis_end_value[j + 1]:
                    minimum_lis_end_value[j + 1] = nums[i]
        
        return len(minimum_lis_end_value)
```

上記を踏まえて動くようにしたコード
最初にminimum_lis_end_value[j] >= nums[i]になったjについて、minimum_lis_end_value[j]をnums[i]で更新する
```python
class Solution:
    def lengthOfLIS(self, nums: List[int]) -> int:
        if not nums:
            return 0

        minimum_lis_end_value = [nums[0]]
        for i in range(1, len(nums)):
            if minimum_lis_end_value[-1] < nums[i]:
                minimum_lis_end_value.append(nums[i])
                continue
            j = 0
            while minimum_lis_end_value[j] < nums[i]:
                j += 1
            minimum_lis_end_value[j] = nums[i]
        
        return len(minimum_lis_end_value)
```

最後に、これを2分探索で実現する
bisectモジュールを活用して実装する
```python
from bisect import bisect_left


class Solution:
    def lengthOfLIS(self, nums: List[int]) -> int:
        minimum_lis_end_values = []
        for num in nums:
            i = bisect_left(minimum_lis_end_values, num)
            if i == len(minimum_lis_end_values):
                minimum_lis_end_values.append(num)
                continue
            minimum_lis_end_values[i] = num

        return len(minimum_lis_end_values)  
```

https://github.com/tokuhirat/LeetCode/pull/31/files
DPの場合の変数名をtableにし、コメントで内容を補足している
下記のコメントもらってるように悩ましいところ。変数名が長くなりすぎるとそれはそれで読みづらいと思うので。
https://github.com/tokuhirat/LeetCode/pull/31/files#r2128692081

https://github.com/ryosuketc/leetcode_arai60/pull/44/files
DPの場合のlongest_subsequence_lengthsは分かりやすい


# step3: 15分
※間違えがあればn回目を増やす

動的計画法も貪欲法 + 2分探索もやる
個人的には動的計画法のほうが分かりやすい
貪欲法だと最後にlen(lis_tails)を返しているのが、ぱっと見で分かりづらいと思う（コード読んで意図把握できるならいいが、それに頼るのも良くないと思う）

## 動的計画法
### 1回目
```python
class Solution:
    def lengthOfLIS(self, nums: List[int]) -> int:
        n = len(nums)
        longest_subsequence_lengths = [1] * n
        for i in range(n):
            for j in range(i):
                if nums[j] < nums[i]:
                    longest_subsequence_lengths[i] = max(longest_subsequence_lengths[i], longest_subsequence_lengths[j] + 1)
    
        return max(longest_subsequence_lengths)
```

### 2回目
```python
class Solution:
    def lengthOfLIS(self, nums: List[int]) -> int:
        n = len(nums)
        longest_subsequence_lengths = [1] * n
        for i in range(n):
            for j in range(i):
                if nums[j] < nums[i]:
                    longest_subsequence_lengths[i] = max(longest_subsequence_lengths[i], longest_subsequence_lengths[j] + 1)
        
        return max(longest_subsequence_lengths)
```

### 3回目
```python
class Solution:
    def lengthOfLIS(self, nums: List[int]) -> int:
        n = len(nums)
        longest_subsequence_lengths = [1] * n
        for i in range(n):
            for j in range(i):
                if nums[j] < nums[i]:
                    longest_subsequence_lengths[i] = max(longest_subsequence_lengths[i], longest_subsequence_lengths[j] + 1)

        return max(longest_subsequence_lengths)
```

## 貪欲法 + 2分探索
### 1回目
```python
from bisect import bisect_left


class Solution:
    def lengthOfLIS(self, nums: List[int]) -> int:
        lis_tails = [nums[0]]
        for i in range(1, len(nums)):
            lower_bound_index = bisect_left(lis_tails, nums[i])
            if lower_bound_index == len(lis_tails):
                lis_tails.append(nums[i])
                continue
            lis_tails[lower_bound_index] = nums[i]

        return len(lis_tails)
```


### 2回目
```python
from bisect import bisect_left


class Solution:
    def lengthOfLIS(self, nums: List[int]) -> int:
        if not nums:
            return 0
        
        lis_tails = [nums[0]]
        for i in range(1, len(nums)):
            lower_bound_index = bisect_left(lis_tails, nums[i])
            if lower_bound_index == len(lis_tails):
                lis_tails.append(nums[i])
                continue
            lis_tails[lower_bound_index] = nums[i] 

        return len(lis_tails)
```

### 3回目
```python
from bisect import bisect_left


class Solution:
    def lengthOfLIS(self, nums: List[int]) -> int:
        if not nums:
            return 0
        
        lis_tails = [nums[0]]
        for i in range(1, len(nums)):
            lower_bound_index = bisect_left(lis_tails, nums[i])
            if lower_bound_index == len(lis_tails):
                lis_tails.append(nums[i])
                continue
            lis_tails[lower_bound_index] = nums[i]
        
        return len(lis_tails)
```