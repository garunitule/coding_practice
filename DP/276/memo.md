# 進め方
- step1: 5分考えて分からなかったら答えを見る。答えを理解したら、答えを隠して書く。筆が進まず5分立ったら答えを見る。答えを送信して正解するまで。
- step2: コードを読みやすく整える。動くコードになったら終了。
- step3: 時間を計りながら書く。10分以内に3回連続でアクセプトされるまで。

# step1: 15分
## 方針を考える
愚直にやると少なくともk ** nはかかりそう
dp[i][j]（i: 塗った支柱のindex, j: 直近2本の色が同じかどうか）と置けば、
- dp[0][T] = 0
- dp[0][F] = k
- dp[i][T] = dp[i - 1][F]
- dp[i][F] = (k - 1) * (dp[i - 1][T] + dp[i - 1][F])
と初期値や遷移規則を書けるので、最後にdp[n - 1][T] + dp[n - 1][F]を返せばよい

最初は下記のように考えていたが、直近の2本の色が同じかどうかだけが必要だと分かったので保持するのやめた
- dp[i][j][k]（i: 塗った支柱のindex, j: 塗った色, k: 直近の2本の色が同じかどうか）

## 実装
```python
class Solution:
    def numWays(self, n: int, k: int) -> int:
        dp = [[0 for _ in range(2)] for _ in range(n)]
        dp[0][0] = k
        for i in range(1, n):
            dp[i][1] = dp[i - 1][0]
            dp[i][0] = (k - 1) * (dp[i - 1][0] + dp[i - 1][1])

        return dp[n - 1][0] + dp[n - 1][1]
```

# step2: 30分
## 時間の関係でstep1で書けなかった迷いや判断を書く
変数名がdpより分かりやすいものがありそうだが思いつかない
他の人のコードを見てみる

## 他の人のPRやコメントを踏まえて実装
leetcodeの回答を見る。
totalWays = (k - 1) * totalWays
で更新できる。たしかに自分が書いた遷移式もまとめられるな。

下記の3パターンの書き方がある
- トップダウン
- ボトムアップ
- ボトムアップ + Constant Space

空間計算量の観点だとConstant Spaceが良さそう
```python
class Solution:
    def numWays(self, n: int, k: int) -> int:
        if n == 1:
            return k
        
        second_last_num_ways = k
        last_num_ways = k * k
        result = last_num_ways
        for i in range(3, n + 1):
            result = (k - 1) * (last_num_ways + second_last_num_ways) 
            second_last_num_ways = last_num_ways
            last_num_ways = result
        return result
```

トップダウンも書いてみる
```python
from functools import cache


class Solution:
    def numWays(self, n: int, k: int) -> int:
        @cache
        def total_ways(i: int) -> int:
            if i == 1:
                return k
            if i == 2:
                return k * k
            
            return (k - 1) * (total_ways(i - 1) + total_ways(i - 2)) 

        
        return total_ways(n)
```

https://github.com/ryosuketc/leetcode_arai60/pull/43/files
total_waysという変数で保持している

# step3: 15分
今回問題の特性上、保持するのは直前の2つだけでいいので、2つだけ保持する実装を選択した

## 1回目
```python
class Solution:
    def numWays(self, n: int, k: int) -> int:
        if n == 1:
            return k
        if n == 2:
            return k * k
        
        second_last_num_ways = k
        last_num_ways = k * k
        total_ways = second_last_num_ways
        for i in range(3, n + 1):
            total_ways = (k - 1) * (second_last_num_ways + last_num_ways)
            second_last_num_ways = last_num_ways
            last_num_ways = total_ways
        return total_ways
```

## 2回目
```python
class Solution:
    def numWays(self, n: int, k: int) -> int:
        if n == 1:
            return k
        if n == 2:
            return k * k
        
        second_last_num_ways = k
        last_num_ways = k * k
        total_ways = last_num_ways
        for i in range(3, n + 1):
            total_ways = (k - 1) * (second_last_num_ways + last_num_ways)
            second_last_num_ways = last_num_ways
            last_num_ways = total_ways
        return total_ways
```

## 3回目
```python
class Solution:
    def numWays(self, n: int, k: int) -> int:
        if n == 1:
            return k
        if n == 2:
            return k * k
        
        second_last_num_ways = k
        last_num_ways = k * k
        total_ways = last_num_ways
        for i in range(3, n + 1):
            total_ways = (k - 1) * (second_last_num_ways + last_num_ways)
            second_last_num_ways = last_num_ways
            last_num_ways = total_ways
        return total_ways
```