# 進め方
- step1: 5分考えて分からなかったら答えを見る。答えを理解したら、答えを隠して書く。筆が進まず5分立ったら答えを見る。答えを送信して正解するまで。
- step2: コードを読みやすく整える。動くコードになったら終了。
- step3: 時間を計りながら書く。10分以内に3回連続でアクセプトされるまで。

# step1: 15分
## 方針を考える
シンプルに「各emailに.と+のルールを適用して送信先を求め、その送信先が初出の場合にカウントする」だと思った

各メールアドレスに対して下記を繰り返す
- `@`で区切り、local nameとdomain nameを取得
- localnameの`.`は無視する
- localnameの`+`が登場したらそれ以降無視する
- `.`と`+`のルールを適用したlocal nameとdomain nameから送信先を求める
- すでにあるかどうか判定し、なければカウントする

emailsの要素数をn, emailの平均の長さをｍとすれば、最悪時間計算量O(nm)になる

## 実装
6分くらい

```python
class Solution:
    def numUniqueEmails(self, emails: List[str]) -> int:
        recipients = set()
        for email in emails:
            local_name, domain_name = email.split('@')
            recipient_local_name = ''
            for i in range(len(local_name)):
                if local_name[i] == '.':
                    continue
                if local_name[i] == '+':
                    break
                recipient_local_name += local_name[i]
            recipient = recipient_local_name + '@' + domain_name
            if recipient in recipients:
                continue
            recipients.add(recipient)
        return len(recipients)
```

## （必要に応じて）最適化
これ以上計算を早くする方法は思いつかない
`recipient_local_name`の取得はもっと短くできるかも？

例えば下記のように書ける
こう書くなら最初に書いた実装のほうが分かりやすいか
両方の処理で要素全てを走査しているはずなので2倍かかる
```python
# .を取り除く
local_name = ''.join(local_name.split('.'))
# +以降を無視する
plus_index = local_name.find('+')
if plus_index != -1
    recipient_local_name = local_name[:plus_index]
```

## テストケースを考える
- ローカル名とドメイン名の区切り以外に`@`は使えるのか？
https://datatracker.ietf.org/doc/html/rfc5322
RFC5322によれば、引用符`"`で囲われている場合は利用できるらしい
空白なども使える
通常の`dot-atom`に対して`quoted-string`と呼ぶ
引用符`"`を利用しなくてもよい場合は利用すべきでないとのこと
実際にはローカル名が `quoted-string`の場合も想定する必要がありそう

- メールアドレスの最大の長さは？
https://tex2e.github.io/rfc-translater/html/rfc5321.html
ローカルパート：64オクテット
ドメイン：255オクテット
リバースパス or フォワードパス：255オクテット
ascii文字なら255文字か


# step2: 30分
## 時間の関係でstep1で書けなかった迷いや判断を書く
特にない

## 自分でコード読みやすく整えて実装
step1で整えて書いたので特にない

## 他の人のPRやコメントを踏まえて実装
https://github.com/SuperHotDogCat/coding-interview/pull/30#discussion_r1646552062
>この問題では @ は1つですが、一応、RFC 上は、複数持てるので、これでいいんじゃないでしょうか。
(RFC を見よう、標準マニュアルを確認しようという教育的な意図も入っていますが。)
>
>あと、本当はセキュリティー上、メールアドレスはユーザーから渡されるものである可能性が高く、ゴミを渡されたときに落ちるプログラムにしてはいけないですね。

たしかにユーザーから渡されるものの可能性が高いので変なものを渡されても落ちないとか変な挙動しないようにする必要があるな
例えばローカルパートがRFC5322の`quoted-string`形式だった場合とか

https://github.com/Yoshiki-Iwasa/Arai60/pull/13#discussion_r1649832719
>とりあえず、ユースケースの想定ですね。これ、そもそもなんでこんなものを作りたいんだと思いますか。
>たとえば、これ、マーケティングのメールを送りたいのか、ある集団のやりとりを整理したいのか。
>つまり、たとえば、誤った入力が一つ入ったときに、全体として、そこそこ動いて欲しいのか、異常だといって止まって欲しいのか。それは何をしたいのかとの兼ね合いになるでしょう。

そもそもなんで作りたいんだっけ？の視点は抜けていた
他にも
- 多重応募の排除（1人1回にする）
- 顧客データの名寄せ
- ユーザー単位の集計
とかがありそう
抽象度を上げれば「メールアドレスの紐づけ先の人をユニークにする」なので、その活用例として、送信先の特定 / 応募の重複判定 / 顧客やユーザーをユニークにするがあるという認識

https://discord.com/channels/1084280443945353267/1307605446538039337/1333100090054934628
`normalize`のほうが分かりやすいか
あと関数化した方がよいか

https://github.com/plushn/SWE-Arai60/pull/14#discussion_r2051710985
>上記でも言及されていますが、ここの文字列結合は推奨されません。
文字列結合時に文字列の再構築が走るので、n個の文字になるように1個ずつ文字をを連結しようとすると、
>1 + 2 + 3 + .... + n -> n*(n+1)/2でO(n^2)の操作になるという背景です

知らなかった
https://docs.python.org/ja/3.13/library/stdtypes.html#text-sequence-type-str
そもそもstrがイミュータブルなことも知らなかった
で、イミュータブルだから変更できず別領域にコピーしてから追加されるのでコメントのとおりO(n^2)の走査になるってことか

https://github.com/tokuhirat/LeetCode/pull/14/files
`.`の置換はreplaceもあるな
`+`以降の無視はsplitでもいい


## コメントを踏まえた実装
### step1に似た実装。ただし「+=」を利用せず結合する
```python
class Solution:
    def numUniqueEmails(self, emails: List[str]) -> int:
        def normalize(email: str) -> str:
            local_part, domain = email.split('@')
            local_part_chars = []
            for c in local_part:
                if c == '.':
                    continue
                if c == '+':
                    break
                local_part_chars.append(c)
            return ''.join(local_part_chars) + '@' + domain

        normalized_emails = set()
        for email in emails:
            normalized_emails.add(normalize(email))
        return len(normalized_emails)
```

### replace, findを使った実装
```python
class Solution:
    def numUniqueEmails(self, emails: List[str]) -> int:
        def normalize(email: str) -> str:
            local_part, domain = email.split('@')
            normalized_local_part = local_part.replace('.', '')
            plus_index = normalized_local_part.find('+')
            if plus_index != -1:
                normalized_local_part = normalized_local_part[:plus_index]
            return normalized_local_part + '@' + domain
        
        normalized_emails = set()
        for email in emails:
            normalized_emails.add(normalize(email))
        return len(normalized_emails)
```

# step3: 15分
少し迷ったが、1文字ずつ走査する方法を選択した。
replace, findを使った方法よりもルールを直接的に反映できていると思ったので。

## 1回目
```python
class Solution:
    def numUniqueEmails(self, emails: List[str]) -> int:
        def normalize(email: str) -> str:
            local_part, domain = email.split('@')
            normalized_local_part_chars = []
            for c in local_part:
                if c == '.':
                    continue
                if c == '+':
                    break
                normalized_local_part_chars.append(c)
            return ''.join(normalized_local_part_chars) + '@' + domain
        
        normalized_emails = set()
        for email in emails:
            normalized_emails.add(normalize(email))
        return len(normalized_emails)
```

## 2回目
```python
class Solution:
    def numUniqueEmails(self, emails: List[str]) -> int:
        def normalize(email: str) -> str:
            local_part, domain = email.split('@')
            normalized_local_part_chars = []
            for c in local_part:
                if c == '.':
                    continue
                if c == '+':
                    break
                normalized_local_part_chars.append(c)
            return ''.join(normalized_local_part_chars) + '@' + domain

        normalized_emails = set()
        for email in emails:
            normalized_emails.add(normalize(email))
        return len(normalized_emails)    
```

## 3回目
```python
class Solution:
    def numUniqueEmails(self, emails: List[str]) -> int:
        def normalize(email: str) -> str:
            local_part, domain = email.split('@')
            normalized_local_part_chars = []
            for c in local_part:
                if c == '.':
                    continue
                if c == '+':
                    break
                normalized_local_part_chars.append(c)
            normalized_local_part = ''.join(normalized_local_part_chars)
            return normalized_local_part + '@' + domain
        
        normalized_emails = set()
        for email in emails:
            normalized_emails.add(normalize(email))
        return len(normalized_emails)
```