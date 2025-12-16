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