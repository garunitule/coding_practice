## 取り組み方
step1: 5分考えて分からなかったら答えを見る。答えを理解したら、答えを隠して書く。筆が進まず5分立ったら答えを見る。答えを送信して正解するまで。
step2: コードを読みやすく整える。動くコードになったら終了。
step3: 時間を計りながら書く。10分以内に3回連続でアクセプトされるまで。

## step1
パッと見、ノードの先頭から最後まで走査は必要そう。
重複があったらnextを付け替えるという方針はシンプルだし、計算量の観点でも、
- 時間計算量：O(N)
- 空間計算量：O(1)
となるので筋がよさそう。
訪れた値をsetに突っ込んでおいて重複を判断するとかもできそうだけど、余計にメモリを消費するだけでメリットがなさそうなのでこの方針で進める

node, curというノードを指し示す変数を用意し、
- node.val = cur.val：重複がある
- node.val != cur.val：重複がない
の2つで場合分けすれば良いと考えた。

継続条件はcurがNoneじゃない間でok

ここまで5分くらい（考えるの3分、文章に起こすの2分くらい）
なお、考えるときはiPadでざっくりイメージ図を書いている。手で書いてるのでここには載せられないが。

nodeという変数名が微妙だが、他に思いつかないのでいったん実装を進める

下記のように実装した。3分くらい。
```python
class Solution:
    def deleteDuplicates(self, head: Optional[ListNode]) -> Optional[ListNode]:
        node = head
        cur = head

        while cur is not None:
            # 重複がある場合は重複を削除し、引き続き現在の値で重複削除をする
            if node.val == cur.val:
                node.next = cur.next
                cur = cur.next
            # 重複がない場合は、次のnodeの重複削除に進む
            else:
                node = cur
                cur = cur.next
        
        # headは変わらず先頭を参照しているため
        return head
```

## step2
### 他の人のPRを読んでみる
https://github.com/shintaro1993/arai60/pull/6/files

>headがNoneの場合に下の方まで処理を進めてもいいのかという気持ちがある。

たしかに、気持ちは分かる。headがNoneだった場合に、明示的に早期returnしてあげたほうが読み手は楽かも。
個人的にはこれくらいのコードの長さであれば早期returnしなくてもよいかなと思った。
自分のコードだとcurがNoneだったらwhile文が一度も走らずにreturn head（None）が実行されるので、さっと分かるかなと。

https://github.com/yus-yus/leetcode/pull/3/files
```Python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, val=0, next=None):
#         self.val = val
#         self.next = next
class Solution:
    def deleteDuplicates(self, head: Optional[ListNode]) -> Optional[ListNode]:
        node = head

        while node and node.next:
            if node.val == node.next.val:
                node.next = node.next.next
            else:
                node = node.next
        return head
```
一変数で実現している。こっちのほうが分かりやすいか。自分のコードだと、ちゃんと読まないとcurという変数の目的が理解できないが、node.next = node.next.next だと次の次に参照を移していることが分かりやすい。

https://github.com/oshibusu/leetcode-arai60/pull/4/files

```python
class Solution:
    def deleteDuplicates(self, head: Optional[ListNode]) -> Optional[ListNode]:
        node = head
        while node is not None:
            while node.next is not None and node.val == node.next.val:
                node.next = node.next.next
            node = node.next
        return head
```

2重のloopで実現している。
こっちのほうが今のnodeの値の重複をなくすまで続ける、という意図が分かりやすいな。重複がなくなったら内側のfor loopを抜けて次に進むってことか。

## step3
下記の書き方が一番やりたいことに対して素直なので、下記の書き方でstep3を実施した
```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, val=0, next=None):
#         self.val = val
#         self.next = next
class Solution:
    def deleteDuplicates(self, head: Optional[ListNode]) -> Optional[ListNode]:
        node = head
        while node is not None:
            while node.next is not None and node.val == node.next.val:
                node.next = node.next.next
            node = node.next
        return head
```