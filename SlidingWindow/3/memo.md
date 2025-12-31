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



# step2: 30分
## 時間の関係でstep1で書けなかった迷いや判断を書く

## 自分でコード読みやすく整えて実装

## 他の人のPRやコメントを踏まえて実装

# step3: 15分
※間違えがあればn回目を増やす

## 1回目

## 2回目

## 3回目