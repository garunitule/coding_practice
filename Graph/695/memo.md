# 進め方
- step1: 5分考えて分からなかったら答えを見る。答えを理解したら、答えを隠して書く。筆が進まず5分立ったら答えを見る。答えを送信して正解するまで。
- step2: コードを読みやすく整える。動くコードになったら終了。
- step3: 時間を計りながら書く。10分以内に3回連続でアクセプトされるまで。

# step1: 15分
## 方針を考える
まずunion findであればunion by sizeを利用すれば最適化しつつサイズを保持できそう
一通りunionした後にsizeの最大値を返せばよい

他の方法があるか考える
DFSやBFSで探索しつつ、最大値を保持する方針でもできそう

今回だとDFSとBFSの違いはなさそう。DFSで実装してみる


## 実装

最初、`WATER = '0'`と置いてしまっていた
修正したら通った

```python
class Solution:
    def maxAreaOfIsland(self, grid: List[List[int]]) -> int:
        num_rows = len(grid)
        num_cols = len(grid[0])
        visited = [[False] * num_cols for _ in range(num_rows)]
        WATER = 0

        def traverse_island_and_calc_area(row: int, col: int, area: int) -> int:
            if not (0 <= row < num_rows and 0 <= col < num_cols):
                return area
            if grid[row][col] == WATER:
                return area
            if visited[row][col]:
                return area
            
            visited[row][col] = True
            area += 1
            area = traverse_island_and_calc_area(row - 1, col, area)
            area = traverse_island_and_calc_area(row, col - 1, area)
            area = traverse_island_and_calc_area(row + 1, col, area)
            area = traverse_island_and_calc_area(row, col + 1, area)
            return area
            
        max_area = 0
        for row in range(num_rows):
            for col in range(num_cols):
                area = traverse_island_and_calc_area(row, col, 0)
                max_area = max(max_area, area)
        return max_area
```

# step2: 30分
## 他の人のPRやコメントを踏まえて実装
leetcodeの解法を見て実装
再帰を活用した解法
自分が実装したstep1の解法とアイデアは近い
違いはareaを引数に含めていない点で、含めない方が自然だと思った

ある地点(row, col)のislandのareaは、(row,col)の島と隣接する4つの島のareaの和だと考えられる
ただし、重複して数えるのを避けるためにvisitedで訪問したかどうか管理している、と考えられる
少し日本語として不自然か？
他の人がどのような理解をしているか聞いてみたい

```python
class Solution:
    def maxAreaOfIsland(self, grid: List[List[int]]) -> int:
        num_rows = len(grid)
        num_cols = len(grid[0])
        visited = set()

        def calc_island_area(row: int, col: int) -> int:
            if not (0 <= row < num_rows and 0 <= col < num_cols):
                return 0
            if grid[row][col] == 0:
                return 0
            if (row, col) in visited:
                return 0
            
            visited.add((row, col))
            return 1 + calc_island_area(row + 1, col) + calc_island_area(row - 1, col) + calc_island_area(row, col + 1) + calc_island_area(row, col - 1)
        
        max_area = 0
        for row in range(num_rows):
            for col in range(num_cols):
                max_area = max(max_area, calc_island_area(row, col))
        return max_area
```


https://github.com/ryosuketc/leetcode_arai60/pull/18/files
スタックを利用した実装
- 調査候補をスタックに追加する
- スタックが空になる（=調査対象がなくなる）まで下記を実行する
    - スタックから取り出して未訪問の島だったらareaをインクリメントして隣接する場所を調査候補に追加する
のように考えられそう

実装してみた。
```python
class Solution:
    def maxAreaOfIsland(self, grid: List[List[int]]) -> int:
        num_rows = len(grid)
        num_cols = len(grid[0])
        visited = [[False] * num_cols for _ in range(num_rows)]
        LAND = 1

        def is_unvisited_land_in_grid(row: int, col: int) -> bool:
            if not (0 <= row < num_rows and 0 <= col < num_cols):
                return False
            if grid[row][col] != LAND:
                return False
            if visited[row][col]:
                return False
            return True
        
        def calc_island_area(row: int, col: int) -> int:
            pos_to_visit = [(row, col)]
            area = 0

            while pos_to_visit:
                row, col = pos_to_visit.pop()
                if not is_unvisited_land_in_grid(row, col):
                    continue
                
                visited[row][col] = True
                area += 1
                pos_to_visit.append((row - 1, col))
                pos_to_visit.append((row, col - 1))
                pos_to_visit.append((row + 1, col))
                pos_to_visit.append((row, col + 1))
            return area

        max_area = 0
        for row in range(num_rows):
            for col in range(num_cols):
                max_area = max(max_area, calc_island_area(row, col))
        return max_area
```

なお、スタックに追加する前に未訪問の島かチェックする実装もある
こちらの方が下記をスタックに追加しないため処理速度の点では有利。
- すでに訪問済みのLAND
- islandに隣接するWATER
早期returnするだけの処理とはいえ上記の分だけ省略できる

一方、どうしても判定処理が複数回登場するので可読性は落ちる

land_to_traverseに追加する前にvisitedに追加している
「なぜland_to_traverseに重複して追加される可能性がある」がうまく説明できない
上下左右に走査するので戻りうることはあり得るのは分かる
「land_to_traverseに一度だけ追加されればよいので、land_to_travserseに追加する前にvisitedに追加すればよい」という理屈は分かる
他の人がどのような理解をしているか聞いてみたい

以下、実装

```python
class Solution:
    def maxAreaOfIsland(self, grid: List[List[int]]) -> int:
        num_rows = len(grid)
        num_cols = len(grid[0])
        WATER = 0
        LAND = 1
        visited = set()

        def is_unvisited_land(row: int, col: int) -> bool:
            if not (0 <= row < num_rows and 0 <= col < num_cols):
                return False
            if grid[row][col] != LAND:
                return False
            if (row, col) in visited:
                return False
            return True

        def calc_island_area(row: int, col: int) -> int:
            if not is_unvisited_land(row, col):
                return 0
            
            land_to_traverse = [(row, col)]
            visited.add((row, col))
            area = 0
            while land_to_traverse:
                row, col = land_to_traverse.pop()
                area += 1

                if is_unvisited_land(row - 1, col):
                    visited.add((row - 1, col))
                    land_to_traverse.append((row - 1, col))
                if is_unvisited_land(row + 1, col):
                    visited.add((row + 1, col))
                    land_to_traverse.append((row + 1, col))
                if is_unvisited_land(row, col - 1):
                    visited.add((row, col - 1))
                    land_to_traverse.append((row, col - 1))
                if is_unvisited_land(row, col + 1):
                    visited.add((row, col + 1))
                    land_to_traverse.append((row, col + 1))
            return area

        max_area = 0
        for row in range(num_rows):
            for col in range(num_cols):
                if grid[row][col] != LAND or (row, col)in visited:
                    continue
                max_area = max(max_area, calc_island_area(row, col))
        return max_area                
```

# step3: 15分
※間違えがあればn回目を増やす

スタックを利用し、スタック追加後に未訪問の島か判定する方式が一番しっくり来ているので、この方法で実装する
(今回はパフォーマンスより可読性を優先した)

## 1回目
```python
class Solution:
    def maxAreaOfIsland(self, grid: List[List[int]]) -> int:
        num_rows = len(grid)
        num_cols = len(grid[0])
        LAND = 1
        visited = set()

        def is_unvisited_land(row: int, col: int) -> bool:
            if not (0 <= row < num_rows and 0 <= col < num_cols):
                return False
            if grid[row][col] != LAND:
                return False
            if (row, col) in visited:
                return False
            return True
        
        def calc_island_area(row: int, col: int) -> int:
            pos_to_visit = [(row, col)]
            area = 0

            while pos_to_visit:
                row, col = pos_to_visit.pop()
                if not is_unvisited_land(row, col):
                    continue

                area += 1
                visited.add((row, col))

                pos_to_visit.append((row - 1, col))
                pos_to_visit.append((row + 1, col))
                pos_to_visit.append((row, col - 1))
                pos_to_visit.append((row, col + 1))
            return area
        
        max_area = 0
        for row in range(num_rows):
            for col in range(num_cols):
                max_area = max(max_area, calc_island_area(row, col))
        return max_area
```

## 2回目
```python
class Solution:
    def maxAreaOfIsland(self, grid: List[List[int]]) -> int:
        num_rows = len(grid)
        num_cols = len(grid[0])
        visited = set()
        LAND = 1

        def is_unvisited_land(row: int, col: int) -> bool:
            if not (0 <= row < num_rows and 0 <= col < num_cols):
                return False
            if grid[row][col] != LAND:
                return False
            if (row, col) in visited:
                return False
            return True 

        def calc_island_area(row: int, col: int) -> int:
            pos_to_visit = [(row, col)]
            area = 0

            while pos_to_visit:
                row, col = pos_to_visit.pop()
                if not is_unvisited_land(row, col):
                    continue

                area += 1
                visited.add((row, col))
                pos_to_visit.append((row - 1, col))
                pos_to_visit.append((row + 1, col))
                pos_to_visit.append((row, col - 1))
                pos_to_visit.append((row, col + 1))
            return area

        max_area = 0
        for row in range(num_rows):
            for col in range(num_cols):
                max_area = max(max_area, calc_island_area(row, col))
        return max_area
```

## 3回目
```python
class Solution:
    def maxAreaOfIsland(self, grid: List[List[int]]) -> int:
        num_rows = len(grid)
        num_cols = len(grid[0])
        LAND = 1
        visited = set()

        def is_unvisited_land(row: int, col: int) -> bool:
            if not (0 <= row < num_rows and 0 <= col < num_cols):
                return False
            if grid[row][col] != LAND:
                return False
            if (row, col) in visited:
                return False
            return True
        
        def calc_island_area(row: int, col: int) -> int:
            pos_to_visit = [(row, col)]
            area = 0

            while pos_to_visit:
                row, col = pos_to_visit.pop()
                if not is_unvisited_land(row, col):
                    continue
                
                area += 1
                visited.add((row, col))
                pos_to_visit.append((row - 1, col))
                pos_to_visit.append((row + 1, col))
                pos_to_visit.append((row, col - 1))
                pos_to_visit.append((row, col + 1))
            return area
        
        max_area = 0
        for row in range(num_rows):
            for col in range(num_cols):
                max_area = max(max_area, calc_island_area(row, col))
        return max_area
```