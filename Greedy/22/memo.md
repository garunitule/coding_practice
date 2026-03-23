# 進め方
- step1: 5分考えて分からなかったら答えを見る。答えを理解したら、答えを隠して書く。筆が進まず5分立ったら答えを見る。答えを送信して正解するまで。
- step2: コードを読みやすく整える。動くコードになったら終了。
- step3: 時間を計りながら書く。10分以内に3回連続でアクセプトされるまで。

# step1: 15分
## 方針を考える
愚直にやると、2 ** n通りの組み合わせを考え、それぞれで妥当かどうか判定すればいい。
解けそうだが記述量が多くなりそうなので別の方法で考える。

木として捉えるとシンプルになりそう。
有効なかっこ列の長さは2nになる。
作成する際、開きかっこと閉じかっこのどちらかを選ぶ必要がある。
下記を満たすように選択すればいい
- 開きかっこの数 >= 閉じかっこの数
- 開きかっこの数 <= n

## 実装
不変条件を満たすように実装した。

```python
class Solution:
    def generateParenthesis(self, n: int) -> List[str]:
        parentheses = []
        def make_parentheses(parenthesis: str, open_count: int, close_count: int) -> None:
            if close_count == n:
                parentheses.append(parenthesis)
                return
            if open_count < n:
                parenthesis += '('
                make_parentheses(parenthesis, open_count + 1, close_count)
                parenthesis = parenthesis[:-1]
            if close_count < open_count:
                parenthesis += ')'
                make_parentheses(parenthesis, open_count, close_count + 1)
                parenthesis = parenthesis[:-1]
        
        make_parentheses('', 0, 0)
        return parentheses
```

空間計算量はO(n)。再帰が2nまで深くなるため。
時間計算量は有効な組み合わせ数に比例するが具体的な数が分からない。カタラン数というものがあるらしい。

# step2: 30分
分割統治による解法があった。
F(i): i個のペアのかっこで作られる組み合わせ
F(n) = ( + F(i) + ) + F(n - i - 1)
i = 0, 1, ..., n - 1

leetcodeの解法ではメモ化してなかったが利用した。
メモ化していればgenerateParenthesisの呼び出しはn+1回で済む。
メモ化していないと結構多そう。
F(3)を考えても、
F(0), F(2)
F(1), F(1)
F(2), F(0)
で、
F(2)を考えると
F(0), F(1)
F(1), F(0)
なので、nが大きくなるほどこの差が大きくなりそうな感覚はある。
メモ化するとi=0, 1, ..., n - 1までの計算結果を保持するのでメモリ使用量は増えるデメリットはある。
見積もれたらいいがどうしたらよいかよくわかっていない。
見積もれなかったとしてもnの数を変えて実測してみるとかはできそう。


```python
from functools import cache


class Solution:
    @cache
    def generateParenthesis(self, n: int) -> List[str]:
        if n == 0:
            return [""]

        parentheses = []
        for i in range(n):
            left_parentheses = self.generateParenthesis(i)
            right_parentheses = self.generateParenthesis(n - i - 1)
            for left_parenthesis in left_parentheses:
                for right_parenthesis in right_parentheses:
                    parentheses.append("(" + left_parenthesis + ")" + right_parenthesis)

        return parentheses
```

# step3: 15分
※間違えがあればn回目を増やす

## 1回目
```python
class Solution:
    def generateParenthesis(self, n: int) -> List[str]:
        parentheses = []
        def make_parentheses(parenthesis: str, open_count: int, close_count: int) -> None:
            if close_count == n:
                parentheses.append(parenthesis)
                return

            if open_count < n:
                make_parentheses(parenthesis + "(", open_count + 1, close_count)
            if close_count < open_count:
                make_parentheses(parenthesis + ")", open_count, close_count + 1)

        make_parentheses("", 0, 0)
        return parentheses
```

## 2回目
```python
class Solution:
    def generateParenthesis(self, n: int) -> List[str]:
        parentheses = []
        def make_parentheses(parenthesis: str, open_count: int, close_count: int) -> None:
            if close_count == n:
                parentheses.append(parenthesis)
                return

            if open_count < n:
                make_parentheses(parenthesis + "(", open_count + 1, close_count)
            if close_count < open_count:
                make_parentheses(parenthesis + ")", open_count, close_count + 1)

        make_parentheses("", 0, 0)
        return parentheses
```

## 3回目
```python
class Solution:
    def generateParenthesis(self, n: int) -> List[str]:
        parentheses = []
        def make_parentheses(parenthesis: str, open_count: int, close_count: int) -> None:
            if close_count == n:
                parentheses.append(parenthesis)
                return
            
            if open_count < n:
                make_parentheses(parenthesis + "(", open_count + 1, close_count)
            if close_count < open_count:
                make_parentheses(parenthesis + ")", open_count, close_count + 1)

        make_parentheses("", 0, 0)
        return parentheses
```