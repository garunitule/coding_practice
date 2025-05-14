# 進め方
- step1: 5分考えて分からなかったら答えを見る。答えを理解したら、答えを隠して書く。筆が進まず5分立ったら答えを見る。答えを送信して正解するまで。
- step2: コードを読みやすく整える。動くコードになったら終了。
- step3: 時間を計りながら書く。10分以内に3回連続でアクセプトされるまで。

# step1: 15分
## 方針を考える
n: strsの長さ
m: strs[i]の長さ
とする。

key: strs[i]を昇順にソートしたもの
value: 並び替えてkeyになるstrs[i]のリスト
という辞書に保持すれば、
時間計算量はO(nmlogm)で可能

setを使えばもっと早いか？
ただ、setにする時間計算量やset自体がハッシュ可能かどうか覚えていない。

いったんソートの方法でやってみる。
ここまで5分。

## 実装
実装は8分ほどかかった。
ただ、ソートした文字列を結合する方法が分からなくて調べた。

```python
from collections import defaultdict


class Solution:
    def groupAnagrams(self, strs: List[str]) -> List[List[str]]:
        anagram_dict = defaultdict(list)
        for s in strs:
            sorted_s = ''.join(sorted(s))
            anagram_dict[sorted_s].append(s)
        
        result = []
        for anagrams in anagram_dict.values():
            result.append(anagrams)
        return result

```


## （必要に応じて）最適化
keyにsetを使えばもっと早そう。
set化にかかる時間とハッシュ可能か調査する。

https://docs.python.org/3/glossary.html#term-hashable
コンテナ型の場合、immutableなコンテナ型（tupleやfrozenset）がハッシュ可能

https://github.com/python/cpython/blob/1fb7e2aeb7e4312b7f20f0d5f39ddd00d7762004/Objects/setobject.c#L13C4-L20C25
依然調べたようにsetはopen addressingなハッシュテーブル

frozensetを使った実装。
テストケース通らない。
よく考えたらSetは重複を消してしまうので、
`["ddddddddddg","dgggggggggg"]` 
のようなケースで失敗してしまう。
※frozensetを利用してるのにsorted_sのままにしているが、いったんfrozensetで動くか試したかったのでそのままにしている。
通っていたら変数名は変える想定だった。

```python
from collections import defaultdict


class Solution:
    def groupAnagrams(self, strs: List[str]) -> List[List[str]]:
        anagram_dict = defaultdict(list)
        for s in strs:
            sorted_s = ''.join(frozenset(s))
            anagram_dict[sorted_s].append(s)
        
        result = []
        for anagrams in anagram_dict.values():
            result.append(anagrams)
        return result

```

## テストケースを考える
leetcode上の例以外だと、重複文字があったケースもあるとよさそう

# step2: 30分
## 時間の関係でstep1で書けなかった迷いや判断を書く
- sorted_s, anagram_dictという変数名どうだろう

## 自分でコード読みやすく整えて実装
- 自分で実装最適化するよりも、他の人のコメントを見てみたい

## 他の人のPRやコメントを踏まえて実装
https://leetcode.com/problems/group-anagrams/editorial/
leetcodeの解答
keyを(0, 0, 0, 0, 0, ..., 0)と各アルファベットをインデックス？にして管理する方法がある。
自分はsetでやろうとしたけど、こういった方法があるのか。

```
list(ans.values())
```
という実装があるけど、valuesってイテレータを返すのか？
dict_valuesを返していた。
https://docs.python.org/3/c-api/dict.html#c.PyDict_Values
Cで定義されたオブジェクトっぽい？


https://github.com/tokuhirat/LeetCode/pull/12/files
`anagram_group_to_words` という変数名わかりやすいな。

https://github.com/kazukiii/leetcode/pull/13/files
frozenset使ってる人いた。
アルファベットをインデックスにする手法と合わせてだけど

countで管理する方法を選択した
```python
from collections import defaultdict


class Solution:
    def groupAnagrams(self, strs: List[str]) -> List[List[str]]:
        anagram_group_to_words = defaultdict(list)
        for s in strs:
            alphabet_count = [0] * 26
            for c in s:
                alphabet_count[ord(c) - ord("a")] += 1
            anagram_group_to_words[tuple(alphabet_count)].append(s)
        return list(anagram_group_to_words.values())
```

# step3: 15分
※間違えがあればn回目を増やす

## 1回目
```python
from collections import defaultdict


class Solution:
    def groupAnagrams(self, strs: List[str]) -> List[List[str]]:
        anagram_group_to_words = defaultdict(list)
        for s in strs:
            alphabet_count = [0] * 26
            for c in s:
                alphabet_count[ord(c) - ord("a")] += 1
            anagram_group_to_words[tuple(alphabet_count)].append(s)
        return list(anagram_group_to_words.values())
```

## 2回目
```python
from collections import defaultdict


class Solution:
    def groupAnagrams(self, strs: List[str]) -> List[List[str]]:
        anagram_group_to_words = defaultdict(list)
        for s in strs:
            alphabet_count = [0] * 26
            for c in s:
                alphabet_count[ord(c) - ord("a")] += 1
            anagram_group_to_words[tuple(alphabet_count)].append(s)
        return list(anagram_group_to_words.values())
```

## 3回目
```python
from collections import defaultdict


class Solution:
    def groupAnagrams(self, strs: List[str]) -> List[List[str]]:
        anagram_group_to_words = defaultdict(list)
        for s in strs:
            alphabet_count = [0] * 26
            for c in s:
                alphabet_count[ord(c) - ord("a")] += 1
            anagram_group_to_words[tuple(alphabet_count)].append(s)
        return list(anagram_group_to_words.values())
```