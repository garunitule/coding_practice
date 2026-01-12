# 進め方
- step1: 5分考えて分からなかったら答えを見る。答えを理解したら、答えを隠して書く。筆が進まず5分立ったら答えを見る。答えを送信して正解するまで。
- step2: コードを読みやすく整える。動くコードになったら終了。
- step3: 時間を計りながら書く。10分以内に3回連続でアクセプトされるまで。

# step1: 15分
手でやるなら、
nums[0], nums[1], nums[2]
nums[0], nums[2], nums[1]
nums[1], nums[0], nums[2]
nums[1], nums[2], nums[0]
nums[2], nums[0], nums[1]
nums[2], nums[1], nums[0]
のように作る。
先頭をnums[0]にしたら、候補はnums[1], nums[2]なので2つ目はどちらかを選んで、3つ目は残りを選ぶ。
先頭をnums[1]にしたら、候補はnums[0], nums[2]なので2つ目はどちらかを選んで、3つ目は残りを選ぶ。
先頭に関してもnums[0], nums[1], nums[2]から順番に選ぶ。


雑だけど、このような処理を依頼したい。
```python
result = []
for num1 in nums:
    for num2 in num1以外のnums:
        for num3 in num1,num2以外のnums:
            result.append([num1, num2, num3])
return result
```

コードで表現するうえで問題なのは下記。
- nums1以外のnumsをどうコードで表現するか
- for文の数はlen(nums)分だけ実行する必要があるが、それをどうコードで表現するか（実行時じゃないとfor文の繰り返し回数が決まらない）

分からないので解答を見る。

# step2: 30分
step1で感じた疑問や問題が解決された。
leetcodeの解説通り、たしかにグラフとして捉えると見通しが良いと思った。
```python
class Solution:
    def permute(self, nums: List[int]) -> List[List[int]]:
        permutations = []
        def build_permutations(permutation: List[int]):
            if len(permutation) == len(nums):
                permutations.append(permutation[:])
                return
            
            for num in nums:
                if num not in permutation:
                    permutation.append(num)
                    build_permutations(permutation)
                    permutation.pop()
        
        build_permutations([])
        return permutations
```

https://github.com/h1rosaka/arai60/pull/51/files
構築途中の順列だけじゃなく使ってない要素も渡す実装。
やりたいことは理解できるが、作成途中の順列から判断できるので渡さなくても良いとは思った。

https://github.com/naoto-iwase/leetcode/pull/51/files
permutationsはitertools.permutationsがあるので避けた方が良さそう。
permute自身を再帰的に呼び出す実装をしている。

再帰じゃなくてループでも実装してみる。

```python
class Solution:
    def permute(self, nums: List[int]) -> List[List[int]]:
        result = []
        stack = [[]]
        while stack:
            permutation = stack.pop()
            if len(permutation) == len(nums):
                result.append(permutation)
                continue
            for num in nums:
                if num not in permutation:
                    stack.append(permutation + [num])

        return result
```


# step3: 15分
※間違えがあればn回目を増やす

## 1回目
```python
class Solution:
    def permute(self, nums: List[int]) -> List[List[int]]:
        result = []
        def build_permutations(permutation: List[int]):
            if len(permutation) == len(nums):
                result.append(permutation[:])
                return

            for num in nums:
                if num not in permutation:
                    permutation.append(num)
                    build_permutations(permutation)
                    permutation.pop()
        
        build_permutations([])
        return result
```

## 2回目
```python
class Solution:
    def permute(self, nums: List[int]) -> List[List[int]]:
        result = []
        def build_permutations(permutation: List[int]):
            if len(permutation) == len(nums):
                result.append(permutation[:])
                return
            
            for num in nums:
                if num not in permutation:
                    permutation.append(num)
                    build_permutations(permutation)
                    permutation.pop()
        
        build_permutations([])
        return result
```

## 3回目
```python
class Solution:
    def permute(self, nums: List[int]) -> List[List[int]]:
        result = []
        def build_permutations(permutation: List[int]):
            if len(permutation) == len(nums):
                result.append(permutation[:])
                return
            
            for num in nums:
                if num not in permutation:
                    permutation.append(num)
                    build_permutations(permutation)
                    permutation.pop()
        
        build_permutations([])
        return result
```