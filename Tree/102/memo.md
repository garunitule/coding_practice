# 進め方
- step1: 5分考えて分からなかったら答えを見る。答えを理解したら、答えを隠して書く。筆が進まず5分立ったら答えを見る。答えを送信して正解するまで。
- step2: コードを読みやすく整える。動くコードになったら終了。
- step3: 時間を計りながら書く。10分以内に3回連続でアクセプトされるまで。

# step1: 15分
## 方針を考える
BFSでnodeと高さを一緒に渡せばできそう

## 実装
```python
from collections import deque


class Solution:
    def levelOrder(self, root: Optional[TreeNode]) -> List[List[int]]:
        result = []
        if root is None:
            return result
        
        nodes_and_level = deque([(root, 1)])
        while nodes_and_level:
            node, level = nodes_and_level.popleft()
            if node is None:
                continue

            if len(result) < level:
                result.append([])
            result[level - 1].append(node.val)

            nodes_and_level.append((node.left, level + 1))
            nodes_and_level.append((node.right, level + 1))
        return result
``` 

# step2: 30分
## 他の人のPRやコメントを踏まえて実装
https://github.com/quinn-sasha/leetcode/pull/25/files
同じレベルのノードだけをまとめる方法、この方が分かりやすい
```python
class Solution:
    def levelOrder(self, root: Optional[TreeNode]) -> List[List[int]]:
        result = []
        if root is None:
            return result
        
        nodes = [root]
        while nodes:
            result.append([node.val for node in nodes])
            child_nodes = []
            for node in nodes:
                if node.left is not None:
                    child_nodes.append(node.left)
                if node.right is not None:
                    child_nodes.append(node.right)
            nodes = child_nodes
        return result
            
```

https://discordapp.com/channels/1084280443945353267/1200089668901937312/1211248049884499988
たしかに、複数段に対応した方が優しいか？
それか1段であることを明記するとか？

# step3: 15分
※間違えがあればn回目を増やす

## 1回目
```python
class Solution:
    def levelOrder(self, root: Optional[TreeNode]) -> List[List[int]]:
        result = []
        if root is None:
            return result
        
        nodes = [root]
        while nodes:
            result.append([node.val for node in nodes])
            next_level_nodes = []
            for node in nodes:
                if node.left is not None:
                    next_level_nodes.append(node.left)
                if node.right is not None:
                    next_level_nodes.append(node.right)
            nodes = next_level_nodes
        return result
```

## 2回目
```python
class Solution:
    def levelOrder(self, root: Optional[TreeNode]) -> List[List[int]]:
        if root is None:
            return []
        
        result = []
        nodes = [root]
        while nodes:
            result.append([node.val for node in nodes])
            next_level_nodes = []
            for node in nodes:
                if node.left is not None:
                    next_level_nodes.append(node.left)
                if node.right is not None:
                    next_level_nodes.append(node.right)
            nodes = next_level_nodes
        return result
```

## 3回目
```python
class Solution:
    def levelOrder(self, root: Optional[TreeNode]) -> List[List[int]]:
        if root is None:
            return []
        
        result = []
        nodes = [root]
        while nodes:
            result.append([node.val for node in nodes])
            next_level_nodes = []
            for node in nodes:
                if node.left is not None:
                    next_level_nodes.append(node.left)
                if node.right is not None:
                    next_level_nodes.append(node.right)
            nodes = next_level_nodes
        return result
```