# 進め方
- step1: 5分考えて分からなかったら答えを見る。答えを理解したら、答えを隠して書く。筆が進まず5分立ったら答えを見る。答えを送信して正解するまで。
- step2: コードを読みやすく整える。動くコードになったら終了。
- step3: 時間を計りながら書く。10分以内に3回連続でアクセプトされるまで。

# step1: 15分
## 方針を考える
各状態で許可された操作
- no stock: none, buy
- one stock: none, sell, sell -> buy
愚直にやるなら、全操作を網羅すればできるが、高々O(3 ** n)かかってしまう。

これを書くのもしんどいので実装は書かない。漸化式っぽいのでDPで考えてみる。
買った値段より高くなったら売るという戦略でいい気がしてきた。
[1, 100, 300, 500] だったら、1で買って500で売るのと、1で買って, 100,300,500で売って買う場合で利益が同じ。つまり、株価が連続で増加するなら、maxの時点で売ればいい。
[1, 100, 2, 200]でも同じで、1で買って100で売る、2で買って200で売ればいい。これも株価が増加して最大のタイミングで売ればいい。
[100, 2, 1, 200, 300]の場合だと、株価が下がって上がるタイミングの1で買って、300で売ればいい。ただ、200, 300それぞれで売り買いしてもいい。

まとめると、
- 買うタイミング：prices[i] < prices[i - 1] and prices[i] < prices[i + 1]
- 売るタイミング: prices[i - 1] < prices[i] and prices[i + 1] < prices[i]
ようは谷で買って、山で売ろうということか

## 実装
最初に書いたコード。[3, 3]でTLEした。
同じ額が連続することを考慮できていなかった。

```python
class Solution:
    def maxProfit(self, prices: List[int]) -> int:
        if not prices or len(prices) == 1:
            return 0

        max_profit = 0
        day = 0
        while day < len(prices):
            while day < len(prices):
                if 0 <= day - 1 and prices[day - 1] < prices[day]:
                    day += 1
                    continue
                if day + 1 < len(prices) and prices[day + 1] < prices[day]:
                    day += 1
                    continue
                break
            if day < len(prices):
                bought_stock_amount = prices[day]

            while day < len(prices):
                if 0 <= day - 1 and prices[day] < prices[day - 1]:
                    day += 1
                    continue
                if day + 1 < len(prices) and prices[day] < prices[day + 1]:
                    day += 1
                    continue
                break
            if day < len(prices):
                max_profit += prices[day] - bought_stock_amount
        
        return max_profit

```

問題をきれいに捉えられてないせいか、どう改善すればいいか分からなかった。
いったん解答を見る。

# step2: 30分
## 他の人のPRやコメントを踏まえて実装
leetcodeの解法を見た
まず、TLEするがブルートフォースの実装。
部分問題に分割して解いている。
この実装を見ると、重複する部分問題が発生していることが分かり、改善したい気持ちになる。

```python
class Solution:
    def maxProfit(self, prices: List[int]) -> int:
        return self.max_profit_sub_prices(prices, 0)
    
    def max_profit_sub_prices(self, prices: List[int], start: int) -> int:
        if len(prices) < start:
            return 0
        
        max_profit = 0
        for buy_day in range(start, len(prices)):
            for sell_day in range(buy_day + 1, len(prices)):
                if prices[buy_day] >= prices[sell_day]:
                    continue
                max_profit = max(
                    max_profit,
                    prices[sell_day] - prices[buy_day] + self.max_profit_sub_prices(prices, sell_day + 1)
                )

        return max_profit
```

次は隣接するピークを取得する方法、自分がstep1でやろうとしていた方法。
もっとシンプルで良かったのか。
自分はv字の谷、^字の山を考えていたけど、
- 谷: prices[i] < prices[i + 1]
- 山: prices[i] > prices[i + 1]
のみでよい

https://discord.com/channels/1084280443945353267/1233603535862628432/1290661949868216472
>毎日できることは、株を持っているか、お金を持っているかの2択なので、未来が見える人になったとして、どちらがいいかを考えればいいのです。

このコメント通りだな

```python
class Solution:
    def maxProfit(self, prices: List[int]) -> int:
        if not prices:
            return 0
        
        day = 0
        max_profit = 0
        days = len(prices)
        while day < days - 1:
            while day < days - 1 and prices[day] >= prices[day + 1]:
                day += 1
            buy_price = prices[day]
            while day < days - 1 and prices[day] <= prices[day + 1]:
                day += 1
            sell_price = prices[day]
            max_profit += sell_price - buy_price
        
        return max_profit
```

さらにシンプルに一つ一つの増加減に対して計算する方法

```python
class Solution:
    def maxProfit(self, prices: List[int]) -> int:
        if not prices:
            return 0
        
        max_profit = 0
        day = 0
        days = len(prices)
        while day < days - 1:
            if prices[day] < prices[day + 1]:
                max_profit += prices[day + 1] - prices[day]
            day += 1
        
        return max_profit
```

https://github.com/ryosuketc/leetcode_arai60/pull/51/files
読んだ

https://github.com/fuga-98/arai60/pull/38/files
読んだ

https://github.com/tokuhirat/LeetCode/pull/38/files#r2173703267
DP？で解いている
なるほど、株を保有している/してないときの利益を計算している
直感的ではないが、帰納的に成立していることは分かる

```python
class Solution:
    def maxProfit(self, prices: List[int]) -> int:
        if not prices:
            return 0

        cash_with_stock = -prices[0]
        cash_without_stock = 0
        for day in range(1, len(prices)):
            cash_with_stock = max(cash_with_stock, cash_without_stock - prices[day])
            cash_without_stock = max(cash_without_stock, cash_with_stock + prices[day])

        return cash_without_stock
```


# step3: 15分
※間違えがあればn回目を増やす

## 1回目
```python
class Solution:
    def maxProfit(self, prices: List[int]) -> int:
        if not prices:
            return 0
        
        max_profit = 0
        days = len(prices)
        day = 0
        while day < days - 1:
            while day < days - 1 and prices[day] >= prices[day + 1]:
                day += 1
            buy_price = prices[day]

            while day < days - 1 and prices[day] <= prices[day + 1]:
                day += 1
            sell_price = prices[day]
            max_profit += sell_price - buy_price

        return max_profit
```

## 2回目
```python
class Solution:
    def maxProfit(self, prices: List[int]) -> int:
        if not prices:
            return 0
        
        day = 0
        days = len(prices)
        max_profit = 0
        while day < days - 1:
            while day < days - 1 and prices[day] >= prices[day + 1]:
                day += 1
            buy_price = prices[day]
            
            while day < days - 1 and prices[day] <= prices[day + 1]:
                day += 1
            sell_price = prices[day]
            max_profit += sell_price - buy_price

        return max_profit 

```

## 3回目
```python
class Solution:
    def maxProfit(self, prices: List[int]) -> int:
        if not prices:
            return 0
        
        day = 0
        days = len(prices)
        max_profit = 0
        while day < days - 1:
            while day < days - 1 and prices[day] >= prices[day + 1]:
                day += 1
            buy_price = prices[day]
            while day < days - 1 and prices[day] <= prices[day + 1]:
                day += 1
            sell_price = prices[day]
            max_profit += sell_price - buy_price

        return max_profit
```