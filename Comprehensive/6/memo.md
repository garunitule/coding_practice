# 進め方
- step1: 5分考えて分からなかったら答えを見る。答えを理解したら、答えを隠して書く。筆が進まず5分立ったら答えを見る。答えを送信して正解するまで。
- step2: コードを読みやすく整える。動くコードになったら終了。
- step3: 時間を計りながら書く。10分以内に3回連続でアクセプトされるまで。

# step1: 15分
## 方針を考える
最初は二次元配列を考えてジグザグで埋めていき、そのあと各行毎に読みだして文字列を構築するのが分かりやすいと考えた。
ただメモリを食う方法なので、indexをうまく計算すればもっとメモリ消費量を抑えられると考えた。

何パターンか書き出してみて気づいたのは、
0列目: s[0], s[0 + 2 * (numRows - 1)], s[0 + 2 * (numRows - 1) + 2 * (numRows - 1)], ...
1列目: s[1], s[1 + 2 * (numRows - 1) - 2], s[1 + 2 * (numRows - 1) - 2 + 2], ...
...
i列目: s[i], s[i + 2 * (numRows - 1) - 2 * i], s[i + 2 * (numRows - 1) - 2 * i + 2 * i], ...
ということ。
一段下がるごとに最初の移動量は2減るのと、次の移動量は2 * (numRows - 1) - 最初の移動量になるということ。そして移動量は交互になっているということ。
これに気づいたので素直に実装した。
考察には30分ほどかかった。

```python
class Solution:
    def convert(self, s: str, numRows: int) -> str:
        if numRows == 1:
            return s

        result = ""
        row_num = 0
        while row_num < numRows:
            i = row_num
            step = 2 * (numRows - 1) - 2 * i
            while i < len(s):
                if 0 < step:
                    result += s[i]
                i += step
                step = 2 * (numRows - 1) - step
            row_num += 1
        
        return result

```

# step2: 30分
せっかくなので二次元配列版も実装してみた。
leetcodeの解法だと最初に2次元配列のサイズを決めて実装しているが、決めなくても良いと考えて実装してみた。

```python
class Solution:
    def convert(self, s: str, numRows: int) -> str:
        if numRows == 1:
            return s

        zigzag_strs = [[] for _ in range(numRows)]
        row = 0
        div = 1
        for c in s:
            zigzag_strs[row].append(c)
            row += div
            if row == 0:
                div = 1
            elif row == numRows - 1:
                div = -1
        
        result = ""
        for zigzag_str in zigzag_strs:
            result += "".join(zigzag_str)
        return result

```

この実装の方がシンプルだしメモリ使用量もほとんど変わらないはずだから、こちらの方が良さそうだな。

そういえば事前に確保して代入する方法と都度appendする方法はどちらがパフォーマンス上良いのか気になった。
appendはならしでO(1)とのことなので、あまり気にしなくて良さそう。
https://github.com/python/cpython/blob/dd88e77fad887aaf00ead1a3ba655fc00fd1dc25/Objects/listobject.c#L121


`"".join("".join(zigzag_str) for zigzag_str in zigzag_strs)`とも書ける 
https://discord.com/channels/1084280443945353267/1347375192065703986/1353577012593426502

変数名や一部ロジックを修正。
```python
class Solution:
    def convert(self, s: str, numRows: int) -> str:
        if numRows == 1:
            return s

        rows = [[] for _ in range(numRows)]
        row = 0
        row_step = 1
        for c in s:
            rows[row].append(c)
            if row == 0:
                row_step = 1
            elif row == numRows - 1:
                row_step = -1
            row += row_step
        
        return "".join("".join(row) for row in rows)

```

numRow == 1のときの処理もfor文に含めたかったが、かえって複雑になりそうだったのでやめた。

# step3: 15分
※間違えがあればn回目を増やす

## 1回目
```python
class Solution:
    def convert(self, s: str, numRows: int) -> str:
        if numRows == 1:
            return s
        
        rows = [[] for _ in range(numRows)]
        num_row = 0
        num_row_step = 1
        for c in s:
            rows[num_row].append(c)
            if num_row == 0:
                num_row_step = 1
            elif num_row == numRows - 1:
                num_row_step = -1
            num_row += num_row_step
        
        return "".join("".join(row) for row in rows)
```

## 2回目
```python
class Solution:
    def convert(self, s: str, numRows: int) -> str:
        if numRows == 1:
            return s
        
        rows = [[] for _ in range(numRows)]
        num_row = 0
        num_row_step = 1
        for c in s:
            rows[num_row].append(c)
            if num_row == 0:
                num_row_step = 1
            elif num_row == numRows - 1:
                num_row_step = -1
            num_row += num_row_step
        
        return "".join("".join(row) for row in rows)
```

## 3回目
```python
class Solution:
    def convert(self, s: str, numRows: int) -> str:
        if numRows == 1:
            return s
        
        rows = [[] for _ in range(numRows)]
        num_row = 0
        num_row_step = 1
        for c in s:
            rows[num_row].append(c)
            if num_row == 0:
                num_row_step = 1
            elif num_row == numRows - 1:
                num_row_step = -1
            num_row += num_row_step
        
        return "".join("".join(row) for row in rows)
```