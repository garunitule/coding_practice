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

もっと良い解法があるかもしれないが思いつかないので、いったん解法を見てみる。

# step2: 30分

別のlistを用意して詰めるだけでよかった。

```python
class Solution:
    def moveZeroes(self, nums: List[int]) -> None:
        """
        Do not return anything, modify nums in-place instead.
        """
        non_zero_values = [num for num in nums if num != 0]
        nums[:len(non_zero_values)] = non_zero_values
        nums[len(non_zero_values):] = [0] * (len(nums) - len(non_zero_values))
```

別のlistを用意しない方法もあった。
numsを順番に走査していく。
非ゼロの要素が登場したらnext_non_zero_indexとスワップし、next_non_zero_indexをインクリメントする方法。
不変条件
- next_non_zero_indexより左は全て非ゼロ。
- next_non_zero_indexからcurrentの前までは全てゼロ。
current - 1までは作りたい状態になっていて、次のcurrentでもその状態になるように操作する。
これを繰り返す。

動かないコード。[1]を受け取ったときに[0]を返す。

```python
class Solution:
    def moveZeroes(self, nums: List[int]) -> None:
        """
        Do not return anything, modify nums in-place instead.
        """

        next_non_zero_index = 0
        for i in range(len(nums)):
            if nums[i] == 0:
                continue
            non_zero_value = nums[i]
            nums[next_non_zero_index] = non_zero_value
            nums[i] = 0
            next_non_zero_index += 1
```


不変条件は
- 0 <= index < first_zero_index: 非ゼロ領域
- first_zero_index <= index < i: ゼロ領域
- i <= index: 未調査領域
と書ける。
個人的にはswapを使うよりも下記のように考えたほうが分かりやすい。
- nums[i] == 0のとき：何もしない。ゼロ領域が伸びるだけで不変条件は維持されるので。
- nums[i] != 0のとき
  - first_zero_index == 1：非ゼロ領域と未調査領域しかない。現在の要素を未調査領域から非ゼロ領域にすればいいので、first_zero_indexをインクリメントする。
  - first_zero_index < i: 非ゼロ領域、ゼロ領域、未調査領域がある。現在の要素を非ゼロ領域の末尾に移動し、現在の位置はゼロ領域とする。


```python
class Solution:
    def moveZeroes(self, nums: List[int]) -> None:
        """
        Do not return anything, modify nums in-place instead.
        不変条件
        - 0 <= index < first_zero_index: 非ゼロ領域
        - first_zero_index <= index < i: ゼロ領域
        - i <= index: 未調査領域
        """

        first_zero_index = 0
        for i in range(len(nums)):
            # 0のときは何もせず進んでも不変条件が守られるため何もしない
            if nums[i] == 0:
                continue
            # ゼロ領域がある場合は、非ゼロ領域を伸ばしてそのあとゼロ領域を後ろにずらす
            # ゼロ領域がない場合は、非ゼロ領域を伸ばす
            if first_zero_index < i:
                nums[first_zero_index] = nums[i]
                nums[i] = 0
            first_zero_index += 1
```

# step3: 15分
※間違えがあればn回目を増やす

## 1回目
```python
class Solution:
    def moveZeroes(self, nums: List[int]) -> None:
        """
        Do not return anything, modify nums in-place instead.
        """
        first_zero_index = 0
        for i in range(len(nums)):
            if nums[i] == 0:
                continue
            if first_zero_index < i:
                nums[first_zero_index] = nums[i]
                nums[i] = 0
            first_zero_index += 1
```

## 2回目
```python
class Solution:
    def moveZeroes(self, nums: List[int]) -> None:
        """
        Do not return anything, modify nums in-place instead.
        不変条件
        - 0 <= index < first_zero_index: 非ゼロ領域
        - first_zero_index <= index < i: ゼロ領域
        - i <= index: 未調査領域
        """
        first_zero_index = 0
        for i in range(len(nums)):
            if nums[i] == 0:
                continue
            if first_zero_index < i:
                nums[first_zero_index] = nums[i]
                nums[i] = 0
            first_zero_index += 1
```

## 3回目
```python
class Solution:
    def moveZeroes(self, nums: List[int]) -> None:
        """
        Do not return anything, modify nums in-place instead.
        """
        first_zero_index = 0
        for i in range(len(nums)):
            if nums[i] == 0:
                continue
            if first_zero_index < i:
                nums[first_zero_index] = nums[i]
                nums[i] = 0
            first_zero_index += 1
```

## step3の後に考えたこと
first_zero_indexという変数名が分かりづらい。
もっと不変条件が自然に想起されるような名前にしたい。

https://github.com/mamo3gr/arai60/pull/51/changes
max_non_zero_index
非ゼロ領域に関して命名するか、ゼロ領域に関して命名するかの2択と思うと、これ以上はなさそう。