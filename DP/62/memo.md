# 進め方
- step1: 5分考えて分からなかったら答えを見る。答えを理解したら、答えを隠して書く。筆が進まず5分立ったら答えを見る。答えを送信して正解するまで。
- step2: コードを読みやすく整える。動くコードになったら終了。
- step3: 時間を計りながら書く。10分以内に3回連続でアクセプトされるまで。

# step1: 15分
## 方針を考える
すごく動的計画法っぽい問題
dp[i][j]: i, jに行く経路数
とおけば、
- 初期値: dp[0][:] = 1, dp[:][0] = 1（[:]はすべてのインデックス）
- 遷移: dp[i][j] = dp[i][j - 1] + dp[i - 1][j]
- 返り値: dp[m - 1][n - 1]

こうすると時間計算量、空間計算量ともにO(m*n)で解ける

dpという変数名は分かりづらいので、unique_pathsが良さそう

他の方法は思いつかないので、このまま実装を進める

## 実装
```python
class Solution:
    def uniquePaths(self, m: int, n: int) -> int:
        unique_paths = [[0] * n for _ in range(m)]
        unique_paths[0] = [1] * n
        for i in range(m):
            unique_paths[i][0] = 1
        
        for i in range(1, m):
            for j in range(n):
                if j == 0:
                    continue
                unique_paths[i][j] = unique_paths[i][j - 1] + unique_paths[i - 1][j]

        return unique_paths[m - 1][n - 1] 
```

# step2: 30分
## 他の人のPRやコメントを踏まえて実装
leetcodeの回答を見た
たしかに、初期化は全部1でやっても良いな
再帰の解法もある
メモ化再帰の実装方法もあるな

```python
class Solution:
    def uniquePaths(self, m: int, n: int) -> int:
        unique_paths = [[None] * n for _ in range(m)]
        def get_unique_paths(m: int, n: int) -> int:
            if m == 0 or n == 0:
                return 1
            
            if unique_paths[m][n] is None:
                unique_paths[m][n] = get_unique_paths(m - 1, n) + get_unique_paths(m, n - 1)

            return unique_paths[m][n]

        return get_unique_paths(m - 1, n - 1)
```

https://github.com/h1rosaka/arai60/pull/35/files
row, colの方が分かりやすいか
メモ化はfunctoolsのcacheを使う方法もあるか

https://github.com/shintaro1993/arai60/pull/37/files#r2291377356
たしかに全部保持する必要はないな
直前だけ保持するようにすれば空間計算量はO(n)に落とせる

```python
class Solution:
    def uniquePaths(self, m: int, n: int) -> int:
        prev_num_paths = [1] * n
        for row in range(1, m):
            next_num_paths = [1] * n
            for column in range(1, n):
                next_num_paths[column] = next_num_paths[column - 1] + prev_num_paths[column]
            prev_num_paths = next_num_paths

        return prev_num_paths[-1]
```

# step3: 15分
※間違えがあればn回目を増やす

## 1回目
```python
class Solution:
    def uniquePaths(self, m: int, n: int) -> int:
        prev_num_paths = [1] * n
        for _ in range(1, m):
            next_num_paths = [1] * n
            for column in range(1, n):
                next_num_paths[column] = next_num_paths[column - 1] + prev_num_paths[column]
            prev_num_paths = next_num_paths 

        return prev_num_paths[-1]
```

## 2回目
```python
class Solution:
    def uniquePaths(self, m: int, n: int) -> int:
        prev_num_paths = [1] * n
        for _ in range(1, m):
            next_num_paths = [1] * n
            for column in range(1, n):
                next_num_paths[column] = next_num_paths[column - 1] + prev_num_paths[column]
            prev_num_paths = next_num_paths

        return prev_num_paths[-1]
```

## 3回目
```python
class Solution:
    def uniquePaths(self, m: int, n: int) -> int:
        prev_num_paths = [1] * n
        for _ in range(1, m):
            next_num_paths = [1] * n
            for column in range(1, n):
                next_num_paths[column] = next_num_paths[column - 1] + prev_num_paths[column]
            prev_num_paths = next_num_paths

        return prev_num_paths[-1]
```