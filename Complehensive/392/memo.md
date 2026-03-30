# 進め方
- step1: 5分考えて分からなかったら答えを見る。答えを理解したら、答えを隠して書く。筆が進まず5分立ったら答えを見る。答えを送信して正解するまで。
- step2: コードを読みやすく整える。動くコードになったら終了。
- step3: 時間を計りながら書く。10分以内に3回連続でアクセプトされるまで。

# step1: 15分
## 方針を考える
愚直に実装するならtのそれぞれの文字を選ぶ・選ばないで全ての部分文字列を作る。
この方法だとO(2 ** t.length)なので時間がかかりすぎる。

動的計画法で解けそう？
いや、そんなことしなくてもよさそう。
sとtの現在のindexをそれぞれ管理して、tを順次進めていく。
s[s_index]とt[t_index]が一致したら、s_indexを進める。
sが部分文字列になるなら、s_indexはlen(s)と等しくなる。
ので、return s_index == len(s)でよい。

時間計算量としてはO(t.length)で完了できる。
各文字を操作する必要があるので、これが一番早いはず。


## 実装

動かないコード。`""`の入力を想定できていなかった。
```python
class Solution:
    def isSubsequence(self, s: str, t: str) -> bool:
        s_index = 0
        for t_index in range(len(t)):
            if s[s_index] == t[t_index]:
                s_index += 1

        return s_index == len(s)
```

動くコード。
```python
class Solution:
    def isSubsequence(self, s: str, t: str) -> bool:
        if len(s) == 0:
            return True

        s_index = 0
        for t_index in range(len(t)):
            if s[s_index] != t[t_index]:
                continue
            s_index += 1
            if s_index == len(s):
                return True                
        return False
```

もっとシンプルにした。
```python
class Solution:
    def isSubsequence(self, s: str, t: str) -> bool:
        s_index = 0
        t_index = 0
        while s_index < len(s) and t_index < len(t):
            if s[s_index] == t[t_index]:
                s_index += 1
            t_index += 1
        return s_index == len(s)
```

# step2: 30分
フォローアップや他にも解法がありそうだが、一周したいので後で戻ってくる。

# step3: 15分
※間違えがあればn回目を増やす

## 1回目
```python
class Solution:
    def isSubsequence(self, s: str, t: str) -> bool:
        s_index = 0
        t_index = 0
        while s_index < len(s) and t_index < len(t):
            if s[s_index] == t[t_index]:
                s_index += 1
            t_index += 1
        
        return s_index == len(s)
```

## 2回目
```python
class Solution:
    def isSubsequence(self, s: str, t: str) -> bool:
        s_index = 0
        t_index = 0
        while s_index < len(s) and t_index < len(t):
            if s[s_index] == t[t_index]:
                s_index += 1
            t_index += 1
        
        return s_index == len(s)
```

## 3回目
```python
class Solution:
    def isSubsequence(self, s: str, t: str) -> bool:
        s_index = 0
        t_index = 0
        while s_index < len(s) and t_index < len(t):
            if s[s_index] == t[t_index]:
                s_index += 1
            t_index += 1
        
        return s_index == len(s)
```