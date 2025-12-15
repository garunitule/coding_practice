# 進め方
- step1: 5分考えて分からなかったら答えを見る。答えを理解したら、答えを隠して書く。筆が進まず5分立ったら答えを見る。答えを送信して正解するまで。
- step2: コードを読みやすく整える。動くコードになったら終了。
- step3: 時間を計りながら書く。10分以内に3回連続でアクセプトされるまで。

# step1: 15分
## 方針を考える
問題は分かりやすい。
額が大きいコインから使えばよいと思ったけど、そうとも限らない。
例えば[1, 5, 8], 10の場合、8,1,1よりも5,5の方が枚数少ない。
amountから各コインが使える最大枚数は決まるので、全パターン試せば最小枚数を求められる。
ただ、額が大きいと時間がかかりすぎるし無駄が多い。
例えば、1,1,1,1,1,5だとしたら5,5にまとめられる。

[1, 5, 8], 10の例で考える。
dp[i]: coins[i]までを使った最小枚数
とするなら、
dp[0] = 10
dp[1] = 2（coins[1]はcoins[0]より5倍大きいので）
dp[2] = 3（coins[2]はcoins[0]より8倍大きいので、coins[2]はcoins[1]の倍数じゃないのでdp[1]は使えない）
で、最後にmin(dp)で返すで出来そう。

遷移をもっと詰める。
[1, 5, 8], 13の場合だと、[5, 8]が最小枚数だが、上述のように倍数関係しか考えないと[5, 8]は導けない。
このパターンに対応するなら大きいコインから使う実装にしたくなる。
というか倍数というよりは、今までのコインからそのコインの値を作れるかどうかか。

dp[i]: coins[i]までを使った最小枚数のパターン
として、[1, 5, 8], 13のパターンを考えるなら、
dp[0]: {1: 13}
dp[1]: {1: 3, 5: 2}（coins[1] = 5 * coins[0]なので）
dp[2]: {5: 1, 8: 1}（coins[2] = coins[1] + 3 * coins[0]なので）
で最小枚数のパターンを返す、で出来そう。

[2, 5, 11], 13のように前のコインから構築できないパターンもある。
dp[0]: パターンなし
dp[1]: {2: 4, 5: 1}（大きい方から使って計算するしかない）
dp[2]: {2: 1, 11: 1}（大きい方から使って計算するしかない）

結構複雑になるな。ここまで30分かかっている。もっとシンプルな解法があるか知りたいので解答を見る。

# step2: 30分
ブルートフォース
最悪ケースでは、amount分だけ各コインで枚数の枝分かれが発生するので、O(amount ** n)
空間計算量はO(len(coins))。for文に入ったあらすぐget_min_numが呼ばれるので
```python
class Solution:
    def coinChange(self, coins: List[int], amount: int) -> int:
        num_coin_types = len(coins)
        def get_min_num(index: int, amount: int) -> int:
            if amount == 0:
                return 0
            if num_coin_types <= index or amount < 0:
                return -1

            min_num = float('inf')
            for num_coin in range(amount // coins[index] + 1):
                min_num_from_next = get_min_num(index + 1, amount - num_coin * coins[index])
                if min_num_from_next == -1:
                    continue
                min_num = min(min_num, num_coin + min_num_from_next)
            if min_num == float('inf'):
                return -1
            return min_num
        
        return get_min_num(0, amount)
```

トップダウンのDP
1枚ずつ使えば、ある額に対する最小枚数を保持しておけばよい。

```python
from functools import cache


class Solution:
    def coinChange(self, coins: List[int], amount: int) -> int:
        @cache
        def get_min_num(remain: int) -> int:
            if remain < 0:
                return -1
            if remain == 0:
                return 0
            
            min_num = float('inf')
            for coin in coins:
                min_num_from_next = get_min_num(remain - coin)
                if min_num_from_next == -1:
                    continue
                min_num = min(min_num, min_num_from_next + 1)
            if min_num == float('inf'):
                return -1
            return min_num
        
        return get_min_num(amount)
```

ボトムアップのDP。
leetcodeの解法はcoinを固定で各額のときの最小値を求めていた。なぜそれで正しい解が得られるのか分からない。。
個人的には小さいamountから埋めていく方法の方が分かりやすい。

```python
class Solution:
    def coinChange(self, coins: List[int], amount: int) -> int:
        min_count_by_amount = [float("inf")] * (amount + 1)
        min_count_by_amount[0] = 0

        for current_amount in range(amount + 1):
            for coin in coins:
                if current_amount - coin < 0:
                    continue
                min_count_by_amount[current_amount] = min(min_count_by_amount[current_amount], min_count_by_amount[current_amount - coin] + 1)
        if min_count_by_amount[-1] == float("inf"):
            return -1
        return min_count_by_amount[-1]
```


# step3: 15分
※間違えがあればn回目を増やす

## 1回目
```python
class Solution:
    def coinChange(self, coins: List[int], amount: int) -> int:
        min_count_by_amount = [float("inf")] * (amount + 1)
        min_count_by_amount[0] = 0

        for current_amount in range(amount + 1):
            for coin in coins:
                if current_amount - coin < 0:
                    continue
                min_count_by_amount[current_amount] = min(min_count_by_amount[current_amount], min_count_by_amount[current_amount - coin] + 1)
        if min_count_by_amount[current_amount] == float("inf"):
            return -1
        return min_count_by_amount[current_amount]
```

## 2回目
```python
class Solution:
    def coinChange(self, coins: List[int], amount: int) -> int:
        min_count_by_amount = [float("inf")] * (amount + 1)
        min_count_by_amount[0] = 0
        for current_amount in range(amount + 1):
            for coin in coins:
                if current_amount - coin < 0:
                    continue
                min_count_by_amount[current_amount] = min(min_count_by_amount[current_amount], min_count_by_amount[current_amount - coin] + 1)
        
        if isinf(min_count_by_amount[amount]):
            return -1
        return min_count_by_amount[amount]
```

## 3回目
```python
class Solution:
    def coinChange(self, coins: List[int], amount: int) -> int:
        min_count_by_amount = [float("inf")] * (amount + 1)
        min_count_by_amount[0] = 0
        for current_amount in range(amount + 1):
            for coin in coins:
                if current_amount - coin < 0:
                    continue
                min_count_by_amount[current_amount] = min(min_count_by_amount[current_amount], min_count_by_amount[current_amount - coin] + 1)

        if isinf(min_count_by_amount[amount]):
            return -1
        return min_count_by_amount[amount]
```