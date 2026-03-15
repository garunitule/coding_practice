# 進め方
- step1: 5分考えて分からなかったら答えを見る。答えを理解したら、答えを隠して書く。筆が進まず5分立ったら答えを見る。答えを送信して正解するまで。
- step2: コードを読みやすく整える。動くコードになったら終了。
- step3: 時間を計りながら書く。10分以内に3回連続でアクセプトされるまで。

# step1: 15分
## 方針を考える
インプレースという制限がある。
要素を走査して0だったら末尾になるまで隣り合った要素と入れ替えていくという手順で出来そう。
この場合は時間計算量としてはO(n ** 2)になる。
これ以上落とす場合はO(n)とかO(nlogn)になりそう。ソートを使えばO(nlogn)にできそうだが、0以外の要素の並び順も変わってしまうので使えない。
いったん入れ替え方式でやってみよう。
最大の要素数が10**4なので、10**8程度の計算回数。Pythonであれば1から10秒のオーダーで終わりそう。
仮に1000万ステップ実行できると仮定した。
https://github.com/Yuto729/LeetCode_arai60/pull/16#discussion_r2602118324

## 実装

最初の実装（動かないです）
[0, 0, 1]のテストケースで失敗した。
i番目に対して入れ替えを実施したときにi+1番目以降が確定すると思っていたけどそうじゃないケースがある。

```python
class Solution:
    def moveZeroes(self, nums: List[int]) -> None:
        """
        Do not return anything, modify nums in-place instead.
        """
        for i in range(len(nums)):
            if nums[i] != 0:
                continue
            for j in range(i, len(nums) - 1):
                prev_num = nums[j]
                next_num = nums[j + 1]
                nums[j] = next_num
                nums[j + 1] = prev_num
```

i番目が0じゃないことを確定させてから次に進むように修正した。
5分くらい。
```python
class Solution:
    def moveZeroes(self, nums: List[int]) -> None:
        """
        Do not return anything, modify nums in-place instead.
        """
        i = 0
        moved_zero_count = 0
        while i < len(nums) - moved_zero_count:
            if nums[i] != 0:
                i += 1
                continue
            for j in range(i, len(nums) - 1):
                prev_num = nums[j]
                next_num = nums[j + 1]
                nums[j] = next_num
                nums[j + 1] = prev_num
            moved_zero_count += 1
```

もっと良い解法があるかもしれないがいったん解法を見てみる。

# step2: 30分
## 時間の関係でstep1で書けなかった迷いや判断を書く

## 自分でコード読みやすく整えて実装

## 他の人のPRやコメントを踏まえて実装

# step3: 15分
※間違えがあればn回目を増やす

## 1回目

## 2回目

## 3回目