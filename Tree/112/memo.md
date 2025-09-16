
# 進め方
- step1: 5分考えて分からなかったら答えを見る。答えを理解したら、答えを隠して書く。筆が進まず5分立ったら答えを見る。答えを送信して正解するまで。
- step2: コードを読みやすく整える。動くコードになったら終了。
- step3: 時間を計りながら書く。10分以内に3回連続でアクセプトされるまで。

# step1: 15分
## 方針を考える
方針はDFSが良い。rootからleafまでの経路でtargetSumに等しい場合はそこで探索終了すればいいので
次に再帰とstackがあるけど、hasPathSumの引数にtargetSumがあるので再帰で出来そう

## 実装
root = NoneかつtargetSum == 0の場合にTrueを返すようにすればもっとシンプルにできるかもと思ったが、問題の条件でFalseを返す必要があるので、leafの判定処理を加えた
また、leftに対してhasPathSumを適用してTrueだったらrightは見なくていいのでorを利用した

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def is_leaf(self, root: TreeNode) -> bool:
        return root.left is None and root.right is None

    def hasPathSum(self, root: Optional[TreeNode], targetSum: int) -> bool:
        if root is None:
            return False
        
        if self.is_leaf(root):
            return targetSum == root.val
        
        return self.hasPathSum(root.left, targetSum - root.val) or self.hasPathSum(root.right, targetSum - root.val)
```

## （必要に応じて）最適化
特にない

# step2: 30分
## 自分で実装
再帰でも実装できるので実装
```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def is_leaf(self, root: TreeNode) -> bool:
        return root.left is None and root.right is None

    def hasPathSum(self, root: Optional[TreeNode], targetSum: int) -> bool:
        if not root:
            return False

        nodes = [(root, targetSum)]
        while nodes:
            node, remaining_sum = nodes.pop()
            if not node:
                continue

            remaining_sum -= node.val
            if self.is_leaf(node) and remaining_sum == 0:
                return True

            nodes.append((node.left, remaining_sum))
            nodes.append((node.right, remaining_sum))
        return False
```

## 他の人のPRやコメントを踏まえて実装
和を渡していき、leafに到達したら和とtargetSumが等しいか判定する方法もある
その方が分かりやすいか？
https://github.com/ryosuketc/leetcode_arai60/pull/25/files

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
    def is_leaf(self, root: TreeNode) -> bool:
        return root.left is None and root.right is None

    def hasPathSum(self, root: Optional[TreeNode], targetSum: int) -> bool:
        if not root:
            return False

        nodes_and_sum_so_far = [(root, 0)]
        while nodes_and_sum_so_far:
            node, sum_so_far = nodes_and_sum_so_far.pop()
            if not node:
                continue
            
            sum_so_far += node.val
            if self.is_leaf(node) and sum_so_far == targetSum:
                return True
            
            nodes_and_sum_so_far.append((node.left, sum_so_far))
            nodes_and_sum_so_far.append((node.right, sum_so_far))
        return False
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
    def is_leaf(self, root: TreeNode) -> bool:
        return root.left is None and root.right is None

    def hasPathSum(self, root: Optional[TreeNode], targetSum: int) -> bool:
        if root is None:
            return False

        nodes_and_sum_so_far = [(root, 0)]
        while nodes_and_sum_so_far:
            node, sum_so_far = nodes_and_sum_so_far.pop()
            if node is None:
                continue
            
            sum_so_far += node.val
            if self.is_leaf(node) and sum_so_far == targetSum:
                return True
            
            nodes_and_sum_so_far.append((node.left, sum_so_far))
            nodes_and_sum_so_far.append((node.right, sum_so_far))
        return False
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
    def _is_leaf(self, root: TreeNode) -> bool:
        return root.left is None and root.right is None

    def hasPathSum(self, root: Optional[TreeNode], targetSum: int) -> bool:
        if root is None:
            return False

        nodes_and_sum_so_far = [(root, 0)]
        while nodes_and_sum_so_far:
            node, sum_so_far = nodes_and_sum_so_far.pop()
            if node is None:
                continue

            sum_so_far += node.val
            if self._is_leaf(node) and sum_so_far == targetSum:
                return True

            nodes_and_sum_so_far.append((node.left, sum_so_far))
            nodes_and_sum_so_far.append((node.right, sum_so_far))
        return False        
```