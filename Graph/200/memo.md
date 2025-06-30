# 進め方
- step1: 5分考えて分からなかったら答えを見る。答えを理解したら、答えを隠して書く。筆が進まず5分立ったら答えを見る。答えを送信して正解するまで。
- step2: コードを読みやすく整える。動くコードになったら終了。
- step3: 時間を計りながら書く。10分以内に3回連続でアクセプトされるまで。

# step1: 15分
## 方針を考える
m * nのvisitedを用意する
for文でgridの要素へ順番にアクセスしvisitedを更新する
現在のgridの要素からアクセスできる島に一通りアクセスしてvisitedを更新する
終わったら、for文で次のgridの要素に進む
のようなイメージ

時間計算量：O(m * n)
空間計算量：O(m * n)

## 実装
最初 `dfs`の中で島かどうか判定する処理を抜かしてしまっていた
判定処理を追加したら通った

```python
class Solution:
    def numIslands(self, grid: List[List[str]]) -> int:
        m = len(grid)
        n = len(grid[0])
        visited = [[False for _ in range(n)] for _ in range(m)]

        def dfs(grid: List[List[str]], i: int, j: int) -> List[List[str]]:
            visited[i][j] = True

            if 0 <= i - 1 and grid[i - 1][j] == '1' and not visited[i - 1][j]:
                grid = dfs(grid, i - 1, j)
            if 0 <= j - 1 and grid[i][j - 1] == '1' and not visited[i][j - 1]:
                grid = dfs(grid, i, j - 1)
            if i + 1 < m and grid[i + 1][j] == '1' and not visited[i + 1][j]:
                grid = dfs(grid, i + 1, j)
            if j + 1 < n and grid[i][j + 1] == '1' and not visited[i][j + 1]:
                grid = dfs(grid, i, j + 1)
            return grid

        numberOfislands = 0
        for i in range(m):
            for j in range(n):
                if grid[i][j] == '1' and not visited[i][j]:
                    grid = dfs(grid, i, j)
                    numberOfislands += 1
        return numberOfislands
```

## （必要に応じて）最適化
visitedを用意せずにgridを更新するようにすれば空間計算量は減らせる。ただ、実際には元のgridを変えないほうが良いケースが多い気がする。変えるにしてもコメントはあった方が良いと思う。

それ以外は特に思いつかない。

## テストケースを考える

# step2: 30分
## 自分でコード読みやすく整えて実装
メソッド名をdfsからvisit_adjacent_landsに修正した

```python
class Solution:
    def numIslands(self, grid: List[List[str]]) -> int:
        m = len(grid)
        n = len(grid[0])
        visited = [[False for _ in range(n)] for _ in range(m)]

        def visit_adjacent_lands(grid: List[List[str]], i: int, j: int) -> List[List[str]]:
            visited[i][j] = True

            if 0 <= i - 1 and grid[i - 1][j] == '1' and not visited[i - 1][j]:
                grid = visit_adjacent_lands(grid, i - 1, j)
            if 0 <= j - 1 and grid[i][j - 1] == '1' and not visited[i][j - 1]:
                grid = visit_adjacent_lands(grid, i, j - 1)
            if i + 1 < m and grid[i + 1][j] == '1' and not visited[i + 1][j]:
                grid = visit_adjacent_lands(grid, i + 1, j)
            if j + 1 < n and grid[i][j + 1] == '1' and not visited[i][j + 1]:
                grid = visit_adjacent_lands(grid, i, j + 1)
            return grid

        numberOfislands = 0
        for i in range(m):
            for j in range(n):
                if grid[i][j] == '1' and not visited[i][j]:
                    grid = visit_adjacent_lands(grid, i, j)
                    numberOfislands += 1
        return numberOfislands
```

## 他の人のPRやコメントを踏まえて実装
leetcodeの解法を見ると、dfs, bfs, union findの3つあった。union findは名前しか知らなかったので調査する。
leetcodeのunion findの実装では、`grid[i][j] == "0"`だった場合に`self.parent`に`-1`を入れている。間違えてfindを呼び出した場合におかしな挙動になるので、変えたほうが良い気がする。
parent, rankを辞書にすればそこで処理は落ちる（これがベストかはわからないが）
この方法で一度実装してみる

自分はrankで効率よく処理できる理由の理解に時間かかりそうだったので、union by sizeで最適化した

最初の実装（動かないです）
```python
class UnionFind:
    def __init__(self, grid: List[List[str]]):
        m = len(grid)
        n = len(grid[0])
        self.parent = {}
        self.size = {}
        self.count = 0
        for i in range(m):
            for j in range(n):
                if grid[i][j] == '0':
                    continue
                self.parent[i * n + j] = i * n + j
                self.size[i * n + j] = 1
                self.count += 1

    def find(self, x):
        if self.parent[x] != x:
            self.parent[x] = self.find(self.parent[x])
        return self.parent[x]

    def union(self, x, y):
        root_x = self.find(x)
        root_y = self.find(y)
        if root_x == root_y:
            return
        
        if self.size[x] < self.size[y]:
            x, y = y, x
        self.parent[y] = x
        self.size[x] += self.size[y]
        self.count -= 1
    
    def get_count(self):
        return self.count

class Solution:
    def numIslands(self, grid: List[List[str]]) -> int:
        union_find = UnionFind(grid)
        m = len(grid)
        n = len(grid[0])

        for i in range(m):
            for j in range(n):
                if grid[i][j] == '1':
                    current_land = i * n + j
                    if 0 <= i - 1 and grid[i - 1][j] == '1':
                        adjacent_land = (i - 1) * n + j
                        union_find.union(current_land, adjacent_land)
                    if 0 <= j - 1 and grid[i][j - 1] == '1':
                        adjacent_land = i * n + j - 1
                        union_find.union(current_land, adjacent_land)
                    if i + 1 < m and grid[i + 1][j] == '1':
                        adjacent_land = (i + 1) * n + j
                        union_find.union(current_land, adjacent_land)
                    if j + 1 < n and grid[i][j + 1] == '1':
                        adjacent_land = i * n + j + 1
                        union_find.union(current_land, adjacent_land)
        return union_find.get_count()
```

unionの処理が間違っていた。
```python
class UnionFind:
    def __init__(self, grid: List[List[str]]):
        m = len(grid)
        n = len(grid[0])
        self.parent = {}
        self.size = {}
        self.count = 0
        for i in range(m):
            for j in range(n):
                if grid[i][j] == '0':
                    continue
                self.parent[i * n + j] = i * n + j
                self.size[i * n + j] = 1
                self.count += 1

    def find(self, x):
        if self.parent[x] != x:
            self.parent[x] = self.find(self.parent[x])
        return self.parent[x]

    def union(self, x, y):
        root_x = self.find(x)
        root_y = self.find(y)
        if root_x == root_y:
            return
        
        if self.size[root_x] < self.size[root_y]:
            root_x, root_y = root_y, root_x
        self.parent[root_y] = root_x
        self.size[root_x] += self.size[root_y]
        self.count -= 1
    
    def get_count(self):
        return self.count

class Solution:
    def numIslands(self, grid: List[List[str]]) -> int:
        union_find = UnionFind(grid)
        m = len(grid)
        n = len(grid[0])

        for i in range(m):
            for j in range(n):
                if grid[i][j] == '1':
                    current_land = i * n + j
                    if 0 <= i - 1 and grid[i - 1][j] == '1':
                        adjacent_land = (i - 1) * n + j
                        union_find.union(current_land, adjacent_land)
                    if 0 <= j - 1 and grid[i][j - 1] == '1':
                        adjacent_land = i * n + j - 1
                        union_find.union(current_land, adjacent_land)
                    if i + 1 < m and grid[i + 1][j] == '1':
                        adjacent_land = (i + 1) * n + j
                        union_find.union(current_land, adjacent_land)
                    if j + 1 < n and grid[i][j + 1] == '1':
                        adjacent_land = i * n + j + 1
                        union_find.union(current_land, adjacent_land)
        return union_find.get_count()
```


https://discord.com/channels/1084280443945353267/1336510702742929499/1350740661791625327
BFSでの実装時、キューに突っ込むタイミングでvisited=Trueにしないと複数回キューに突っ込まれることがあるという話

# step3: 15分
※間違えがあればn回目を増やす
DFSは慣れているのでUnionFindで実装する

## 1回目
マジックナンバーをなくした。
あとよく考えたらDFSと違って下と右だけでいい

```python
WATER = '0'
LAND = '1'

class UnionFind:
    def __init__(self, grid: List[List[str]]):
        m = len(grid)
        n = len(grid[0])
        self.parent = {}
        self.size = {}
        self.count = 0
        for i in range(m):
            for j in range(n):
                if grid[i][j] == WATER:
                    continue
                self.parent[i * n + j] = i * n + j
                self.size[i * n + j] = 1
                self.count += 1
    
    def find(self, x):
        if self.parent[x] != x:
            self.parent[x] = self.find(self.parent[x])
        return self.parent[x]
    
    def union(self, x, y):
        root_x = self.find(x)
        root_y = self.find(y)
        if root_x == root_y:
            return
        
        if self.size[root_x] < self.size[root_y]:
            root_x, root_y = root_y, root_x
        self.parent[root_y] = root_x
        self.size[root_x] += self.size[root_y]
        self.count -= 1
    
    def get_count(self):
        return self.count
                

class Solution:
    def numIslands(self, grid: List[List[str]]) -> int:
        m = len(grid)
        n = len(grid[0])
        union_find = UnionFind(grid)
        for i in range(m):
            for j in range(n):
                current_land = i * n + j
                if grid[i][j] == WATER:
                    continue
                if i + 1 < m and grid[i + 1][j] == LAND:
                    adjacent_land = (i + 1) * n + j
                    union_find.union(current_land, adjacent_land)
                if j + 1 < n and grid[i][j + 1] == LAND:
                    adjacent_land = i * n + j + 1
                    union_find.union(current_land, adjacent_land)
        return union_find.get_count()
```

## 2回目
```python
LAND = '1'

class UnionFind:
    def __init__(self, grid: List[List[str]]):
        m = len(grid)
        n = len(grid[0])
        self.parent = {}
        self.size = {}
        self.count = 0

        for i in range(m):
            for j in range(n):
                if grid[i][j] != LAND:
                    continue
                self.parent[i * n + j] = i * n + j
                self.size[i * n + j] = 1
                self.count += 1

    def find(self, x):
        if self.parent[x] != x:
            self.parent[x] = self.find(self.parent[x])
        return self.parent[x]
    
    def union(self, x, y):
        root_x = self.find(x)
        root_y = self.find(y)
        if root_x == root_y:
            return
        
        if self.size[root_x] < self.size[root_y]:
            root_x, root_y = root_y, root_x
        self.parent[root_y] = root_x
        self.size[root_x] += self.size[root_y]
        self.count -= 1
    
    def get_count(self):
        return self.count

class Solution:
    def numIslands(self, grid: List[List[str]]) -> int:
        union_find = UnionFind(grid)
        m = len(grid)
        n = len(grid[0])
        for i in range(m):
            for j in range(n):
                land = i * n + j
                if grid[i][j] != LAND:
                    continue
                if i + 1 < m and grid[i + 1][j] == LAND:
                    adjacent_land = (i + 1) * n + j
                    union_find.union(land, adjacent_land)
                if j + 1 < n and grid[i][j + 1] == LAND:
                    adjacent_land = i * n + j + 1
                    union_find.union(land, adjacent_land)
        return union_find.get_count()
```

## 3回目
行数が長いので6分くらいかかった

```python
LAND = '1'

class UnionFind:
    def __init__(self, grid: List[List[str]]):
        m = len(grid)
        n = len(grid[0])
        self.parent = {}
        self.size = {}
        self.count = 0
        
        for i in range(m):
            for j in range(n):
                if grid[i][j] != LAND:
                    continue
                self.parent[i * n + j] = i * n + j
                self.size[i * n + j] = 1
                self.count += 1

    def find(self, x):
        if self.parent[x] != x:
            self.parent[x] = self.find(self.parent[x])
        return self.parent[x]

    def union(self, x, y):
        root_x = self.find(x)
        root_y = self.find(y)
        if root_x == root_y:
            return

        if self.size[root_x] < self.size[root_y]:
            root_x, root_y = root_y, root_x
        self.parent[root_y] = root_x
        self.size[root_x] += self.size[root_y]
        self.count -= 1

    def get_count(self):
        return self.count 

class Solution:
    def numIslands(self, grid: List[List[str]]) -> int:
        m = len(grid)
        n = len(grid[0])
        union_find = UnionFind(grid)

        def is_land_in_grid(i, j):
            return 0 <= i < m and 0 <= j < n and grid[i][j] == LAND 

        for i in range(m):
            for j in range(n):
                if grid[i][j] != LAND:
                    continue
                land = i * n + j
                if is_land_in_grid(i + 1, j):
                    adjacent_land = (i + 1) * n + j
                    union_find.union(land, adjacent_land)
                if is_land_in_grid(i, j + 1):
                    adjacent_land = i * n + j + 1
                    union_find.union(land, adjacent_land)
        return union_find.get_count()
                
```