## 取り組み方
step1: 5分考えて分からなかったら答えを見る。答えを理解したら、答えを隠して書く。筆が進まず5分立ったら答えを見る。答えを送信して正解するまで。
step2: コードを読みやすく整える。動くコードになったら終了。
step3: 時間を計りながら書く。10分以内に3回連続でアクセプトされるまで。

## step1
### 思考メモ
まず入出力とheadのデータ構造を確認。

この問題は以前解いたことがあった。slow, fastで進める。

while文の継続条件で悩んだ。
・slow is not fast
・fast is not None
いったん両方含めることにした。

そういえば != と is not って違いはあるのか？
後で調べてみる。

while文の継続条件に両方含めていたけど、while文を抜けた後の判定が面倒なので、slow is not fast のみに変更した。
本来step2でやることかもしれないが、確実にすっきりすることが見えたのと変えないとミスしそうなので、step1で対応した。

### 自分の回答
```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution:
    def hasCycle(self, head: Optional[ListNode]) -> bool:
        if not head:
            return False

        slow = head
        # 開始位置をずらす
        fast = head.next

        while slow is not fast:
            slow = slow.next
            if fast is None or fast.next is None:
                return False
            fast = fast.next.next
        
        return True
```

## step2
### 思考
step1の時点で読みやすいコードになったので、疑問点の調査と他の人のコードリーディングをやってみる。

#### step1で気になったことを調査
step1で気になった !や=の比較と notやisの比較の違いを調べる。

公式ドキュメントを参照しながら調査した。

下記の調査結果から、is, is notを使った方が意図に合致しているし安全と判断した。
理由は、isは振る舞いをカスタマイズできず、同じメモリ上のオブジェクトを参照しているかどうかを判定しているから。==は__eq__を呼び出しているので、振る舞いが変わりうる。

以下、調査結果

https://docs.python.org/ja/3.13/reference/expressions.html#is
isやis notはid関数を使ってオブジェクトの同一性を判定している。
https://docs.python.org/ja/3.13/library/functions.html#id
id関数はオブジェクトのメモリアドレスを示す。
よって、x is yはxとyが同じメモリ上のオブジェクトを指しているかどうかを判定している。
振る舞いはカスタマイズできず、いかなる2つのオブジェクトにも適用でき、決して例外を送出しないとのこと。

https://docs.python.org/ja/3.13/reference/datamodel.html#object.__eq__
==は、__eq__を呼び出している。なので、クラス側で__eq__をカスタマイズしていれば、必ずしも同じメモリ上のオブジェクトじゃなくてもTrueが返ることがある。

#### 他の人のコードやコメントを読んでみる

以前解いたことがあるという理由だけでslow, fastで進めてしまったのは良くなかった。
setの解法と比較してメモリ使用量の観点でメリットがある、という理由で選択すべきだった。
https://github.com/tk-hirom/Arai60/pull/1#discussion_r1641231416


参考程度とのことなので、setを使った解法でやってみる。
なお、setを使った理由は検索がO(1)でできるから。

### 解法
```python
class Solution:
    def hasCycle(self, head: Optional[ListNode]) -> bool:
        visited = set()
        while head:
            if head in visited:
                return True
            visited.add(head)
            head = head.next
        
        return False
```

## step3
setを使った解法のほうがオーソドックだと思うので、setを使った解法で解いた。

```python
class Solution:
    def hasCycle(self, head: Optional[ListNode]) -> bool:
        visited = set()

        while head:
            if head in visited:
                return True
            visited.add(head)
            head = head.next
        
        return False
```