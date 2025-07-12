# 進め方
- step1: 5分考えて分からなかったら答えを見る。答えを理解したら、答えを隠して書く。筆が進まず5分立ったら答えを見る。答えを送信して正解するまで。
- step2: コードを読みやすく整える。動くコードになったら終了。
- step3: 時間を計りながら書く。10分以内に3回連続でアクセプトされるまで。

# step1: 15分
## 方針を考える
全てのノードを探索する必要がある
DFS, BFSどちらでもできそう
最大の深さを見つけることなので、直感的にDFSのほうが沿っていそう

## 実装
```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def maxDepth(self, root: Optional[TreeNode]) -> int:
        if not root:
            return 0

        stack = [(root, 1)]
        max_depth = 0
        while stack:
            node, depth = stack.pop()
            max_depth = max(max_depth, depth)

            if node.left:
                stack.append((node.left, depth + 1))
            if node.right:
                stack.append((node.right, depth + 1))
        return max_depth
```

## （必要に応じて）最適化
最適化ではないが再帰で書いてみる
```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def maxDepth(self, root: Optional[TreeNode]) -> int:
        if not root:
            return 0

        def get_max_depth(node: TreeNode, depth: int) -> int:
            current_depth = depth
            if node.left:
                current_depth = get_max_depth(node.left, depth + 1)
            if node.right:
                current_depth = max(current_depth, get_max_depth(node.right, depth + 1))
            return current_depth
            
        return get_max_depth(root, 1)
```

# step2: 30分
## 他の人のPRやコメントを踏まえて実装
https://github.com/Satorien/LeetCode/pull/21/files
maxDepth自体で再帰する実装
再帰にするならこちらの方が分かりやすいな

stackを使う場合にもnode.left, node.rightがNoneかどうか判定せずにstackに積む実装もある
処理はスッキリしそうだが、今回はそこまで処理が複雑じゃないので、個人的にはNoneかどうか判定したほうがいいかな

https://docs.python.org/ja/3.13/library/stdtypes.html#:~:text=is%20%E3%81%8A%E3%82%88%E3%81%B3%20is%20not%20%E6%BC%94%E7%AE%97%E5%AD%90%E3%81%AE%E6%8C%AF%E3%82%8B%E8%88%9E%E3%81%84%E3%81%AF%E3%82%AB%E3%82%B9%E3%82%BF%E3%83%9E%E3%82%A4%E3%82%BA%E3%81%A7%E3%81%8D%E3%81%BE%E3%81%9B%E3%82%93%E3%80%82%E3%81%BE%E3%81%9F%E3%80%81%E3%81%93%E3%82%8C%E3%82%89%E3%81%AF%E3%81%84%E3%81%8B%E3%81%AA%E3%82%8B%202%20%E3%81%A4%E3%81%AE%E3%82%AA%E3%83%96%E3%82%B8%E3%82%A7%E3%82%AF%E3%83%88%E3%81%AB%E3%82%82%E9%81%A9%E7%94%A8%E3%81%A7%E3%81%8D%E3%80%81%E6%B1%BA%E3%81%97%E3%81%A6%E4%BE%8B%E5%A4%96%E3%82%92%E9%80%81%E5%87%BA%E3%81%97%E3%81%BE%E3%81%9B%E3%82%93%E3%80%82
以前も調べたけど、インスタンスの比較はis使った方がいいな
is演算子のふるまいはカスタマイズできないので

再帰でもいいけど、stackを使った実装のほうがいいかな
```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def maxDepth(self, root: Optional[TreeNode]) -> int:
        if root is None:
            return 0
        
        stack = [(root, 1)]
        max_depth = 0
        while stack:
            node, depth = stack.pop()
            max_depth = max(max_depth, depth)
            if node.left is not None:
                stack.append((node.left, depth + 1))
            if node.right is not None:
                stack.append((node.right, depth + 1))
        return max_depth
```

# step3: 15分
※間違えがあればn回目を増やす

## 1回目
```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def maxDepth(self, root: Optional[TreeNode]) -> int:
        if root is None:
            return 0
        
        stack = [(root, 1)]
        max_depth = 0
        while stack:
            node, depth = stack.pop()
            max_depth = max(depth, max_depth)
            if node.left is not None:
                stack.append((node.left, depth + 1))
            if node.right is not None:
                stack.append((node.right, depth + 1))
        return max_depth
```

## 2回目
```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def maxDepth(self, root: Optional[TreeNode]) -> int:
        if root is None:
            return 0
        
        stack = [(root, 1)]
        max_depth = 0
        while stack:
            node, depth = stack.pop()
            max_depth = max(depth, max_depth)
            if node.left is not None:
                stack.append((node.left, depth + 1))
            if node.right is not None:
                stack.append((node.right, depth + 1))
        return max_depth
```

## 3回目
```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def maxDepth(self, root: Optional[TreeNode]) -> int:
        if root is None:
            return 0
        
        stack = [(root, 1)]
        max_depth = 0
        while stack:
            node, depth = stack.pop()
            max_depth = max(depth, max_depth)
            if node.left is not None:
                stack.append((node.left, depth + 1))
            if node.right is not None:
                stack.append((node.right, depth + 1))
        return max_depth
```