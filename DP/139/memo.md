# 進め方
- step1: 5分考えて分からなかったら答えを見る。答えを理解したら、答えを隠して書く。筆が進まず5分立ったら答えを見る。答えを送信して正解するまで。
- step2: コードを読みやすく整える。動くコードになったら終了。
- step3: 時間を計りながら書く。10分以内に3回連続でアクセプトされるまで。

# step1: 15分
まず愚直に行う実装を考えた。再帰で解く方法。
n: sの長さ
m: wordDictに含まれるword数
とする。
最悪時間計算量はO(m ** n)になる。
最悪の場合sからの枝分かれを考えると、毎回mだけ広がって、深さがnになるので。
1 <= m <= 1000, 1 <= n <= 300なのでTLEするだろう。
実際TLEした。

```python
class Solution:
    def wordBreak(self, s: str, wordDict: List[str]) -> bool:
        result = False
        for word in wordDict:
            if s == word:
                return True
            if s[:len(word)] == word:
                result = result or self.wordBreak(s[len(word):], wordDict)

        return result
```

resultがTrueになった時点でreturnするなど小さい改善できるが、最悪計算量は変わらない
wordDictから作ってsに一致するか判定するのを愚直にやると、O(m!)かかるな
DFSっぽく実装できそうだが、さっきの実装と計算量は変わらずO(m ** n)だけかかるな

TLEする実装。
```python
class Solution:
    def wordBreak(self, s: str, wordDict: List[str]) -> bool:
        stack = [s]
        while stack:
            target = stack.pop()
            for word in wordDict:
                if target == word:
                    return True
                if target[:len(word)] == word:
                    stack.append(target[len(word):])
        
        return False
```

# step2: 30分
ちらっと解答を見てstep1の実装のメモ化するように改善したら通った。

```python
class Solution:
    def wordBreak(self, s: str, wordDict: List[str]) -> bool:
        stack = [s]
        seen = set()
        while stack:
            target = stack.pop()
            if target in seen:
                continue
            seen.add(target)
            for word in wordDict:
                if target == word:
                    return True
                if target[:len(word)] == word:
                    stack.append(target[len(word):])
        
        return False
```

時間計算量はAIに聞きながら考えたが、step1で通ったコードの最悪時間計算量はO(n*m*n)になる
まずseenがあることで、同じtargetに対して複数回計算しない。targetはn種類。
次に各targetに対してはm回。wordDict分繰り返すので。
for文の各処理が全て実行された都市、if文の条件式とstackへの追加処理がある。
if文の条件式はO(min(len(target), len(word)))でlen(target)を超えることはないので、O(len(target))できるし、len(target)はnを超えることはないのでO(n)にできる
stackへの追加処理のうち、target[len(word):]も同様に考えてO(n)とみなせる
よって、最悪時間計算量はO(n*m*n)になる
平均時間計算量を求めるなら、target[:len(word)] == wordがTrueになる確率を文字でおいて考える必要がありそう。

BFSによる実装。
startからsの末尾まで走査して一致するwordがあるか調査。あれば、一致した部分以降を調査対象とする。
wordDictに対して繰り返すのではなく、sに対して繰り返している。
状況次第だが、今回の問題設定としてはsに対して繰り返した方が早い。


```python
from collections import deque


class Solution:
    def wordBreak(self, s: str, wordDict: List[str]) -> bool:
        queue = deque([0])
        words = set(wordDict)
        seen = set()

        while queue:
            start = queue.popleft()
            if start == len(s):
                return True
            if start in seen:
                continue
            seen.add(start)
            for end in range(start, len(s)):
                if s[start : end+1] in words:
                    queue.append(end + 1)

        return False
```

貰うDPでも解ける、たしかに。　
i文字目までを作れるかどうかを判定する。
先頭からi文字目までがwordに一致する場合と、途中からi文字目までがwordに一致するパターンがある。

```python
class Solution:
    def wordBreak(self, s: str, wordDict: List[str]) -> bool:
        is_breakable = [False] * len(s)
        for i in range(len(s)):
            for word in wordDict:
                word_length = len(word)
                if i < word_length - 1:
                    continue
                if i == word_length - 1 or (i >= word_length and is_breakable[i - word_length]):
                    if s[i-word_length+1 : i+1] == word:
                        is_breakable[i] = True
                        break

        return is_breakable[-1]
```

Trieを使った実装。
s[0:n], s[1:n], ..., s[n-1:n] がwordから作れるかTrie木で判定する。
s[i:n]のうち、i>0の場合はs[i-1:n]がwordから作れることが前提になる。

```python
class TrieNode:
    def __init__(self):
        self.is_word = False
        self.children = {}

class Solution:
    def wordBreak(self, s: str, wordDict: List[str]) -> bool:
        root = TrieNode()
        for word in wordDict:
            curr = root
            for c in word:
                if c not in curr.children:
                    curr.children[c] = TrieNode()
                curr = curr.children[c]
            curr.is_word = True

        is_breakable = [False] * len(s)
        for i in range(len(s)):
            if i == 0 or is_breakable[i - 1]:
                curr = root
                for j in range(i, len(s)):
                    if s[j] not in curr.children:
                        break
                    curr = curr.children[s[j]]
                    if curr.is_word:
                        is_breakable[j] = True

        return is_breakable[-1]
```

送るDPでも解ける。
平均的な時間計算量はO(n * m * l)（l: wordの平均的な長さ）。
ひとつ前のTrieの実装は、送るDPの実装があって、wordによる検索部分を最適化した実装と言える。
個人的には送るDPの方が分かりやすいな。
貰うDPだと先頭から一致した場合と途中から一致した場合両方を書く必要があって明示的だが煩雑になる。

```python
class Solution:
    def wordBreak(self, s: str, wordDict: List[str]) -> bool:
        is_breakable = [False] * (len(s) + 1)
        is_breakable[0] = True
        for i in range(len(s)):
            if not is_breakable[i]:
                continue
            for word in wordDict:
                if s[i:i+len(word)] == word:
                    is_breakable[i+len(word)] = True
        
        return is_breakable[-1]
```

# step3: 15分
※間違えがあればn回目を増やす

## 1回目
```python
class TrieNode:
    def __init__(self):
        self.is_word = False
        self.children = {}
    
    @staticmethod
    def of(wordDict: List[str]) -> Self:
        root = TrieNode()
        for word in wordDict:
            curr = root
            for c in word:
                if c not in curr.children:
                    curr.children[c] = TrieNode()
                curr = curr.children[c]
            curr.is_word = True
        return root

class Solution:
    def wordBreak(self, s: str, wordDict: List[str]) -> bool:
        wordTrie = TrieNode.of(wordDict)

        is_breakable = [False] * len(s)
        for start in range(len(s)):
            if start == 0 or is_breakable[start - 1]:
                curr = wordTrie
                for i in range(start, len(s)):
                    if s[i] not in curr.children:
                        break
                    curr = curr.children[s[i]]
                    if curr.is_word:
                        is_breakable[i] = True

        return is_breakable[-1]
```

## 2回目
```python
class TrieNode:
    def __init__(self):
        self.is_word = False
        self.children = {}
    
    @staticmethod
    def of(wordDict: List[str]) -> bool:
        root = TrieNode()
        for word in wordDict:
            curr = root
            for c in word:
                if c not in curr.children:
                    curr.children[c] = TrieNode()
                curr = curr.children[c]
            curr.is_word = True

        return root

class Solution:
    def wordBreak(self, s: str, wordDict: List[str]) -> bool:
        is_breakable = [False] * len(s)
        word_trie = TrieNode.of(wordDict)
        for start in range(len(s)):
            if start == 0 or is_breakable[start - 1]:
                curr = word_trie
                for i in range(start, len(s)):
                    if s[i] not in curr.children:
                        break
                    curr = curr.children[s[i]]
                    if curr.is_word:
                        is_breakable[i] = True 

        return is_breakable[-1]
```

## 3回目
```python
class TrieNode:
    def __init__(self):
        self.is_word = False
        self.children = {}
    
    @staticmethod
    def of(wordDict: List[str]) -> Self:
        root = TrieNode()
        for word in wordDict:
            curr = root
            for c in word:
                if c not in curr.children:
                    curr.children[c] = TrieNode()
                curr = curr.children[c]
            curr.is_word = True

        return root

class Solution:
    def wordBreak(self, s: str, wordDict: List[str]) -> bool:
        word_trie = TrieNode.of(wordDict)
        is_breakable = [False] * len(s)
        for start in range(len(s)):
            if start == 0 or is_breakable[start - 1]:
                curr = word_trie
                for i in range(start, len(s)):
                    if s[i] not in curr.children:
                        break
                    curr = curr.children[s[i]]
                    if curr.is_word:
                        is_breakable[i] = True

        return is_breakable[-1]
```