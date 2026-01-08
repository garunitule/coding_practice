# 進め方
- step1: 5分考えて分からなかったら答えを見る。答えを理解したら、答えを隠して書く。筆が進まず5分立ったら答えを見る。答えを送信して正解するまで。
- step2: コードを読みやすく整える。動くコードになったら終了。
- step3: 時間を計りながら書く。10分以内に3回連続でアクセプトされるまで。

# step1: 15分
n: 文字列の長さ
O(n ** 2)ならできるが、n <= 5 * 10 ** 4なのでTLEしそう。
秒間1000万ステップだとしても25秒かかる計算になるため。

と思ったがTLEしなかった。
```python
class Solution:
    def lengthOfLongestSubstring(self, s: str) -> int:
        result = 0
        for start in range(len(s)):
            seen_charactor = set()
            length = 0
            for i in range(start, len(s)):
                if s[i] in seen_charactor:
                    break
                length += 1
                result = max(result, length)
                seen_charactor.add(s[i])
        
        return result
```

今のプログラムは毎回seen_charactorを作成しているが、前回のループから引き継げそう。
そうすれば時間計算量はO(n)に落とせる。


書けた。
ただ手順を整理しきれてなかったせいかごちゃごちゃしてしまった。
start, endを回すループを分けた方が良かったかもしれない。
解答を確認する。
```python
class Solution:
    def lengthOfLongestSubstring(self, s: str) -> int:
        if not s:
            return 0

        max_length = 1
        start = 0
        end = 1
        seen_charactor = set()
        while start < len(s) and end < len(s):
            seen_charactor.add(s[start])
            if s[end] in seen_charactor:
                seen_charactor.remove(s[start])
                start += 1
                if start == end:
                    end += 1
                continue
            max_length = max(max_length, end - start + 1)
            seen_charactor.add(s[end])
            end += 1
        
```


# step2: 30分
leetcodeの解答を見た。
アプローチ1の解答はstart, endで毎回文字列作ってるからO(n ** 3)かかる。

スライディングウィンドウの実装。自分の実装よりもわかりやすい。
各endに対して重複がないかつ最長の部分文字列ができるようになっている。
↑このように端的に手順や不変条件を言語化できてないのと実装がごちゃごちゃする。

```python
from collections import Counter


class Solution:
    def lengthOfLongestSubstring(self, s: str) -> int:
        start = end = 0
        max_length = 0
        seen_charactor_counter = Counter()
        while end < len(s):
            seen_charactor_counter[s[end]] += 1
            while seen_charactor_counter[s[end]] > 1:
                seen_charactor_counter[s[start]] -= 1
                start += 1
            max_length = max(max_length, end - start + 1)
            end += 1

        return max_length
```

各ループで、endに対して重複しない最小のstartを保持している。
start = max(start, char_to_next_index[s[end]])である理由は、startより左は何かしら重複があって捨てた領域のためと理解しているが、何となく腑に落ちていない。2分探索の不変条件ほどしっくりきていない感覚。

```python
class Solution:
    def lengthOfLongestSubstring(self, s: str) -> int:
        start = end = 0
        max_length = 0
        char_to_next_index = {}
        while end < len(s):
            if s[end] in char_to_next_index:
                start = max(start, char_to_next_index[s[end]])
            max_length = max(max_length, end - start + 1)
            char_to_next_index[s[end]] = end + 1
            end += 1

        return max_length
```

# step3: 15分
※間違えがあればn回目を増やす

## 1回目
```python
class Solution:
    def lengthOfLongestSubstring(self, s: str) -> int:
        start = end = 0
        max_length = 0
        char_to_next_index = {}
        while end < len(s):
            if s[end] in char_to_next_index:
                start = max(start, char_to_next_index[s[end]])
            max_length = max(max_length, end - start + 1)
            char_to_next_index[s[end]] = end + 1
            end += 1

        return max_length
```

## 2回目
```python
class Solution:
    def lengthOfLongestSubstring(self, s: str) -> int:
        start = end = 0
        max_length = 0
        char_to_next_index = {}
        while end < len(s):
            if s[end] in char_to_next_index:
                start = max(start, char_to_next_index[s[end]])
            max_length = max(max_length, end - start + 1)
            char_to_next_index[s[end]] = end + 1
            end += 1

        return max_length
```

## 3回目
```python
class Solution:
    def lengthOfLongestSubstring(self, s: str) -> int:
        start = end = 0
        max_length = 0
        char_to_next_index = {}
        while end < len(s):
            if s[end] in char_to_next_index:
                start = max(start, char_to_next_index[s[end]])
            max_length = max(max_length, end - start + 1)
            char_to_next_index[s[end]] = end + 1
            end += 1

        return max_length
```