# 進め方
- step1: 5分考えて分からなかったら答えを見る。答えを理解したら、答えを隠して書く。筆が進まず5分立ったら答えを見る。答えを送信して正解するまで。
- step2: コードを読みやすく整える。動くコードになったら終了。
- step3: 時間を計りながら書く。10分以内に3回連続でアクセプトされるまで。

# step1: 15分
## 方針を考える
愚直な方法だけど、最初に走査して各文字の登場回数を計上する（key: 文字, value: 回数の形式で保存）
再度最初から走査して、登場回数が1だったらそのインデックスを返す
ループが最後に到達したら-1を返す

n = len(s)
時間計算量：O(n)
空間計算量：O(1)（sに登場する文字の種類（今回は英語の小文字のみなので定数になる））

## 実装
```python
from collections import Counter


class Solution:
    def firstUniqChar(self, s: str) -> int:
        char_count = Counter(s)
        for i, c in enumerate(s):
            if char_count[c] == 1:
                return i
        return -1
```

## （必要に応じて）最適化
各要素を走査する必要があるので時間計算量O(n)よりは小さくならないはず
定数倍の改善も特に思いつかない

空間計算量を小さくする方法もあるのか？
登場する文字の種類が読めない場合はO(m)（m: 文字数）になるので、改善出来たら良いが思いつかない

## テストケースを考える
ユースケースに基づく考えたほうが良いケースは思いつかない


# step2: 30分
## 他の人のPRやコメントを踏まえて実装
https://discordapp.com/channels/1084280443945353267/1233603535862628432/1237973103670198292
1回登場した文字とインデックス：dict
2回以上登場した文字: set
を管理すると、ループが1回で済むというアイディア

1回にできないかなーとは思ったけど、具体的な方法までは思いつかなかった

3.7以降dictでキーの挿入順が維持されているのも知らなかった（これを知らないと上記の方法を思いつけない）
https://stackoverflow.com/questions/1867861/how-to-keep-keys-values-in-same-order-as-declared

OrderedDictの内部実装
https://github.com/python/cpython/blob/main/Lib/collections/__init__.py

https://github.com/t0hsumi/leetcode/pull/15#discussion_r1930362913
計算量は2乗になってもネイティブコードだから早いとかあるのか
この辺は仕組みをちゃんと理解したい

```python
class Solution:
    def firstUniqChar(self, s: str) -> int:
        unique_char_to_index = {}
        duplicate_chars = set()
        for i, c in enumerate(s):
            if c in duplicate_chars:
                unique_char_to_index.pop(c, None)
                continue
            unique_char_to_index[c] = i
            duplicate_chars.add(c)
        
        if not unique_char_to_index:
            return -1
        first_unique_char = next(iter(unique_char_to_index))
        return unique_char_to_index[first_unique_char]
```


# step3: 15分
※間違えがあればn回目を増やす

## 1回目
```python
class Solution:
    def firstUniqChar(self, s: str) -> int:
        unique_char_to_index = {}
        duplicate_chars = set()

        for i, c in enumerate(s):
            if c in duplicate_chars:
                unique_char_to_index.pop(c, None)
                continue
            unique_char_to_index[c] = i
            duplicate_chars.add(c)
        
        if not unique_char_to_index:
            return -1
        first_unique_char_index = next(iter(unique_char_to_index))
        return unique_char_to_index[first_unique_char_index]
```

## 2回目
```python
class Solution:
    def firstUniqChar(self, s: str) -> int:
        unique_char_to_index = {}
        duplicate_chars = set()

        for i, c in enumerate(s):
            if c in duplicate_chars:
                unique_char_to_index.pop(c, None)
                continue
            unique_char_to_index[c] = i
            duplicate_chars.add(c)
        
        if not unique_char_to_index:
            return -1
        first_unique_char_index = next(iter(unique_char_to_index))
        return unique_char_to_index[first_unique_char_index]
```

## 3回目
```python
class Solution:
    def firstUniqChar(self, s: str) -> int:
        unique_char_to_index = {}
        duplicate_chars = set()

        for i, c in enumerate(s):
            if c in duplicate_chars:
                unique_char_to_index.pop(c, None)
                continue
            unique_char_to_index[c] = i
            duplicate_chars.add(c)
        
        if not unique_char_to_index:
            return -1
        first_unique_char_index = next(iter(unique_char_to_index))
        return unique_char_to_index[first_unique_char_index]
```

## レビューコメントを踏まえた実装
### JavaのLinkedHashMapを使って実装してみる
```java
class Solution {
    public int firstUniqChar(String s) {
        LinkedHashMap<Character, Integer> uniqueCharToIndex = new LinkedHashMap<>();
        Set<Character> duplicateChars = new HashSet<>();

        for (int i = 0; i < s.length(); i++) {
            char c = s.charAt(i);
            if (duplicateChars.contains(c)) {
                uniqueCharToIndex.remove(c);
                continue;
            }
            uniqueCharToIndex.put(c, i);
            duplicateChars.add(c);
        }
        if (uniqueCharToIndex.isEmpty()) {
            return -1;
        }
        return uniqueCharToIndex.entrySet().iterator().next().getValue();
    }
}
```

### PythonでLinkedHashMapを実装してみる
```python
class Solution:
    def firstUniqChar(self, s: str) -> int:
        class Node:
            def __init__(self, key: Optional[str] = None, val: Optional[int] = None, prev: Self | None = None, next: Self | None = None):
                self.key = key
                self.val = val
                self.prev = prev
                self.next = next
            
        class LinkedHashMap:
            def __init__(self):
                self.head = Node()
                self.tail = Node(prev=self.head)
                self.head.next = self.tail
                self.map = {}

            def put(self, key: str, val: int):
                new_node = Node(key=key, val=val)
                if key in self.map:
                    old_node = self.map[key]
                    old_node_prev = old_node.prev
                    old_node_next = next_node.next

                    # before: old_node_prev <-> old_node <-> old_node_next
                    # after: old_node_prev <-> new_node <-> old_node_next
                    new_node.prev = old_node_prev
                    new_node.next = old_node_next
                    old_node_prev.next = new_node
                    old_node_next.prev = new_node
                else:
                    tail_prev = self.tail.prev

                    # before: tail_prev <-> self.tail
                    # after: tail_prev <-> new_node <-> self.tail
                    tail_prev.next = new_node
                    new_node.prev = tail_prev
                    new_node.next = self.tail
                    self.tail.prev = new_node

                self.map[key] = new_node
            
            def isEmpty(self):
                return self.head.next == self.tail and not self.map

            def remove(self, key):
                if key not in self.map:
                    return

                remove_node = self.map[key]
                remove_node_prev = remove_node.prev
                remove_node_next = remove_node.next

                # before: remove_node_prev <-> remove_node <-> remove_node_next
                # after: remove_node_prev <-> remove_node_next
                remove_node_prev.next = remove_node_next
                remove_node_next.prev = remove_node_prev
                
                del self.map[key]

            def getFirstInsertedNode(self):
                return self.head.next
                                
        unique_char_to_index = LinkedHashMap()
        duplicate_chars = set()
        
        for i, c in enumerate(s):
            if c in duplicate_chars:
                unique_char_to_index.remove(c)
                continue
            unique_char_to_index.put(c, i)
            duplicate_chars.add(c)
        
        if unique_char_to_index.isEmpty():
            return -1
        return unique_char_to_index.getFirstInsertedNode().val
            
```
