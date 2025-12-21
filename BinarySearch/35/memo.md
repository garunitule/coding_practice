# 進め方
- step1: 5分考えて分からなかったら答えを見る。答えを理解したら、答えを隠して書く。筆が進まず5分立ったら答えを見る。答えを送信して正解するまで。
- step2: コードを読みやすく整える。動くコードになったら終了。
- step3: 時間を計りながら書く。10分以内に3回連続でアクセプトされるまで。

# step1: 15分
bisect_leftを使えばできそうだが、自分で書くのをこころみた
通るコード書けなかった

# step2: 30分
一致したら返すで良いか、たしかに。
同じ数が続いていたらどうするかとかも考えてしまっていた。

targetがleftとrightの間に存在あるなら残り続けるように更新している。
left, rightを狭めるときはtargetが含まれない範囲を除外している。
もしleft <= rightなので left == rightまでは探索するので、nums[mid] == target を逃すことはない。

```python
class Solution:
    def searchInsert(self, nums: List[int], target: int) -> int:
        left = 0
        right = len(nums) - 1
        while left <= right:
            mid = (left + right) // 2
            if nums[mid] == target:
                return mid
            if nums[mid] < target:
                left = mid + 1
            else:
                right = mid - 1

        return left
```

書き方に幅があると思うので、色んな人のPRを見てみよう

https://nuc.hatenadiary.org/entry/2025/11/29/#%E4%BA%8C%E5%88%86%E6%8E%A2%E7%B4%A2%E3%82%92%E8%AA%AD%E3%82%81%E3%82%8B%E3%81%8B

>このとき、本質的な情報は2つのみだ。「3月5日5番塔に登った。10だった。太郎。」「5月7日15番塔に登った。50だった。次郎。」という2枚のレポートがあったとして、5番塔の情報は、15番塔が5000未満と分かったらいらないのだ。そういうわけで、小さい側と大きい側の情報の2つだけあればいい。それをホワイトボードに書くときに、いくつかの書き方がある。「小さいと分かっている塔の中で最大の番号」でもいいし「情報がないのはここから」でもいい。同じ状況を表すのにどのように表現するかの方法は何通りかある。ただ、表現された数字が何を意味しているのかについてこのチームの中では共通理解がないとまずい。あとで揉め事になりうる。

left, rightで狭めていくくらいのイメージで書こうとしてしまっていたが、役割や管理したい情報を明確にする必要がある。
先ほど書いたコードだと、 
- left: targetかもしれないインデックスの中で最小のインデックス
- right: targetかもしれないインデックスの中で最大のインデックス
になる。leftより左はtarget未満だし、rightより右はtargetより大きいため。
途中で見つかったらそれを返す。
left > rightになって終了した時、right, left の順になっている。
leftより左はtarget未満、rightより右はtargetより大きいなことを踏まえると、leftが挿入位置になる。


https://github.com/yas-2023/leetcode_arai60/pull/21/files#r2596413649
分かりやすい
最後right, leftの順になるが、
rightより右側はtarget以上、leftより左側はtarget未満になるので、leftが挿入位置になる

```python
class Solution:
    def searchInsert(self, nums: List[int], target: int) -> int:
        left = 0
        right = len(nums) - 1
        while left <= right:
            mid = (left + right) // 2
            if nums[mid] < target:
                left = mid + 1
            else:
                right = mid - 1

        return left
```

# step3: 15分
※間違えがあればn回目を増やす

## 1回目
```python
class Solution:
    def searchInsert(self, nums: List[int], target: int) -> int:
        left = 0
        right = len(nums) - 1
        while left <= right:
            mid = (left + right) // 2
            if target <= nums[mid]:
                right = mid - 1
            else:
                left = mid + 1

        return left
```

## 2回目
```python
class Solution:
    def searchInsert(self, nums: List[int], target: int) -> int:
        left = 0
        right = len(nums) - 1
        while left <= right:
            mid = (left + right) // 2
            if target <= nums[mid]:
                right = mid - 1
            else:
                left = mid + 1

        return left
```

## 3回目
```python
class Solution:
    def searchInsert(self, nums: List[int], target: int) -> int:
        left = 0
        right = len(nums) - 1
        while left <= right:
            mid = (left + right) // 2
            if target <= nums[mid]:
                right = mid - 1
            else:
                left = mid + 1

        return left
```

# レビューをうけて
https://github.com/garunitule/coding_practice/pull/41#discussion_r2625314920
>二分探索については、ループ不変条件のバリエーション、それぞれの不変条件設定から両端点の初期化、更新式、ループ継続条件などの導出、二分探索の正当性を説明できるところまでがSWEの常識に含まれると思います。

自分の理解の確認を兼ねて言語化と実装をやってみた。

## 値を探す時
重複を考慮せず、値を見つけたら返す時
- 不変条件
  - leftより左はtargetより小さい
  - rightより右はtargetより大きい
- 変数の意味
  - left: targetでありうる最小の位置
  - tight: targetでありうつ最大の位置
- 初期値
  - left: 0
  - right: len(nums) - 1
    - len(nums)でもいいが、len(nums)に要素はないのでlen(nums) - 1で充分
- 更新方法
  - nums[mid] < targetならば、left= mid + 1
    - mid以前がtargetより小さいなら、不変条件よりleft = mid + 1に更新できる
  - nums[mid] > targetならば、right = mid - 1
    - leftと同様に不変条件から
- 終了条件
  - nums[mid] == targetならmidを返す
  - left > rightのとき、left以上right以下が候補のはずなので、left > rightになったら候補が空になったといえる。終了時はright = left - 1になっている。不変条件から、rightはnums[i]がtargetより小さい中で最大のインデックス、leftはnums[i]がtargeより大きい中で最小のインデックスなので、leftを返すべき。

```python
class Solution:
    def searchInsert(self, nums: List[int], target: int) -> int:
        left = 0
        right = len(nums) - 1
        while left <= right:
            mid = (left + right) // 2
            if nums[mid] == target:
                return mid
            if nums[mid] < target:
                left = mid + 1
            else:
                right = mid - 1
        
        return left
```

## bisect_right
例えば、[10, 11, 11, 11, 12]があって11を挿入できる位置を探す際に4を返す.
- 不変条件
  - leftより左はtarget以下
  - right以降はtargetより大きい
- 変数の意味
  - left: あり得る挿入位置の最小値
  - right: あり得る挿入位置の最大値
- 初期値
  - left: 0
  - right: len(nums)
    - 挿入位置の候補の最大値なので、len(nums) - 1にするとややこしい。例外的な扱いが必要になる。
- 更新方法（不変条件を維持しながら絞る）
  - nums[mid] <= targetならばleft = mid + 1
    - mid以前のnumsがtarget以下なので、mid + 1に更新できる
  - nums[mid] > targetならばright = mid
    - nums[mid - 1]はtarget以下の可能性があるので、midまでしか移動できない
- 終了条件
  - 不変条件より、left == rightが終了条件になる。継続条件はleft < right

実装してみた。今回の問題だと通らないケースもあるが、想定通り。
left, rightで実装されるコードを見るけど、left,rightだと役割を誤認する可能性があるので明確に記載してみた。
```python
class Solution:
    def searchInsert(self, nums: List[int], target: int) -> int:
        min_insertable_index = 0
        max_insertable_index = len(nums)
        while min_insertable_index < max_insertable_index:
            mid = (min_insertable_index + max_insertable_index) // 2
            if nums[mid] <= target:
                min_insertable_index = mid + 1
            else:
                max_insertable_index = mid

        return min_insertable_index
```

## bisect_left
例えば、[10, 11, 11, 11, 12]があって11を挿入できる位置を探す際に1を返す。
- 不変条件
  - leftより左はtargetより小さい
  - right以降はtarget以上
-  変数の意味
  - left: あり得る挿入位置の最小値
    right: あり得る挿入位置の最大値
- 初期値
  - left: 0
  - right: len(nums)
    - 挿入位置の候補の最大値なので、len(nums) - 1にするとややこしい。例外的な扱いが必要になる。
- 更新方法（不変条件を維持しながら絞る）
  - nums[mid] < targetならば、left = mid + 1
  - nums[mid] >= targetならば、right = mid
- 終了条件
  - left == rightが終了条件になる

```python
class Solution:
    def searchInsert(self, nums: List[int], target: int) -> int:
        min_insertable_index = 0
        max_insertable_index = len(nums)
        while min_insertable_index < max_insertable_index:
            mid = (min_insertable_index + max_insertable_index) // 2
            if nums[mid] < target:
                min_insertable_index = mid + 1
            else:
                max_insertable_index = mid

        return min_insertable_index
```