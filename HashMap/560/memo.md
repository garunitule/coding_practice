# 進め方
- step1: 5分考えて分からなかったら答えを見る。答えを理解したら、答えを隠して書く。筆が進まず5分立ったら答えを見る。答えを送信して正解するまで。
- step2: コードを読みやすく整える。動くコードになったら終了。
- step3: 時間を計りながら書く。10分以内に3回連続でアクセプトされるまで。

# step1: 15分
## 方針を考える
各要素を先頭に部分配列を考えると、部分配列数は
n + (n - 1) + n - 2 + ... + 1
になる
各部分配列について和を求めるので
時間計算量はO(n ** 3)
になる

もっと筋の良い手はあるか考える

[1, 1, 1]の場合
(1)最初は下記の中でkに一致するものがあるか
- nums[0]
- nums[0] + nums[1]
- nums[0] + nums[1] + nums[2]

(2)次は下記の中でkに一致するものがあるか
- nums[1]
- nums[1] + nums[2]

(3)次は下記の中でkに一致するものがあるか
- nums[2]

これを毎回計算せずに行いたい。
(2)は(1)でk+nums[0]に一致するものがあるか、という問いと同じにできそう
最初に(1)で書いた和を求めて保存しておいて、k+nums[0], k+nums[0]+nums[1]というようにkを増やしていけばよさそう？

ただ順番の情報が消えている気がする

わからないので解答を見る

## 解答を見て考える
累積和を使うと時間計算量: O(n**2), 空間計算量: O(n)で解ける
累積和を使わなくても 0 <= start <= len(nums) - 1, start <= end <= len(nums) - 1の2重ループで、各startの中でsumを求めるようにすれば、時間計算量: O(n**2), 空間計算量: O(1)で解ける

素直に累積和を使うと下記のような2重ループになる
これは、startをはじめとする部分配列を順番に走査することに相当する
※一部の処理しか書いてないので動きません。イメージを伝えるためのコードです。
```python
(中略)
for start in range(len(nums)):
    for end in range(start + 1, len(nums) + 1):
(中略)
```

下記のようにハッシュマップを使うと1重ループになる。
これは、各ループで現在の要素numまでの部分配列を走査することに相当する
ただし、今までの累積和の登場回数をハッシュマップに格納しておくことで、各ループでは時間計算量O(1)で計算できる
※一部の処理しか書いてないので動きません。イメージを伝えるためのコードです。
```python
(中略)
prefix_sum_to_count = defaultdict(int)
prefix_sum = 0
count = 0

for num in nums:
    prefix_sum += num
    count += prefix_sum_to_count[prefix_sum - k]
    prefix_sum_to_count[prefix_sum] += 1
(中略)
```

両者ともに部分配列の和をP(i) - P(j)により計算しているのは一緒だが、ループの進め方が違う
前者はjをずらしながら計算している。jから始まる部分配列の和を計算している。
後者はiをずらしながら計算している。iで終わる部分配列の和を計算している。このとき、j < iとなるjを考えればよいので、今までに登場した累積和の回数をハッシュマップに保持しておけばO(1)で計算できるのが時間計算量観点のメリット。

### 解答を見た後に実装
累積和による実装。TLEする
```python
class Solution:
    def subarraySum(self, nums: List[int], k: int) -> int:
        # i番目の値: nums[i - 1]番目までの和
        prefix_sum = [0 for _ in range(len(nums) + 1)]
        for i, num in enumerate(nums):
            prefix_sum[i + 1] = prefix_sum[i] + num
        
        count = 0
        for start in range(len(nums)):
            for end in range(start + 1, len(nums) + 1):
                # nums[start]からnums[end - 1]までの和
                sub_array_sum = prefix_sum[end] - prefix_sum[start]
                if sub_array_sum == k:
                    count += 1
        return count
```

累積和を使わず、2重ループの中でsumを求める方法。TLEする
```python
class Solution:
    def subarraySum(self, nums: List[int], k: int) -> int:
        count = 0

        for start in range(len(nums)):
            culmulative_sum = 0
            for end in range(start, len(nums)):
                culmulative_sum += nums[end]
                if culmulative_sum == k:
                    count += 1
        return count
```

ハッシュマップを使う方法
```python
class Solution:
    def subarraySum(self, nums: List[int], k: int) -> int:
        count = 0
        current_prefix_sum = 0
        prefix_sum_to_count = defaultdict(int)
        prefix_sum_to_count[0] = 1

        for num in nums:
            current_prefix_sum += num
            count += prefix_sum_to_count[current_prefix_sum - k]
            prefix_sum_to_count[current_prefix_sum] += 1
        return count
```

# step2: 30分
## 他の人のPRやコメントを踏まえて実装

https://discord.com/channels/1084280443945353267/1303257587742933024/1322011962598625280
たしかに下記の箇所はループの中に入れ込める
```
prefix_sum_to_count[0] = 1
```
個人的にはほんの少し分かりづらいと感じる
ただ、たまに見る表現なので選択肢として把握しておく

```python
class Solution:
    def subarraySum(self, nums: List[int], k: int) -> int:
        count = 0
        current_prefix_sum = 0
        prefix_sum_to_count = defaultdict(int)

        for num in nums:
            prefix_sum_to_count[current_prefix_sum] += 1
            current_prefix_sum += num
            count += prefix_sum_to_count[current_prefix_sum - k]
        return count
```

# step3: 15分
※間違えがあればn回目を増やす

## 1回目
やっぱり`prefix_sum_to_count[current_prefix_sum] += 1`をループの中に入れた
「prefix_sum_to_count: 現在の要素より1つ前までの累積和の登場回数を保持する」と捉えた方が分かりやすいと判断した

```python
class Solution:
    def subarraySum(self, nums: List[int], k: int) -> int:
        count = 0
        current_prefix_sum = 0
        prefix_sum_to_count = defaultdict(int)

        for num in nums:
            prefix_sum_to_count[current_prefix_sum] += 1
            current_prefix_sum += num
            count += prefix_sum_to_count[current_prefix_sum - k]
        return count

```

## 2回目
```python
class Solution:
    def subarraySum(self, nums: List[int], k: int) -> int:
        count = 0
        current_prefix_sum = 0
        prefix_sum_to_count = defaultdict(int)

        for num in nums:
            prefix_sum_to_count[current_prefix_sum] += 1
            current_prefix_sum += num
            count += prefix_sum_to_count[current_prefix_sum - k]
        return count
```

## 3回目
leetcodeではcollectionsからdefaultdictをimportしなくても動くとはいえ、importしない癖はつけない方が良いと考え、明示的にimportするようにした

```python
from collections import defaultdict


class Solution:
    def subarraySum(self, nums: List[int], k: int) -> int:
        count = 0
        current_prefix_sum = 0
        prefix_sum_to_count = defaultdict(int)

        for num in nums:
            prefix_sum_to_count[current_prefix_sum] += 1
            current_prefix_sum += num
            count += prefix_sum_to_count[current_prefix_sum - k]
        return count
```