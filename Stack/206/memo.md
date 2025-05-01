## 取り組み方
step1: 5分考えて分からなかったら答えを見る。答えを理解したら、答えを隠して書く。筆が進まず5分立ったら答えを見る。答えを送信して正解するまで。
step2: コードを読みやすく整える。動くコードになったら終了。
step3: 時間を計りながら書く。10分以内に3回連続でアクセプトされるまで。

## step1
与えられた連結リストをたどって、新しい連結リストを作ればよさそう。
5 -> 4 -> 3 -> 2 -> 1
を作る場合、
ListNode(1, None)
ListNode(2, ListNode(1, None))
...
と作っていくので、与えられた連結リストを順番通りにたどりながら逆順にした連結リストを作れる。

再帰でもできそうだが、スタックオーバーフローしてしまうので避ける（ノード数が5000までだが、プラットフォームによるがデフォルトだと最大で1000なので）。


```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, val=0, next=None):
#         self.val = val
#         self.next = next
class Solution:
    def reverseList(self, head: Optional[ListNode]) -> Optional[ListNode]:
        node = head
        ans = None
        while node is not None:
            ans = ListNode(node.val, ans)
            node = node.next
        return ans
```

## step2
### 他の人のPRを見る
https://github.com/Satorien/LeetCode/pull/7/files
付け替える方式。
https://github.com/Satorien/LeetCode/pull/7/files#r2055662397
この方式が想定解なんじゃないかという意見

1 -> 2 -> 3 -> 4 -> 5 -> None
があったとしたら、
None <- 1  2 -> 3 -> 4 -> 5 -> None
None <- 1 <- 2  3 -> 4 -> 5 -> None
...
None <- 1 <- 2 <- 3 <- 4 <- 5
のように逆向きにしていく方式

実装してみた。
```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, val=0, next=None):
#         self.val = val
#         self.next = next
class Solution:
    def reverseList(self, head: Optional[ListNode]) -> Optional[ListNode]:
        node = head
        reversed_list = None
        while node is not None:
            next_node = node.next
            node.next = reversed_list
            reversed_list = node
            node = next_node
        return reversed_list
```


再帰でもできるっぽい。まず、自分で考えてみる。
1 -> 2 -> 3 -> 4 -> 5　を考える。
reverseListに1が渡った場合、処理中で再帰的に呼び出したreverseListが5 -> 4 -> 3 -> 2を返せば最後のノードのnextを1にすればいいんだけど、それは筋が悪そう。再帰的に呼び出したreverseListの末尾のノードに1をつけるのはできるが、返ってきたノード数分の走査が必要になるため。

これ最初にスタックか何かに詰める必要がある？
そしたら
(1, None)
(2, (1, None))
...
と順番に作っていけそう。

https://github.com/Satorien/LeetCode/pull/7/files
と思ったけど付け替え + 再帰で出来るみたい。
1 -> 2 -> 3 -> 4 -> 5 -> None
だとして、
1 -> 2 -> 3 -> 4 <- 5
1 -> 2 -> 3 <- 4 <- 5
...
None <- 1 <- 2 -> 3 <- 4 <- 5
になる。


実装してみた。
最後のノードを返し、再帰的に付け替えを行う
```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, val=0, next=None):
#         self.val = val
#         self.next = next
class Solution:
    def reverseList(self, head: Optional[ListNode]) -> Optional[ListNode]:
        if head is None or head.next is None:
            return head
        
        node = head
        last_node = self.reverseList(head.next)
        node.next.next = node
        # これがないと1が2を向いたままになってしまう
        node.next = None
        return last_node
```

## step3
3パターンで実装した。
どれもノード数をNとした場合に
時間計算量：O(N)
空間計算量：O(N)

ノード数的に再帰の深さを心配しなくてよいのであれば再帰付け替え方式が分かりやすいと思う。ただデフォルト値は1000なので心もとない気がする。。
ノード数的にスタックオーバーフローに懸念がある場合は、新しく作る方式が分かりやすいと思う。

### 新しく作る方式
```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, val=0, next=None):
#         self.val = val
#         self.next = next
class Solution:
    def reverseList(self, head: Optional[ListNode]) -> Optional[ListNode]:
        reversed_list = None
        node = head
        while node is not None:
            reversed_list = ListNode(node.val, reversed_list)
            node = node.next
        return reversed_list
```

### 付け替え方式
```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, val=0, next=None):
#         self.val = val
#         self.next = next
class Solution:
    def reverseList(self, head: Optional[ListNode]) -> Optional[ListNode]:
        reversed_list = None
        node = head
        while node is not None:
            next_node = node.next
            node.next = reversed_list
            reversed_list = node
            node = next_node
        return reversed_list
```

### 再帰的に付け替える方式
```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, val=0, next=None):
#         self.val = val
#         self.next = next
class Solution:
    def reverseList(self, head: Optional[ListNode]) -> Optional[ListNode]:
        if head is None or head.next is None:
            return head
        
        node = head
        last_node = self.reverseList(node.next)
        node.next.next = node
        node.next = None
        return last_node
```

## レビューコメントを反映した実装
### 繋ぎ変え
修正点
- last_relinked, relinking, next_relinking という変数名に修正した
- 

```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, val=0, next=None):
#         self.val = val
#         self.next = next
class Solution:
    def reverseList(self, head: Optional[ListNode]) -> Optional[ListNode]:
        relinking = head
        last_relinked = None

        while relinking is not None:
            next_relinking = relinking.next
            relinking.next = last_relinked
            last_relinked = relinking
            relinking = next_relinking
        
        return last_relinked
```

### 再帰繋ぎ変え
修正点
- headをそのまま利用
- reversed_headという変数名に修正

```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, val=0, next=None):
#         self.val = val
#         self.next = next
class Solution:
    def reverseList(self, head: Optional[ListNode]) -> Optional[ListNode]:
        if head is None or head.next is None:
            return head
        
        reversed_head = self.reverseList(head.next)
        head.next.next = head
        head.next = None
        return reversed_head
```