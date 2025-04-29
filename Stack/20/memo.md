## 取り組み方
step1: 5分考えて分からなかったら答えを見る。答えを理解したら、答えを隠して書く。筆が進まず5分立ったら答えを見る。答えを送信して正解するまで。
step2: コードを読みやすく整える。動くコードになったら終了。
step3: 時間を計りながら書く。10分以内に3回連続でアクセプトされるまで。

## step1
ルール1: 開いたかっこは対応するかっこで閉じる必要がある
-> 開いたけど対応する閉じかっこがない場合はダメ
ルール2: 開いたかっこは正しい順番で閉じる必要がある。最後に開いたかっこから閉じる
-> 最後に開いたかっこに対応しない閉じかっこがきたらダメ
ルール3: 全ての閉じかっこは対応する開くかっこが存在する必要がある
-> 対応する開くかっこがない閉じかっこはダメ

ルール2により、閉じかっこが最後に開いたかっこに対応する必要があるのでstackで開いたかっこを管理するのがよさそう。
順番に取り出して、
- 開くかっこの場合：stackに積む
- 閉じかっこの場合：stackから取り出したかっこに対応するか調査。対応しなければその時点でFalse返す
一通り走査して要素が空になっていればTrue。そうじゃなければルール1に違反するのでFalse。

ここまで言葉にするくらいなら実装した方が早かったかも。

大体5分くらいで実装
```python
class Solution:
    def isValid(self, s: str) -> bool:
        stack = []
        close_open_mapping = {
            ")": "(",
            "]": "[",
            "}": "{"
        }

        for c in s:
            if c in close_open_mapping:
                if len(stack) < 1:
                    return False

                topElement = stack.pop()
                if topElement != close_open_mapping[c]:
                    return False
            else:
                stack.append(c)

        return len(stack) == 0
```

## step2
### step1で気になったこと
最後の
```
return len(stack) == 0
```
の部分。

```
return not stack
```
とも書けるはずなのでどちらが良いのか。

https://peps.python.org/pep-0008/#:~:text=For%20sequences%2C%20(strings%2C%20lists%2C%20tuples)%2C%20use%20the%20fact%20that%20empty%20sequences%20are%20false
を見ると、lenではなくシーケンスが空になることを利用せよとのこと。



### 他の人のPRを見てみる
https://github.com/tokuhirat/LeetCode/pull/6/files
ガード節使う方法もあるか。
自分の感覚としては、異常系をはじくとか最初にはじいた方がよい場合に使うイメージなので、単にパターン分けされているだけであればif文のほうが分かりやすい気はするが、ここは好みかな。

https://discord.com/channels/1084280443945353267/1225849404037009609/1231648833914802267
> "(aiu)[eo]" が入力としてきたときに、プログラムの挙動として好ましいのは何だと考えますか?

PRじゃないけど気になったコメント。
このメソッドがどのように利用されるか次第だと思う。
例えば、あるルールに則って文章に「()[]{}」で装飾している場合の妥当性を検証する用途なら、"(aiu)[eo]"が入力された場合に「()[]{}」以外はスキップして妥当性を判定すべき。
実際のケースだと「()[]{}」だけ含まれた文字列を判定したいとうよりも他の文字が含まれた場合のほうが多そう。

https://github.com/koko5959/leetcode-practice/pull/3/files#r2035039298
open_to_closeで実装するのもあるか。
あと変数名 `${key}_to_${value}` の形式分かりやすい。


### 上記を踏まえて整えたコード
```python
class Solution:
    def isValid(self, s: str) -> bool:
        stack = []
        close_to_open = {
            ")": "(",
            "]": "[",
            "}": "{"
        }

        for c in s:
            if c in close_to_open:
                if not stack:
                    return False
                
                actualBracket = stack.pop()
                if actualBracket != close_to_open[c]:
                    return False
            else:
                stack.append(c)

        return not stack
```

## step3
```python
class Solution:
    def isValid(self, s: str) -> bool:
        stack = []
        close_to_open = {
            ")": "(",
            "]": "[",
            "}": "{"
        }

        for c in s:
            if c in close_to_open:
                if not stack:
                    return False
                
                actualOpenBracket = stack.pop()
                if actualOpenBracket != close_to_open[c]:
                    return False
            else:
                stack.append(c)
        
        return not stack
```