## 取り組み方
step1: 5分考えて分からなかったら答えを見る。答えを理解したら、答えを隠して書く。筆が進まず5分立ったら答えを見る。答えを送信して正解するまで。
step2: コードを読みやすく整える。動くコードになったら終了。
step3: 時間を計りながら書く。10分以内に3回連続でアクセプトされるまで。

## step1
Nodeを進んでいって、
- Noneになったら循環はないのでNoneを返す
- 現在のNodeがvisitedにあればそのNodeを返す
で出来そう。

Nodeの長さをNとしたら、
- 時間計算量はO(N)
- 空間計算量はO(N)（visitedがあるので）

他の解法は特に思いつかないし、計算量の観点でも筋は悪くなさそうなのでこの方針でコードを書く

```python
class Solution:
    def detectCycle(self, head: Optional[ListNode]) -> Optional[ListNode]:
        node = head
        visited = set()

        while node is not None:
            if node in visited:
                return node

            visited.add(node)
            node = node.next

        return None
```

## step2
### step1終了時に考えたこと
終了条件を下記のようにすることもできる。うーん、好みな気がする。
```python
class Solution:
    def detectCycle(self, head: Optional[ListNode]) -> Optional[ListNode]:
        node = head
        visited = set()

        while node not in visited: 
            if node is None:
                return None

            visited.add(node)
            node = node.next            

        return node

```

### 他の人のPRを読む
https://github.com/shintaro1993/arai60/pull/5/files
実装についてじゃないが、setの挙動について詳しく調査していただいている。

https://github.com/oshibusu/leetcode-arai60/pull/3/files#diff-3f9c32204f87045957945540758e10433c163d26cdb83f858c1743dd36e22292
これもfast, slowで解けるのか。
fast, slowは循環の検出はできるが、循環の最初のNodeの検出は出来ないと思っていた。
ただコードが複雑にはなりそう。
Floyd's Tortoise and Hare Algorithmというらしい。
まず循環がある場合はfastとslowは衝突する。
次に片方をスタート地点から、片方を衝突点から進めた場合、次は循環の最初で衝突する。つまり、片方がスタート地点から循環の最初に進んだタイミングで、もう片方が衝突点から循環の最初に進むということ。
スタート地点から循環までの距離をa,　循環の最初から衝突点までの距離をb, 循環の長さをcとすれば、
2(a + b) = a + b + kc
kc = a + b
という関係が得られる。
スタート地点から循環の最初までの距離はa。片方がスタート地点からa進んだ間に、もう片方も当然衝突点からaだけ進むが、a = kc - bだけ進んでいて循環の最初にたどり着く。

アルゴリズムは理解できたので、コードを書いてみる。
141と違って、fast = head.nextは使えない。kc = a + bの関係が崩れるので。
```python
class Solution:
    def detectCycle(self, head: Optional[ListNode]) -> Optional[ListNode]:
        slow = head
        fast = head

        # 衝突点を見つける。循環がなければその時点でreturn
        while fast is not None and fast.next is not None:
            slow = slow.next
            fast = fast.next.next
            if slow is fast:
                break
        else:
            return None

        # 循環の最初を見つける
        slow = head
        while slow is not fast:
            slow = slow.next
            fast = fast.next
        return slow
```

## step3
### setを使った解法
```python
class Solution:
    def detectCycle(self, head: Optional[ListNode]) -> Optional[ListNode]:
        node = head
        visited = set()

        while node is not None:
            if node in visited:
                return node

            visited.add(node)
            node = node.next            

        return None
```

### Floyd's Tortoise and Hare Algorithmを使った解法
```python
class Solution:
    def detectCycle(self, head: Optional[ListNode]) -> Optional[ListNode]:
        slow = head
        fast = head

        while fast is not None and fast.next is not None:
            slow = slow.next
            fast = fast.next.next
            if slow is fast:
                break
        else:
            return None
        
        slow = head
        while slow is not fast:
            slow = slow.next
            fast = fast.next
        return slow
```