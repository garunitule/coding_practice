# 進め方
- step1: 5分考えて分からなかったら答えを見る。答えを理解したら、答えを隠して書く。筆が進まず5分立ったら答えを見る。答えを送信して正解するまで。
- step2: コードを読みやすく整える。動くコードになったら終了。
- step3: 時間を計りながら書く。10分以内に3回連続でアクセプトされるまで。

# step1: 15分
## 方針を考える
union find, DFS, BFSどれでもできそう

union findだとedgesを繰り返し処理すればよいので自然に実装できる気がするが、必須レベルの一般常識ではないとことなのでDFSで実装する

DFSの場合はedgesを各aiの位置にaiから遷移できるノードを格納したlistに変換する必要がある
edgesのままだとaiに対応するedgeを探す必要があるため

## 実装
DFSで実装した
20分かかった。グラフへの慣れ不足を感じる

```python
from collections import defaultdict


class Solution:
    def countComponents(self, n: int, edges: List[List[int]]) -> int:
        node_to_connected_nodes = defaultdict(list)
        for node1, node2 in edges:
            node_to_connected_nodes[node1].append(node2)
            node_to_connected_nodes[node2].append(node1)
        
        visited_nodes = set()
        def visit_connected_nodes(start_node: int) -> None:
            visited_nodes.add(start_node)
            nodes_to_visit_connected_nodes = [start_node]

            while nodes_to_visit_connected_nodes:
                node = nodes_to_visit_connected_nodes.pop()
                connected_nodes = node_to_connected_nodes[node]
                for connected_node in connected_nodes:
                    if connected_node in visited_nodes:
                        continue
                    visited_nodes.add(connected_node)
                    nodes_to_visit_connected_nodes.append(connected_node)
        
        result = 0
        for node in range(n):
            if node in visited_nodes:
                continue
            visit_connected_nodes(node)
            result += 1
        return result


```

## （必要に応じて）最適化
stackに入るのが訪れるべきノードじゃなくて、そのノードの隣接を訪れてほしいという意味になってるのでややこしい
ただ、stackをnode_to_visitにすると、popした後にvisited_nodesに追加することになる。こうなると重複してvisited_nodesに追加される可能性が出てしまう。
特に閉路がある場合は無限ループになってしまいそう。

# step2: 30分
## 他の人のPRやコメントを踏まえて実装
leetcodeの回答を見るとnode_to_connected_nodesを作ってから、再帰的にvisitedを更新している
すごくシンプル
他のPRも大体同じ実装。一部UnionFindを利用していた。

```python
from collections import defaultdict


class Solution:
    def countComponents(self, n: int, edges: List[List[int]]) -> int:
        node_to_adjacent = defaultdict(list)
        for node1, node2 in edges:
            node_to_adjacent[node1].append(node2)
            node_to_adjacent[node2].append(node1)
        
        visited = set()
        def traverse_connected_components(start_node: int) -> None:
            visited.add(start_node)
            adjacent_nodes = node_to_adjacent[start_node]
            for adjacent_node in adjacent_nodes:
                if adjacent_node in visited:
                    continue
                traverse_connected_components(adjacent_node)
        
        result = 0
        for node in range(n):
            if node in visited:
                continue
            traverse_connected_components(node)
            result += 1
        return result
```


# step3: 15分
※間違えがあればn回目を増やす

## 1回目
```python
from collections import defaultdict


class Solution:
    def countComponents(self, n: int, edges: List[List[int]]) -> int:
        node_to_adjacent = defaultdict(list)
        for node1, node2 in edges:
            node_to_adjacent[node1].append(node2)
            node_to_adjacent[node2].append(node1)
        
        visited = set()
        def traverse_connected_components(start_node: int) -> None:
            visited.add(start_node)
            adjacent_nodes = node_to_adjacent[start_node]

            for adjacent_node in adjacent_nodes:
                if adjacent_node in visited:
                    continue
                visited.add(adjacent_node)
                traverse_connected_components(adjacent_node)
        
        result = 0
        for node in range(n):
            if node in visited:
                continue
            traverse_connected_components(node)
            result += 1
        return result
```

## 2回目
traverse_connected_components内でfor loop内のvisited.add(adjacent_node)を削除した
引数名をstart_nodeからnodeに変更した

```python
from collections import defaultdict


class Solution:
    def countComponents(self, n: int, edges: List[List[int]]) -> int:
        node_to_adjacent = defaultdict(list)
        for node1, node2 in edges:
            node_to_adjacent[node1].append(node2)
            node_to_adjacent[node2].append(node1)
        
        visited = set()
        def traverse_connected_components(node: int) -> None:
            visited.add(node)
            adjacent_nodes = node_to_adjacent[node]

            for adjacent_node in adjacent_nodes:
                if adjacent_node in visited:
                    continue
                traverse_connected_components(adjacent_node)
        
        result = 0
        for node in range(n):
            if node in visited:
                continue
            traverse_connected_components(node)
            result += 1
        return result
```

## 3回目
```python
from collections import defaultdict


class Solution:
    def countComponents(self, n: int, edges: List[List[int]]) -> int:
        node_to_adjacent = defaultdict(list)
        for node1, node2 in edges:
            node_to_adjacent[node1].append(node2)
            node_to_adjacent[node2].append(node1)
        
        visited = set()
        def traverse_connected_components(node: int) -> None:
            visited.add(node)

            adjacent_nodes = node_to_adjacent[node]
            for adjacent_node in adjacent_nodes:
                if adjacent_node in visited:
                    continue
                traverse_connected_components(adjacent_node)
        
        result = 0
        for node in range(n):
            if node in visited:
                continue
            traverse_connected_components(node)
            result += 1
        return result
```