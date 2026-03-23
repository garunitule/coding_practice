# 進め方
- step1: 5分考えて分からなかったら答えを見る。答えを理解したら、答えを隠して書く。筆が進まず5分立ったら答えを見る。答えを送信して正解するまで。
- step2: コードを読みやすく整える。動くコードになったら終了。
- step3: 時間を計りながら書く。10分以内に3回連続でアクセプトされるまで。

# step1: 15分
図を書いてバックトラッキングで実装できると判断し実装した。
ただ時間計算量、実行時間を見積もれてなかった。
targetとcandidatesの要素の大小関係によると考えたが、それ以上具体的にできなかった。
そのため、毎回combinationの和を計算している部分を早くすべきかどうかの判断ができなかった。

```python
class Solution:
    def combinationSum(self, candidates: List[int], target: int) -> List[List[int]]:
        combinations = []
        def make_combinations(start: int, combination: List[int]):
            combination_sum = sum(combination)
            if combination_sum == target:
                combinations.append(combination[:])
                return
            if combination_sum > target:
                return
            for i in range(start, len(candidates)):
                combination.append(candidates[i])
                make_combinations(i, combination)
                combination.pop()
        
        make_combinations(0, [])
        return combinations
```

# step2: 30分
leetcodeの解説を見ると和も引数として渡すようにしている。
再帰の深さはざっくりtarget / min(candidates)になる。
各ノードからlen(candidates)回呼び出すので、時間計算量の上限を見積もると
O(1 + N + N**2 + ... + N ** (T / M)) = O(N ** (T / M + 1))
になるってことか。
葉の配列のコピーも考えるなら、O((T/M) * N ** (T/M))も加える必要がある。

スタックでも実装してみた。書けるには書けるが、空間計算量は木の深さを超えるので、メモリ使用量は増えているはず。スタック領域より通常のメモリの方が制限は広いと思うが。

```python
class Solution:
    def combinationSum(self, candidates: List[int], target: int) -> List[List[int]]:
        combinations = []
        combination_and_sum_and_start = [[[], 0, 0]]
        while combination_and_sum_and_start:
            combination, combination_sum, start = combination_and_sum_and_start.pop()
            if combination_sum == target:
                combinations.append(combination)
                continue
            if combination_sum > target:
                continue
            
            for i in range(start, len(candidates)):
                new_combination = combination[:]
                new_combination.append(candidates[i])
                combination_and_sum_and_start.append([new_combination, combination_sum + candidates[i], i])
        
        return combinations
```

https://github.com/h1rosaka/arai60/pull/53/changes#diff-f084bff8e4dbd771bf8a202d43b499bc30bffb7c10d4c5ccd2102f021910fd19R110-R118
combinationは各要素を0回以上使った組み合わせとみなせば、下記のようにも実装できる。
```python
class Solution:
    def combinationSum(self, candidates: List[int], target: int) -> List[List[int]]:
        combinations = []
        combination_and_index = [([], 0)]
        while combination_and_index:
            combination, index = combination_and_index.pop()
            combination_sum = sum(combination)
            if combination_sum == target:
                combinations.append(combination)
                continue
            if combination_sum > target:
                continue
            if index == len(candidates):
                continue
            
            combination_and_index.append((
                combination + [candidates[index]],
                index
            ))
            combination_and_index.append((
                combination[:],
                index + 1
            ))
        
        return combinations
```

# step3: 15分
※間違えがあればn回目を増やす

## 1回目
```python
class Solution:
    def combinationSum(self, candidates: List[int], target: int) -> List[List[int]]:
        combinations = []
        def make_combinations(combination: List[int], total: int, start: int):
            if total == target:
                combinations.append(combination[:])
                return
            if total > target:
                return
            for i in range(start, len(candidates)):
                combination.append(candidates[i])
                make_combinations(combination, total + candidates[i], i)
                combination.pop()
        
        make_combinations([], 0, 0)
        return combinations
```

## 2回目
```python
class Solution:
    def combinationSum(self, candidates: List[int], target: int) -> List[List[int]]:
        combinations = []
        def make_combinations(combination: List[int], total: int, start: int):
            if total == target:
                combinations.append(combination[:])
                return
            if total > target:
                return
            for i in range(start, len(candidates)):
                combination.append(candidates[i])
                make_combinations(combination, total + candidates[i], i)
                combination.pop()
        
        make_combinations([], 0, 0)
        return combinations
```

## 3回目
```python
class Solution:
    def combinationSum(self, candidates: List[int], target: int) -> List[List[int]]:
        combinations = []
        def make_combinations(combination: List[int], total: int, start: int):
            if total == target:
                combinations.append(combination[:])
                return
            if total > target:
                return
            for i in range(start, len(candidates)):
                combination.append(candidates[i])
                make_combinations(combination, total + candidates[i], i)
                combination.pop()

        make_combinations([], 0, 0)
        return combinations
```