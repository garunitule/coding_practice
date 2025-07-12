# 進め方
- step1: 5分考えて分からなかったら答えを見る。答えを理解したら、答えを隠して書く。筆が進まず5分立ったら答えを見る。答えを送信して正解するまで。
- step2: コードを読みやすく整える。動くコードになったら終了。
- step3: 時間を計りながら書く。10分以内に3回連続でアクセプトされるまで。

# step1: 15分
## 方針を考える
beginWord, endWord, wordListの各単語をノードとしてとらえて、beginWordからendWordへの最短経路を求める問題と捉えることができそう

その場合
- グラフを構築する
- 最短経路を求める
の2ステップになる

グラフの構築は1文字だけ変えただけの単語同士を同じ辺で結べばよい
各単語ごとに全ての単語の中から1文字違いの単語を辺とすればよいので、時間計算量はO(n**2 * 単語のlength)

無向循環グラフの最短経路を求めるアルゴリズムはあったような気もするが覚えてない
ただ、全ての経路を探索して最小値を返せばよいはず
再帰で実装して呼び出しのたびにインクリメントしてbeginWordにたどり着いたら合計値を返す、みたいな実装でできないか

ここまで12分

## 実装
※下記のコードは動きません
アイディアを実装してみたが、循環が発生するコードになっている
beginWordからendWordへのすべての経路について、各ノードが訪問済みかどうかを保持するのは実装的にも空間計算量的にも厳しそう
いったん回答をみる

```python
from collections import defaultdict


class Solution:
    def ladderLength(self, beginWord: str, endWord: str, wordList: List[str]) -> int:
        def is_words_differed_by_single_letter(word1: str, word2: str):
            i1 = 0
            i2 = 0
            different_letter_count = 0
            while i1 < len(word1) and i2 < len(word2):
                if word1[i1] != word2[i2]:
                    different_letter_count += 1
                i1 += 1
                i2 += 1
            return different_letter_count == 1

        word_to_adjacent = defaultdict(list)
        all_word_list = wordList + [beginWord, endWord]
        for i in range(len(all_word_list)):
            for j in range(len(all_word_list)):
                if is_words_differed_by_single_letter(all_word_list[i], all_word_list[j]):
                    word_to_adjacent[all_word_list[i]].append(all_word_list[j])
                    word_to_adjacent[all_word_list[j]].append(all_word_list[i])
        
        MAX_SHORTEST_SEQUENCE_LENGTH = 2 << 30
        def get_shortest_transformation_sequence(begin_word: str, end_word: str, sequence_length: int) -> int:
            if begin_word == end_word:
                return sequence_length
            
            sequence_length += 1
            adjacent_words = word_to_adjacent[begin_word]
            shortest_sequence_length = MAX_SHORTEST_SEQUENCE_LENGTH
            for adjacent_word in adjacent_words:
                length = get_shortest_transformation_sequence(adjacent_word, end_word, sequence_length)
                shortest_sequence_length = min(length, shortest_sequence_length)
            return shortest_sequence_length
        
        return get_shortest_transformation_sequence(beginWord, endWord, 0)
                
```

# step2: 30分
## 他の人のPRやコメントを踏まえて実装
leetcodeの解法を見た
グラフ構築部分は「*」を用いた中間表現を導入することで時間計算量を削減している
O(n ** 2 * k)からO(n * k)になる

探索部分は単にBFSだった。BFSは始点からの距離が近い順に進むので最短経路を求められるってことか。
あるノードに訪問した時、最初に訪問した時が最短経路になっている。


https://discord.com/channels/1084280443945353267/1200089668901937312/1216123084889788486
文字列の前半部分をkey, 全体をvalueとして格納。バケット内で比較する。
後半に対しても同様のことをすれば、ある程度一様になっていればO(nk)で計算できる。
ただ、あるバケットに偏るとO(n ** 2 * k)になってしまう
そこで、偏りがあるバケットに対してさらに前後半に分けてバケット内で比較すると最悪でもO(nklogk)になるらしい
n * k + n * k/2 + ...  = 2nkになるはずなので、O(nk)だと思うけど違うのか？
まあ、n * k + n * k + .... = nklogk と安全側に倒して考えることもできるか

https://discord.com/channels/1084280443945353267/1201211204547383386/1215702126945112094
名前空間と考えるのいいな。自分は文脈という解釈をしていたけど、空間という方がしっくりくる。
変数名で悩むこともあるので空間を踏まえた命名をしたい。

いったん動くコード
visitedに追加してからword_and_level_to_visitに追加しているのが気持ち悪い
単にword_and_level_to_visitをqueueとかにすればいいか？

```python
from collections import defaultdict, deque


class Solution:
    def ladderLength(self, beginWord: str, endWord: str, wordList: List[str]) -> int:
        def get_all_patterns(word: str) -> list[str]:
            patterns = []
            for i in range(len(word)):
                patterns.append(word[:i] + '*' + word[(i + 1):])
            return patterns
        
        # pattern_to_wordsを構築
        pattern_to_words = defaultdict(list)
        all_words = wordList + [beginWord]
        for word in all_words:
            for pattern in get_all_patterns(word):
                pattern_to_words[pattern].append(word)
        
        # beginWord -> endWordの最短距離を求める
        word_and_level_to_visit = deque()
        word_and_level_to_visit.append((beginWord, 1))
        visited = set()
        visited.add(beginWord)
        while word_and_level_to_visit:
            word, level = word_and_level_to_visit.popleft()

            if word == endWord:
                return level
            
            adjacent_words_to_visit = set()
            for pattern in get_all_patterns(word):
                for adjacent_word in pattern_to_words[pattern]:
                    if adjacent_word in visited:
                        continue
                    visited.add(adjacent_word)
                    adjacent_words_to_visit.add(adjacent_word)
            
            for adjacent_word in adjacent_words_to_visit:
                word_and_level_to_visit.append((adjacent_word, level + 1))
        return 0
```

他の人のPRやコメントを見る
https://github.com/ichika0615/arai60/pull/14#discussion_r1991234699
他の人の実装だけど、自分にも当てはまる
自分の実装だと変数名から読み取れるとも思うが、afterのほうが親切なのは間違いない

before
```python
            keys = word_to_keys[present_word]
            for key in keys:
                for word in key_to_words[key]:
                    if word not in seen_words:
```

after
```python
for word in new_neighbors(present_word):
```

https://github.com/ryosuketc/leetcode_arai60/pull/20/files
levelじゃなくdistance, lengthを使ってる。levelでもそんなに違和感ないかな？ただ最終的にlevelをendWordへの距離や長さとして使ってるので、lengthとかがいいかな
あと素直にqueueという変数名を使っている
patternよりもwildcard_wordのほうが親切か。どうだろう。
https://discord.com/channels/1084280443945353267/1303605021597761649/1306631474065309728
このようにクラスにカプセル化してしまえばkeyとかでも伝わるか

https://github.com/potrue/leetcode/pull/20/files
ひとつ前のPRと割と近い

## 実装
カプセル化した方が命名もスッキリできるし、メインの処理が何か分かりやすいので、下記のような実装にした

```python
from collections import defaultdict, deque
from typing import Iterator

class WordNeighbors:
    def __init__(self):
        self.key_to_neighbors = defaultdict(list)
    
    def to_key(self, word: str) -> Iterator[str]:
        for i in range(len(word)):
            yield (word[:i], word[(i + 1):])

    def add(self, word: str) -> list[str]:
        for key in self.to_key(word):
            self.key_to_neighbors[key].append(word)

    def get_neighbor(self, word: str) -> Iterator[str]:
        for key in self.to_key(word):
            for neighbor in self.key_to_neighbors[key]:
                yield neighbor


class Solution:
    def ladderLength(self, beginWord: str, endWord: str, wordList: List[str]) -> int:
        if endWord not in wordList:
            return 0
        
        word_neighbors = WordNeighbors()
        all_words = wordList + [beginWord]
        for word in all_words:
            word_neighbors.add(word)
        
        queue = deque()
        queue.append((beginWord, 1))
        visited = set()
        visited.add(beginWord)

        while queue:
            word, length = queue.popleft()
            if word == endWord:
                return length

            for neighbor in word_neighbors.get_neighbor(word):
                if neighbor in visited:
                    continue
                visited.add(neighbor)
                queue.append((neighbor, length + 1))
        
        return 0
```

# step3: 15分
※間違えがあればn回目を増やす

## 1回目
beginWordがwordListにない場合のみ追加する

```python
from collections import defaultdict, deque
from typing import Iterator


class WordNeighbors:
    def __init__(self):
        self.key_to_neighbors = defaultdict(list)
    
    def to_key(self, word: str) -> Iterator[tuple[str, str]]:
        for i in range(len(word)):
            yield (word[:i], word[(i + 1):])
    
    def add(self, word: str) -> None:
        for key in self.to_key(word):
            self.key_to_neighbors[key].append(word)

    def get_neighbor(self, word: str) -> Iterator[str]:
        for key in self.to_key(word):
            for neighbor in self.key_to_neighbors[key]:
                yield neighbor

class Solution:
    def get_all_words(self, word_list: list[str], begin_word: str) -> list[str]:
        all_words = word_list
        for word in word_list:
            if word == begin_word:
                return word_list
        else:
            return word_list + [begin_word]

    def ladderLength(self, beginWord: str, endWord: str, wordList: List[str]) -> int:
        if endWord not in wordList:
            return 0

        word_neighbors = WordNeighbors()
        all_words = self.get_all_words(wordList, beginWord)
        for word in all_words:
            word_neighbors.add(word)
        
        queue = deque([(beginWord, 1)])
        visited = {beginWord}
        while queue:
            word, length = queue.popleft()
            if word == endWord:
                return length
            
            for neighbor in word_neighbors.get_neighbor(word):
                if neighbor in visited:
                    continue
                visited.add(neighbor)
                queue.append((neighbor, length + 1))
        return 0
```

## 2回目
endWordも含まれてないときに追加するようにした
問題の制約上あり得ないけど、このほうが親切
->実装した後に思ったけど、wordListにendWordがない場合は0を返す必要があるのでダメだな。最初に早期returnしているから実行されないコードになってしまっている。

```python
from collections import defaultdict, deque
from typing import Iterator


class WordNeighbors:
    def __init__(self):
        self.key_to_neighbors = defaultdict(list)
    
    def to_key(self, word: str) -> Iterator[tuple]:
        for i in range(len(word)):
            yield (word[:i], word[(i+1):])
    
    def add(self, word: str) -> None:
        for key in self.to_key(word):
            self.key_to_neighbors[key].append(word)
    
    def get_neighbor(self, word: str) -> Iterator[str]:
        for key in self.to_key(word):
            for neighbor in self.key_to_neighbors[key]:
                yield neighbor


class Solution:
    def ladderLength(self, beginWord: str, endWord: str, wordList: List[str]) -> int:
        if endWord not in wordList:
            return 0
        
        all_words = wordList
        if beginWord not in all_words:
            all_words.append(beginWord)
        if endWord not in all_words:
            all_words.append(endWord)
        
        word_neighbors = WordNeighbors()
        for word in all_words:
            word_neighbors.add(word)
        
        queue = deque([(beginWord, 1)])
        visited = {beginWord}
        while queue:
            word, length = queue.popleft()
            if word == endWord:
                return length
            
            for neighbor in word_neighbors.get_neighbor(word):
                if neighbor in visited:
                    continue
                visited.add(neighbor)
                queue.append((neighbor, length + 1))
        return 0
```

## 3回目
get_neighborsと複数形にした

```python
from collections import defaultdict, deque
from typing import Iterator

class WordNeighbors:
    def __init__(self) -> None:
        self.key_to_neighbors = defaultdict(list)
    
    def to_key(self, word: str) -> Iterator[tuple]:
        for i in range(len(word)):
            yield (word[:i], word[(i+1):])
    
    def add(self, word: str) -> None:
        for key in self.to_key(word):
            self.key_to_neighbors[key].append(word)
    
    def get_neighbors(self, word: str) -> Iterator[str]:
        for key in self.to_key(word):
            for neighbor in self.key_to_neighbors[key]:
                yield neighbor

class Solution:
    def ladderLength(self, beginWord: str, endWord: str, wordList: List[str]) -> int:
        if endWord not in wordList:
            return 0
        
        all_words = wordList
        if beginWord not in all_words:
            all_words.append(beginWord)
        
        word_neighbors = WordNeighbors()
        for word in all_words:
            word_neighbors.add(word)
        
        queue = deque([(beginWord, 1)])
        visited = {beginWord}
        while queue:
            word, length = queue.popleft()
            if word == endWord:
                return length
            
            for neighbor in word_neighbors.get_neighbors(word):
                if neighbor in visited:
                    continue
                visited.add(neighbor)
                queue.append((neighbor, length + 1))
        return 0

```