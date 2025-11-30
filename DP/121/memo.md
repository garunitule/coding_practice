# 進め方
- step1: 5分考えて分からなかったら答えを見る。答えを理解したら、答えを隠して書く。筆が進まず5分立ったら答えを見る。答えを送信して正解するまで。
- step2: コードを読みやすく整える。動くコードになったら終了。
- step3: 時間を計りながら書く。10分以内に3回連続でアクセプトされるまで。

# step1: 15分
## 実装

TLEするがブルートフォースで実装。O(n ** 2)
```python
class Solution:
    def maxProfit(self, prices: List[int]) -> int:
        num_prices = len(prices)
        max_profit = 0
        for i in range(num_prices):
            for j in range(i + 1, num_prices):
                max_profit = max(max_profit, prices[j] - prices[i])
        
        return max_profit
```

結局各iで必要なのはprices[j]の最大値。ただ、毎回最大値を計算するとO(n)かかってしまう。
後ろから計算したら各i（buy_day）に対して最大値を保持できるので、ループをまとめてO(n)で出来そう
ただ、ちょっと無理やり感がある
```python
class Solution:
    def maxProfit(self, prices: List[int]) -> int:
        if not prices and len(prices) == 0:
            return 0

        num_prices = len(prices)
        max_profit = 0
        max_price_future = prices[-1]
        for buy_day in range(num_prices - 2, -1, -1):
            max_profit = max(max_profit, max_price_future - prices[buy_day])
            max_price_future = max(max_price_future, prices[buy_day])
        
        return max_profit
```

# step2: 30分
## 他の人のPRやコメントを踏まえて実装
leetcodeの解法はstep1と発想が近いが、管理する変数が違う。
その時点の最小額とmax_profitを保持している。

例えば、[7, 100, 1, 5, 10]のようなpricesを考える。
7, 100で買える日のとと、7のときだけ考えれば良い。なぜなら、売れる日の候補が同じなら、一番安く買えるとき
だけ考えればよいから。
次に、1で買える日になった時を考える。上と同様の理由で、これ以降7で買える日は考えなくてよい。
ただ、一番利益が大きくなる組み合わせが、一番安く買った日とは限らないので、利益は常に計算しておく必要がある。

```python
class Solution:
    def maxProfit(self, prices: List[int]) -> int:
        if not prices or len(prices) == 1:
            return 0

        min_buy_price = float('inf')
        max_profit = 0
        for day in range(len(prices)):
            if prices[day] < min_buy_price:
                min_buy_price = prices[day]
                continue
            if max_profit < prices[day] - min_buy_price:
                max_profit = prices[day] - min_buy_price

        return max_profit
```

https://github.com/docto-rin/leetcode/pull/42/files
関数型の考えはちゃんと勉強してみたい。

https://github.com/hayashi-ay/leetcode/pull/52/files
step1と同じような解き方をしてる

https://github.com/olsen-blue/Arai60/pull/37/files
少しシュッとした書き方をしている


# step3: 15分
※間違えがあればn回目を増やす

## 1回目
```python
class Solution:
    def maxProfit(self, prices: List[int]) -> int:
        if not prices or len(prices) == 1:
            return 0
        
        min_buy_price = float('inf')
        max_profit = 0
        for day in range(len(prices)):
            if prices[day] < min_buy_price:
                min_buy_price = prices[day]
                continue
            if max_profit < prices[day] - min_buy_price:
                max_profit = prices[day] - min_buy_price

        return max_profit 
```

## 2回目
```python
class Solution:
    def maxProfit(self, prices: List[int]) -> int:
        if not prices or len(prices) == 1:
            return 0
        
        min_buy_price = float('inf')
        max_profit = 0
        for day in range(len(prices)):
            if prices[day] < min_buy_price:
                min_buy_price = prices[day]
                continue
            if max_profit < prices[day] - min_buy_price:
                max_profit = prices[day] - min_buy_price
    
        return max_profit
```

## 3回目
```python
class Solution:
    def maxProfit(self, prices: List[int]) -> int:
        if not prices or len(prices) == 1:
            return 0
        
        min_buy_price = float('inf')
        max_profit = 0
        for price in prices:
            if price < min_buy_price:
                min_buy_price = price
                continue
            if max_profit < price - min_buy_price:
                max_profit = price - min_buy_price

        return max_profit
```