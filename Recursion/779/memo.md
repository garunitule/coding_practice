# 進め方
- step1: 5分考えて分からなかったら答えを見る。答えを理解したら、答えを隠して書く。筆が進まず5分立ったら答えを見る。答えを送信して正解するまで。
- step2: コードを読みやすく整える。動くコードになったら終了。
- step3: 時間を計りながら書く。10分以内に3回連続でアクセプトされるまで。

# step1: 15分
n番目の行に対して前半部分は(n-1)番目の行を引き継いでいて後半部分はそれを反転させた行になっていることに気づいたので、下記のように実装した。
実際にはn < kの場合にエラーを返すなどバリデーション処理をした方が良いと思う。

```python
class Solution:
    def kthGrammar(self, n: int, k: int) -> int:
        if n == 1:
            return 0

        half_length_of_row = 2 ** (n - 2) 
        if half_length_of_row < k:
            return 1 ^ self.kthGrammar(n, k - half_length_of_row)
        return self.kthGrammar(n - 1, k)
```

# step2: 30分
根がroot_valの2分木を考えて、row_number行目、symbol_number番目のノードの値を求める
左にいくときは次のroot_valは同じ値だし、右に行くときは反転

```python
class Solution:
    def kth_symbol(self, row_number: int, symbol_number: int, root_val) -> int:
        if row_number == 1:
            return root_val
        
        row_count = 2 ** (row_number - 1)
        if row_count // 2 < symbol_number:
            return self.kth_symbol(row_number - 1, symbol_number - row_count // 2, 1 ^ root_val)
        return self.kth_symbol(row_number - 1, symbol_number, root_val)

    def kthGrammar(self, n: int, k: int) -> int:
        return self.kth_symbol(n, k, 0)
```

再帰なのでループでも実装できる。ループだと再帰と違って空間計算量がO(1)になる
```python
class Solution:
    def kthGrammar(self, n: int, k: int) -> int:
        row_length = 2 ** (n - 1)
        k = symbol_position
        result = 0
        while 1 < row_length:
            if row_length // 2 < symbol_position:
                result ^= 1
                symbol_position -= row_length // 2
            row_length //= 2

        return result
```

https://discord.com/channels/1084280443945353267/1200089668901937312/1216054396161622078
後でビット演算の実装も学んでおきたい。ビット演算の慣れに不安がある。

# step3: 15分
※間違えがあればn回目を増やす

## 1回目
```python
class Solution:
    def kthGrammar(self, n: int, k: int) -> int:
        result = 0
        row_length = 2 ** (n - 1)
        symbol_position = k
        while row_length > 1:
            if row_length // 2 < symbol_position:
                result ^= 1
                symbol_position -= row_length // 2
            row_length //= 2

        return result
```

## 2回目
```python
class Solution:
    def kthGrammar(self, n: int, k: int) -> int:
        result = 0
        row_length = 2 ** (n - 1)
        symbol_position = k
        while row_length > 1:
            if symbol_position > row_length // 2:
                result ^= 1
                symbol_position -= row_length // 2
            row_length //= 2
        
        return result
```

## 3回目
```python
class Solution:
    def kthGrammar(self, n: int, k: int) -> int:
        result = 0
        row_length = 2 ** (n - 1)
        symbol_position = k
        while row_length > 1:
            if symbol_position > row_length // 2:
                result ^= 1
                symbol_position -= row_length // 2
            row_length //= 2

        return result
```