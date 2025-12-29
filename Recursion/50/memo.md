# 進め方
- step1: 5分考えて分からなかったら答えを見る。答えを理解したら、答えを隠して書く。筆が進まず5分立ったら答えを見る。答えを送信して正解するまで。
- step2: コードを読みやすく整える。動くコードになったら終了。
- step3: 時間を計りながら書く。10分以内に3回連続でアクセプトされるまで。

# step1: 15分
nがすごく大きい。

下記の実装だと再帰が深くなりすぎて落ちる。
```python
class Solution:
    def myPow(self, x: float, n: int) -> float:
        if n == 0:
            return 1
        if n < 0:
            return 1 / x * self.myPow(x, n + 1)
        else:
            return x * self.myPow(x, n - 1)
```

呼び出しごとに深さが半分になるようにした。O(nlog(n))になる、これでもTLEするだろう。
再帰呼び出しの木を書いたら同じ計算をたくさん行ってることに気づいたのでキャッシュした。
こうすると時間計算量はO(log(n))になる。
ざっくり評価なので実行時間ちゃんと見積もれるようになりたい。
```python
from functools import cache


class Solution:
    @cache
    def myPow(self, x: float, n: int) -> float:
        if n == 0:
            return 1
        if n == 1:
            return x
        if n == -1:
            return 1 / x
        
        half = n // 2
        return self.myPow(x, n - half) * self.myPow(x, half)

```



# step2: 30分
self.myPow(x * x, n // 2)にすればキャッシュする必要がないので空間計算量も減らせる

```python
class Solution:
    def myPow(self, x: float, n: int) -> float:
        if n == 0:
            return 1
        
        if n >= 0:
            base = x
            exponent = n
        else:
            base = 1 / x
            exponent = -n
        if exponent % 2 == 1:
            return base * self.myPow(base ** 2, (exponent - 1) // 2)
        return self.myPow(base ** 2, exponent // 2)
```

同じことをループで実現
```python
class Solution:
    def myPow(self, x: float, n: int) -> float:
        if n == 0:
            return 1
        
        if n >= 0:
            base = x
            exponent = n
        else:
            base = 1 / x
            exponent = -n
        result = 1
        while exponent != 0:
            if exponent % 2 == 1:
                result *= base
                exponent -= 1
            base = base ** 2
            exponent //= 2

        return result
```

https://github.com/Satorien/LeetCode/pull/45/files
再帰の実装で、nが奇数のときに、`return x * self.myPow(x, n - 1)`とする実装も分かりやすいとは思った。
ただ奇数のときの呼び出し回数が増えるので最大で倍の呼び出し回数になってしまうか。呼び出し回数による速度劣化と分かりやすさのトレードオフになりそう。

nを2進数表記したときに1が立っている箇所を掛け算する実装もある。今までの解法の方が分かりやすいが。
```python
class Solution:
    def myPow(self, x: float, n: int) -> float:
        if n == 0:
            return 1
        if n < 0:
            return self.myPow(1 / x, -n)
        
        base = x
        exponent = n
        bit = 1
        result = 1
        while exponent >= bit:
            if exponent & bit > 0:
                result *= base
            bit <<= 1
            base *= base
        
        return result
```


# step3: 15分
※間違えがあればn回目を増やす

## 1回目
```python
class Solution:
    def myPow(self, x: float, n: int) -> float:
        if n == 0:
            return 1
        if n < 0:
            return self.myPow(1 / x, -n)
        
        base = x
        exponent = n
        bit = 1
        result = 1
        while bit <= exponent:
            if exponent & bit > 0:
                result *= base
            base *= base
            bit <<= 1

        return result
```

## 2回目
```python
class Solution:
    def myPow(self, x: float, n: int) -> float:
        if n == 0:
            return 1
        if n < 0:
            return self.myPow(1 / x, -n)
        
        base = x
        exponent = n
        bit = 1
        result = 1
        while bit <= exponent:
            if exponent & bit != 0:
                result *= base
            base *= base
            bit <<= 1

        return result
```

## 3回目
```python
class Solution:
    def myPow(self, x: float, n: int) -> float:
        if n == 0:
            return 1
        if n < 0:
            return self.myPow(1 / x, -n)
        
        base = x
        exponent = n
        bit = 1
        result = 1
        while bit <= exponent:
            if exponent & bit != 0:
                result *= base
            base *= base
            bit <<= 1

        return result
```