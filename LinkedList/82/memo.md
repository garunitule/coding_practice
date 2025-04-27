## 取り組み方
step1: 5分考えて分からなかったら答えを見る。答えを理解したら、答えを隠して書く。筆が進まず5分立ったら答えを見る。答えを送信して正解するまで。
step2: コードを読みやすく整える。動くコードになったら終了。
step3: 時間を計りながら書く。10分以内に3回連続でアクセプトされるまで。

## step1
基本的には83. Remove Duplicates from Sorted Listと一緒の考え方でよさそう。
ただ、重複したノードを全て消す必要があることに注意が必要。

現在のノードのnextを重複してたら次の値のノードに飛ばす
、という処理を繰り返せばよさそう。

問題は先頭に重複があった場合だけど、先頭に値Noneのダミーノードを追加しておけばよさそう。

終了条件はdummyがNoneに到達すること

5分では実装できなかった。
つまったのは、現在のノードのnextとその次のノードの値が一致した場合としなかった場合で
- 一致した場合：現在のノードのnextを重複してたら値の次の値のノードを参照させる
- 一致しなかった場合：そのまま。現在のノードを次に進める。
という処理になるが、両者をうまく実装する方法が思いつかなかった。

ここで解答を見た。（コード中のコメントは自分）
```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, val=0, next=None):
#         self.val = val
#         self.next = next
class Solution:
    def deleteDuplicates(self, head: Optional[ListNode]) -> Optional[ListNode]:
        sentinel = ListNode(0, head)
        pred = sentinel

        while head is not None:
            # 重複があった場合は削除する。pred.nextを更新することで実現している。
            if head.next is not None and head.val == head.next.val:
                # 次のノードと重複がなくなるまで進める
                while head.next is not None and head.val == head.next.val:
                    head = head.next
                # headの次のノードは重複してないので、predの次のノードに現在のノードのnextを参照させる
                pred.next = head.next
            
            # 重複してない場合はpred.nextを次に進める
            else:
                pred = pred.next
            # headを、今重複を調査した値の次の値のノードに進める
            # 前のif分の条件判定がいずれの場合でも、headとその次のノードの値は一致していない
            head = head.next
        
        return sentinel.next
```

実装できなかった理由としては最初からきれいに実装しようとしすぎた。
考えている段階で重複があった場合とそうじゃない場合で処理が結構違うことは分かっていたので、いったんif文で重複ありなしをわけてそれぞれで処理を書く方針にすればよかった。良い書き方は動いた後でよい。


step1で通したコード
```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, val=0, next=None):
#         self.val = val
#         self.next = next
class Solution:
    def deleteDuplicates(self, head: Optional[ListNode]) -> Optional[ListNode]:
        sentinel = ListNode(0, head)
        pred = sentinel

        while head is not None:
            if head.next is not None and head.val == head.next.val:
                while head.next is not None and head.val == head.next.val:
                    head.next = head.next.next
                pred.next = head.next
            else:
                pred = pred.next
            head = head.next

        return sentinel.next
```

## step2
再帰でも書ける
```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, val=0, next=None):
#         self.val = val
#         self.next = next
class Solution:
    def deleteDuplicates(self, head: Optional[ListNode]) -> Optional[ListNode]:
        if head is None or head.next is None:
            return head
        
        if head.val == head.next.val:
            while head.next is not None and head.val == head.next.val:
                head = head.next
            return self.deleteDuplicates(head.next)
        else:
            head.next = self.deleteDuplicates(head.next)
            return head
```

deleteDuplicatesは「渡されたListNodeから重複を削除したListNodeを返すメソッド」と捉えられるので、
- head.valに重複があった場合：head.valの次の値の先頭を返す。処理としてはhead.valの重複をなくして返している。再帰的にheadの後半のノードに対しても呼ばれるので、「head以降から重複が排除されたListNode」が返ることになる。
- head.valに重複がなかった場合：head.next以降の重複を削除して、headを返している
のように考えられるってことか。

個人的には再帰を使った解法のほうがシンプルに感じる。

そういえばPythonってどれくらい再帰的に呼び出すとスタックオーバーフローを起こすんだろう。

### Pythonのスタックオーバーフローの調査
>limit の最大値はプラットフォームによって異なります。深い再帰処理が必要な場合にはプラットフォームがサポートしている範囲内でより大きな値を指定することができますが、この値が大きすぎればクラッシュするので注意が必要です。

https://docs.python.org/ja/3/library/sys.html#sys.setrecursionlimit

とのこと。そりゃ実行環境によるか。

自分の環境だと1000
```
garunitule@garunitule:~/codes/coding_practice$ python3
Python 3.10.12 (main, Feb  4 2025, 14:57:36) [GCC 11.4.0] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> import sys
>>> sys.getrecursionlimit()
1000
>>>
```

ここでデフォルト値を定義している
https://github.com/python/cpython/blob/cc05e4b8676dd438e7928e079925c71dba20cc0f/Include/internal/pycore_ceval.h#L43


ここで指定してるっぽい（ぽいと言ってるのはAIに聞いた結果でPythonがどのように動くかちゃんと理解できていないため）
https://github.com/python/cpython/blob/cc05e4b8676dd438e7928e079925c71dba20cc0f/Include/internal/pycore_runtime_init.h#L132
https://github.com/python/cpython/blob/cc05e4b8676dd438e7928e079925c71dba20cc0f/Include/internal/pycore_runtime_init.h#L187

いったん調査はここまで。こことは別でCPythonがどのように動いているのか処理を追ってみたい。


### 他のPRコメント見る
https://github.com/tokuhirat/LeetCode/pull/4/files
いくつか書き方を提示していただいてる。
自分がstep1で書いた書き方。自分も気になってたことだが、重複削除する際にnode.valの次の値のnodeまで進めない違和感はあった。
```python
        while node is not None and node.next is not None:
            if node.val != node.next.val:
                tail = node
            else:
                while node.next is not None and node.val == node.next.val:
                    node.next = node.next.next
                tail.next = node.next
            node = node.next
        return dummy.next
```

こう書くと重複削除時にnode.valの次の値のノードまで進んでそれを付け替えるという、よりやりたいことに忠実なコードになる。
これすごくいいな。
```python
class Solution:
    def deleteDuplicates(self, head: Optional[ListNode]) -> Optional[ListNode]:
        dummy = ListNode(next=head)
        tail = dummy
        node = head
        while node is not None and node.next is not None:
            val = node.val
            if node.next.val != val:
                tail = node
                node = node.next
                continue
            while node is not None and node.val == val:
                node = node.next
            tail.next = node
        return dummy.next
```

ちなみに変数はtailを使っているがどうなんだろう。step1で書いたpred（predecessor）はちょっとわかりづらいとは思うが、tailかというと違和感がある。
prev（previous）がいいかな。より際立たせるならnodeやheadじゃなくてcurr（current）を使うとよさそう。

今までの考えを総合すると下記。
```python
class Solution:
    def deleteDuplicates(self, head: Optional[ListNode]) -> Optional[ListNode]:
        sentinel = ListNode(next=head)
        prev = sentinel
        curr = head

        while curr is not None and curr.next is not None:
            if curr.val != curr.next.val:
                prev = prev.next
                curr = curr.next
                continue
            
            val = curr.val
            while curr is not None and val == curr.val:
                curr = curr.next
            prev.next = curr
        
        return sentinel.next
```

## step3
### prev, currentを使う方法
最初の2回はtypoで失敗、最後3回は成功。最後3回は1分30秒くらい。
```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, val=0, next=None):
#         self.val = val
#         self.next = next
class Solution:
    def deleteDuplicates(self, head: Optional[ListNode]) -> Optional[ListNode]:
        sentinel = ListNode(next = head)
        prev = sentinel
        current = head

        while current is not None and current.next is not None:
            if current.val != current.next.val:
                prev = prev.next
                current = current.next
                continue
            
            val = current.val
            while current is not None and current.val == val:
                current = current.next
            prev.next = current
        
        return sentinel.next
```


### 再帰を使う方法
最初はwhile文の条件にhead is not Noneを入れ忘れた。

```python
class Solution:
    def deleteDuplicates(self, head: Optional[ListNode]) -> Optional[ListNode]:
        if head is None or head.next is None:
            return head
        
        if head.val == head.next.val:
            val = head.val
            while head is not None and head.val == val:
                head = head.next
            return self.deleteDuplicates(head)
        else:
            head.next = self.deleteDuplicates(head.next)
            return head
```