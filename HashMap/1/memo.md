# 進め方
- step1: 5分考えて分からなかったら答えを見る。答えを理解したら、答えを隠して書く。筆が進まず5分立ったら答えを見る。答えを送信して正解するまで。
- step2: コードを読みやすく整える。動くコードになったら終了。
- step3: 時間を計りながら書く。10分以内に3回連続でアクセプトされるまで。

# step1: 15分
## 方針を考える
numsの要素数をnとする。
2重のループを使えば
- 時間計算量：O(n**2)
- 空間計算量：追加なし

他の方法だとHashMapでkey: nums[i], value: iで管理すれば、
- 時間計算量：O(n)
- 空間計算量：O(n)
で可能。

他の方針は思いつかない。。
途中で答えが見つかったら終了できるが、見つけるまでは素直に探索するしかないと思う。

ここまで3分。

## 実装
値が重複することがあるので、
num: [i1, i2]
のような形式にする。

defaultdictを使いたい。標準だったか忘れたので調べた。collectionsの中だった。

最初に書いたコード。
これは下記のエラーが発生して動かなかった。
書いたのは5分、見直し2分。

>TypeError: 'list' object cannot be interpreted as an integer
             ^^^^^^^^^^^
    for i in range(nums):
Line 7 in twoSum (Solution.py)
          ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
    ret = Solution().twoSum(param_1, param_2)
Line 47 in _driver (Solution.py)
    _driver()
Line 62 in <module> (Solution.py)


```python
from collections import defaultdict


class Solution:
    def twoSum(self, nums: List[int], target: int) -> List[int]:
        num_to_indices = defaultdict(list)
        for i in range(nums):
            num_to_indices[nums[i]].append(i)
        
        for i in range(nums):
            if target - nums[i] in num_to_indices:
                indices = num_to_indices[target - nums[i]]
                for j in indices:
                    if i != j:
                        return [i, j]
        
        return []
```

`range(nums)` になっていたので修正した。
修正したら動いた。原因特定と修正は1分。

```python
from collections import defaultdict


class Solution:
    def twoSum(self, nums: List[int], target: int) -> List[int]:
        num_to_indices = defaultdict(list)
        for i in range(len(nums)):
            num_to_indices[nums[i]].append(i)
        
        for i in range(len(nums)):
            if target - nums[i] in num_to_indices:
                indices = num_to_indices[target - nums[i]]
                for j in indices:
                    if i != j:
                        return [i, j]
        
        return []
```

## （必要に応じて）最適化
特にない。

## テストケースを考える
問題の制約上ないけど、
- 入力が空な場合
- numsの要素数が1の場合
- 答えが一つに定まらない場合
- 答えがない場合
- numsの要素の値が大きすぎる場合
- numsの長さが大きすぎる場合
とかはあると思った。

特に、
- 入力が空または1つの場合
- 答えが一つに定まらない場合
は対応して上げるのが親切だと思った。
ただ、leetcodeの採点がList[int]の形式にしか対応してないので、「答えが一つに定まらない場合」は対応しない。

# step2: 30分
## 時間の関係でstep1で書けなかった迷いや判断を書く
大体は「step1 > テストケースを考える」に書いた。
あとは、
- 変数名。`target - nums[i]` が複数回登場しているけど、何か良い命名ないかな。本当は変数にしたかったけど、良い命名が思いつかず変数化せずそのままにしてしまった。

## 自分でコード読みやすく整えて実装
下記の対応をした。
- numsの要素が空または1つの場合に空Listを返す
  - エラーを投げる選択肢もあると思ったが、空listを返したほうが呼び出し元が使いやすいはず。この処理が業務ロジックにかかわる処理で必ず2つ以上入力がある想定なら不正状態と考えてエラーを投げてよいと思うが、今回はそうじゃない想定。
- 答えが0, または2つ以上になる場合に対応
  - 返した方が親切だと思った。平均的なパフォーマンスを向上させたいなら1つ見つけた時点で早期returnした方が良いが、今回は速度優先するより使い勝手を優先した。

```python
from collections import defaultdict


class Solution:
    def twoSum(self, nums: List[int], target: int) -> List[int]:
        """
        numsの要素の中で和がtargetとなるペアを返す

        - numsの要素数が1以下または和がtargetになるペアがない場合は、[]を返す
        """
        if len(nums) < 2:
            return []
        
        num_to_indices = defaultdict(list)
        for i, num in enumerate(nums):
            num_to_indices[num].append(i)
        
        for i, num in enumerate(nums):
            if target - num in num_to_indices:
                indices = num_to_indices[target - num]
                for j in indices:
                    if i != j:
                        return [i, j]

        return []
```

## 他の人のPRやコメントを踏まえて実装
https://github.com/tokuhirat/LeetCode/pull/11/files
`target - num`には`complement`という名前を使用している。

なるほど、下記のようにfor文をまとめることもできるか。
```python
class Solution:
    def twoSum(self, nums: List[int], target: int) -> List[int]:
        num_to_index = {}
        for i, num in enumerate(nums):
            complement = target - num
            if complement in num_to_index:
                return [i, num_to_index[complement]]
            num_to_index[num] = i
        raise ValueError(
            f"Cannot find two numbers such that they add up to target={target}"
        )
```

https://github.com/olsen-blue/Arai60/pull/11/files
見た

https://github.com/hayashi-ay/leetcode/pull/14/files#diff-92114846038d66d44249955e3fc1231e8f4ba88c06bbec7ab08a37eb61786f06
見た

他の人のレビューコメントを踏まえて修正
```python
from collections import defaultdict


class Solution:
    def twoSum(self, nums: List[int], target: int) -> List[int]:
        """
        numsの要素の中で和がtargetとなるペアを返す

        - numsの要素数が1以下または和がtargetになるペアがない場合は、[]を返す
        """
        
        if len(nums) < 2:
            return []
        
        num_to_index = defaultdict(int)
        for i, num in enumerate(nums):
            complement = target - num
            if complement in num_to_index:
                return [i, num_to_index[complement]]
            num_to_index[num] = i
        
        return []
```

# step3: 15分
※間違えがあればn回目を増やす

## 1回目
```python
from collections import defaultdict


class Solution:
    def twoSum(self, nums: List[int], target: int) -> List[int]:
        """
        numsの要素の中で和がtargetとなるペアを返す

        - numsの要素数が1以下または和がtargetになるペアがない場合は、[]を返す
        """

        if len(nums) < 2:
            return []

        num_to_index = {}
        for i, num in enumerate(nums):
            complement = target - num
            if complement in num_to_index:
                return [i, num_to_index[complement]]
            num_to_index[num] = i
        
        return []
```

## 2回目
```python
from collections import defaultdict


class Solution:
    def twoSum(self, nums: List[int], target: int) -> List[int]:
        """
        numsの中で和がtargetとなるペアを返す

        - numsの長さが2未満またはペアが見つからない場合は[]を返す
        """

        if len(nums) < 2:
            return []
        
        num_to_index = defaultdict(int)
        for i, num in enumerate(nums):
            complement = target - num
            if complement in num_to_index:
                return [i, num_to_index[complement]]
            num_to_index[num] = i
        
        return []
```

## 3回目
```python
from collections import defaultdict


class Solution:
    def twoSum(self, nums: List[int], target: int) -> List[int]:
        """
        numsのうち和がtargetとなるペアを返す

        numsの要素数が2未満またはtargetとなるペアが見つからない場合は[]を返す
        """

        if len(nums) < 2:
            return []

        num_to_index = defaultdict(int)
        for i, num in enumerate(nums):
            complement = target - num
            if complement in num_to_index:
                return [i, num_to_index[complement]]
            num_to_index[num] = i
        
        return []
```

## レビューコメントを踏まえて修正
https://github.com/garunitule/coding_practice/pull/11/files#r2085441677
step2, 3の実装の場合、defaultdictは不要。存在しないキーへのアクセスを想定しているように見えるのでむしろ混乱の元となる。

```python
class Solution:
    def twoSum(self, nums: List[int], target: int) -> List[int]:
        """
        numsの要素のうち、和がtargetとなるペアを一つ返す

        なお、numsの要素数が2未満または和がtargetとなるペアが存在しない場合は [] を返す
        """

        if len(nums) < 2:
            return []
        
        num_to_index = {}
        for i, num in enumerate(nums):
            complement = target - num
            if complement in num_to_index:
                return [i, num_to_index[complement]]
            num_to_index[num] = i
        
        return []
```