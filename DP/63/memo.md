# 進め方
- step1: 5分考えて分からなかったら答えを見る。答えを理解したら、答えを隠して書く。筆が進まず5分立ったら答えを見る。答えを送信して正解するまで。
- step2: コードを読みやすく整える。動くコードになったら終了。
- step3: 時間を計りながら書く。10分以内に3回連続でアクセプトされるまで。

# step1: 15分
## 方針を考える
Unique Pathと一緒で動的計画法で解けそう
一番愚直にやるなら
path_nums[i][j]: i, jへのパス。ただし、grid[i][j] == 1なら0を格納する。
そうすれば遷移は障害物の有無に限らず、path_nums[i][j] = path_nums[i - 1][j] + path_nums[i][j - 1]にできる

Unique pathと同じように直前の行と左隣だけ記録しておけば、時間計算量はO(n)に削減できる

後者で実装する。

## 実装
改善の余地はありそうだが、動くコードはできた
```python
class Solution:
    def uniquePathsWithObstacles(self, obstacleGrid: List[List[int]]) -> int:
        m = len(obstacleGrid)
        n = len(obstacleGrid[0])
        prev_num_paths = []
        for column in range(n):
            if obstacleGrid[0][column] == 1:
                prev_num_paths.extend([0] * (n - column))
                break
            prev_num_paths.append(1)
        
        for row in range(1, m):
            next_num_paths = [1] * n
            for column in range(n):
                if obstacleGrid[row][column] == 1:
                    next_num_paths[column] = 0
                    continue
                next_num_paths[column] = prev_num_paths[column]
                if column > 0:
                    next_num_paths[column] += next_num_paths[column - 1] 
            prev_num_paths = next_num_paths 

        return prev_num_paths[-1]

```

# step2: 30分
## 他の人のPRやコメントを踏まえて実装
https://github.com/Kaichi-Irie/leetcode-python/pull/25
row = 0, col = 0もfor文の中で処理するコード
初期化を外に出すよりもこの方が分かりやすい

https://github.com/shintaro1993/arai60/pull/38/files
ここもrow = 0, col = 0をfor文の中で処理するコード

両方読んで思ったのは、num_paths[0][0] = 1にしてそれ以外はfor文で処理するのが分かりやすい
そうすれば下記のように遷移をかけてすごくシンプルになる
```python
next_num_paths[column] = 0
if obstacleGrid[row][column] == 1:
    continue
if row > 0:
    next_num_paths[column] += prev_num_paths[column]
if column > 0:
    next_num_paths[column] += next_num_paths[column - 1]
```

書いた
```python
class Solution:
    def uniquePathsWithObstacles(self, obstacleGrid: List[List[int]]) -> int:
        if not obstacleGrid:
            return 0
        
        OBSTACLE = 1
        if obstacleGrid[0][0] == OBSTACLE:
            return 0 

        num_rows = len(obstacleGrid)
        num_columns = len(obstacleGrid[0])
        prev_num_paths = [0] * num_columns
        for row in range(num_rows):
            next_num_paths = [0] * num_columns
            for column in range(num_columns):
                if obstacleGrid[row][column] == OBSTACLE:
                    continue
                if row == 0 and column == 0:
                    next_num_paths[column] = 1
                    continue
                if 0 < row:
                    next_num_paths[column] += prev_num_paths[column]
                if 0 < column:
                    next_num_paths[column] += next_num_paths[column - 1]
            prev_num_paths = next_num_paths

        return prev_num_paths[num_columns - 1]

```

よく考えたら保持する配列は一つで良いことが分かった
スタート地点がOBSTACLEだったら早期returnしてるけど、これは好み
後続のfor文で対応できるけど、早期returnした方が親切だと思う
```python
class Solution:
    def uniquePathsWithObstacles(self, obstacleGrid: List[List[int]]) -> int:
        if not obstacleGrid:
            return 0
        
        OBSTACLE = 1
        if obstacleGrid[0][0] == OBSTACLE:
            return 0 

        num_rows = len(obstacleGrid)
        num_columns = len(obstacleGrid[0])
        num_paths_in_row = [0] * num_columns
        num_paths_in_row[0] = 1
        for row in range(num_rows):
            for column in range(num_columns):
                if obstacleGrid[row][column] == OBSTACLE:
                    num_paths_in_row[column] = 0
                    continue
                if 0 < column:
                    num_paths_in_row[column] += num_paths_in_row[column - 1]
        
        return num_paths_in_row[-1]
                
```

# step3: 15分
※間違えがあればn回目を増やす

## 1回目
```python
class Solution:
    def uniquePathsWithObstacles(self, obstacleGrid: List[List[int]]) -> int:
        OBSTACLE = 1
        if not obstacleGrid or obstacleGrid[0][0] == OBSTACLE:
            return 0
        
        num_rows = len(obstacleGrid)
        num_columns = len(obstacleGrid[0])
        num_paths_in_row = [0] * num_columns
        num_paths_in_row[0] = 1
        for row in range(num_rows):
            for column in range(num_columns):
                if obstacleGrid[row][column] == OBSTACLE:
                    num_paths_in_row[column] = 0
                    continue
                if 0 < column:
                    num_paths_in_row[column] += num_paths_in_row[column - 1]

        return num_paths_in_row[-1]
```

## 2回目
```python
class Solution:
    def uniquePathsWithObstacles(self, obstacleGrid: List[List[int]]) -> int:
        OBSTACLE = 1
        if not obstacleGrid or obstacleGrid[0][0] == OBSTACLE:
            return 0
        
        num_rows = len(obstacleGrid)
        num_columns = len(obstacleGrid[0])
        num_paths_in_row = [0] * num_columns
        num_paths_in_row[0] = 1
        for row in range(num_rows):
            for column in range(num_columns):
                if obstacleGrid[row][column] == OBSTACLE:
                    num_paths_in_row[column] = 0
                    continue
                if 0 < column:
                    num_paths_in_row[column] += num_paths_in_row[column - 1]

        return num_paths_in_row[-1]
        
```

## 3回目
```python
class Solution:
    def uniquePathsWithObstacles(self, obstacleGrid: List[List[int]]) -> int:
        OBSTACLE = 1
        if not obstacleGrid or obstacleGrid[0][0] == OBSTACLE:
            return 0
        
        num_rows = len(obstacleGrid)
        num_columns = len(obstacleGrid[0])
        num_paths_in_row = [0] * num_columns
        num_paths_in_row[0] = 1
        for row in range(num_rows):
            for column in range(num_columns):
                if obstacleGrid[row][column] == OBSTACLE:
                    num_paths_in_row[column] = 0
                    continue
                if 0 < column:
                    num_paths_in_row[column] += num_paths_in_row[column - 1]

        return num_paths_in_row[-1]
```