## 取り組み方
step1: 5分考えて分からなかったら答えを見る。答えを理解したら、答えを隠して書く。筆が進まず5分立ったら答えを見る。答えを送信して正解するまで。
step2: コードを読みやすく整える。動くコードになったら終了。
step3: 時間を計りながら書く。10分以内に3回連続でアクセプトされるまで。

## step1
まず逆順で入ってることに注意。
繰り上がりを管理する変数を作っておいて、各桁を足し上げていくのがよさそう。

例3にあるように、
9999999 + 9999 = 89990001（逆順にしてます）
もあるので、継続条件は、下記のor条件になる
- l1のノードの値がある
- l2のノードの値がある
- 繰り上がり桁がある

ここまで4分ほど。大体実装イメージはついたので実装してみる。


最初の回答。8分ほどで実装したが、Memory Limit Exceedが発生。
%と//が逆になっていた。修正したら通った。
```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, val=0, next=None):
#         self.val = val
#         self.next = next
class Solution:
    def addTwoNumbers(self, l1: Optional[ListNode], l2: Optional[ListNode]) -> Optional[ListNode]:
        carry = 0
        sumList = ListNode()
        ans = sumList

        while l1 is not None or l2 is not None or carry > 0:
            # 現在の桁の和と繰り上がりの計算
            l1Val = l1.val if l1 is not None else 0
            l2Val = l2.val if l2 is not None else 0
            val = l1Val + l2Val + carry
            carry = val % 10
            nodeVal = val // 10
            sumList.next = ListNode(val=nodeVal)
            sumList = sumList.next
            

            # 次の桁に進む
            l1 = l1.next if l1 is not None else None
            l2 = l2.next if l2 is not None else None
        
        return ans.next
```

## step2
nodeValだとわかりづらい。columnSumとかのほうがよいか。
sumListもdummyHeadとかのほうがよいか。

### 他のPRを見る
https://github.com/tokuhirat/LeetCode/pull/5/files
再帰でも書ける

```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, val=0, next=None):
#         self.val = val
#         self.next = next
class Solution:
    def addTwoNumbers(self, l1: Optional[ListNode], l2: Optional[ListNode]) -> Optional[ListNode]:
        return self.addTwoColumns(l1, l2, 0)
    
    def addTwoColumns(self, l1: Optional[ListNode], l2: Optional[ListNode], carry: int) -> Optional[ListNode]:
        # 足すものがなくなったら終了
        if l1 is None and l2 is None and carry == 0:
            return None
        
        # 現在の値を計算し、l1, l2を次に進める
        if l1 is not None:
            carry += l1.val
            l1 = l1.next
        if l2 is not None:
            carry += l2.val
            l2 = l2.next
        
        # 進めたl1, l2以降に対して計算し、nextに設定し返す
        nextNode = self.addTwoColumns(l1, l2, carry // 10)
        return ListNode(carry % 10, nextNode)

```

https://github.com/shintaro1993/arai60/pull/8/files#r2037071272
同じif文が登場しているので省略できるという話

書いてみた
条件式はまとめられるが、自分としては処理の流れでまとめたい。
```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, val=0, next=None):
#         self.val = val
#         self.next = next
class Solution:
    def addTwoNumbers(self, l1: Optional[ListNode], l2: Optional[ListNode]) -> Optional[ListNode]:
        dummyHead = ListNode()
        current = dummyHead
        carry = 0

        while l1 is not None or l2 is not None or carry > 0:
            # 和の計算とl1, l2の更新
            currentSum = carry
            if l1 is not None:
                currentSum += l1.val
                l1 = l1.next
            if l2 is not None:
                currentSum += l2.val
                l2 = l2.next

            # 計算した和からcarryと和を示すノードの更新
            carry = currentSum // 10
            newNode = ListNode(currentSum % 10)
            current.next = newNode
            current = current.next 
        
        return dummyHead.next
```

ので、step1に近い下記の書き方のほうが処理の流れに対して自然なコードな気がする。
イメージした処理の流れ
- 和を計算し、currentに反映
- 次の桁の計算準備（current, carry, l1, l2の更新処理）
この書き方だとif文が重複するデメリットはあるが、処理の流れは分かりやすいと思った。

```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, val=0, next=None):
#         self.val = val
#         self.next = next
class Solution:
    def addTwoNumbers(self, l1: Optional[ListNode], l2: Optional[ListNode]) -> Optional[ListNode]:
        dummyHead = ListNode()
        current = dummyHead
        carry = 0

        while l1 is not None or l2 is not None or carry > 0:
            # 和を計算しcurrentに反映
            l1Val = l1.val if l1 is not None else 0
            l2Val = l2.val if l2 is not None else 0
            currentSum = l1Val + l2Val + carry
            current.next = ListNode(currentSum % 10)
    
            # 次の桁の計算準備
            current = current.next
            carry = currentSum // 10
            if l1 is not None:
                l1 = l1.next
            if l2 is not None:
                l2 = l2.next
        
        return dummyHead.next
```


https://github.com/Fuminiton/LeetCode/pull/23#discussion_r1998877143
たしかにPythonの三項演算子は癖があるか。個人的には慣れてしまったので違和感感じないけどどうなんだろう。
しいていえば、
```python
if l1 is not None:
    l1 = l1.next
```
と書けるのを

```python
l1 = l1.next if l1 is not None else None
```
と書くのは違和感がある。
理由は本来else句は不要なのに三項演算子で書くために登場させているから。

## step3
### while loopでの処理
```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, val=0, next=None):
#         self.val = val
#         self.next = next
class Solution:
    def addTwoNumbers(self, l1: Optional[ListNode], l2: Optional[ListNode]) -> Optional[ListNode]:
        dummyHead = ListNode()
        current = dummyHead
        carry = 0

        while l1 is not None or l2 is not None or carry > 0:
            l1Val = 　l1.val if l1 is not None else 0
            l2Val = l2.val if l2 is not None else 0
            currentSum = l1Val + l2Val + carry
            current.next = ListNode(currentSum % 10)

            current = current.next
            carry = currentSum // 10
            if l1 is not None:
                l1 = l1.next
            if l2 is not None:
                l2 = l2.next
        
        return dummyHead.next
```

### 再帰
```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, val=0, next=None):
#         self.val = val
#         self.next = next
class Solution:
    def addTwoNumbers(self, l1: Optional[ListNode], l2: Optional[ListNode]) -> Optional[ListNode]:
        return self.addTwoColumns(l1, l2, 0)
        
    def addTwoColumns(self, l1: Optional[ListNode], l2: Optional[ListNode], carry: int) -> Optional[ListNode]:
        if l1 is None and l2 is None and carry == 0:
            return None
        
        if l1 is not None:
            carry += l1.val
            l1 = l1.next
        if l2 is not None:
            carry += l2.val
            l2 = l2.next
        
        nextNode = self.addTwoColumns(l1, l2, carry // 10)
        return ListNode(carry % 10, nextNode)
```