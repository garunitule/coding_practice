# 進め方
- step1: 5分考えて分からなかったら答えを見る。答えを理解したら、答えを隠して書く。筆が進まず5分立ったら答えを見る。答えを送信して正解するまで。
- step2: コードを読みやすく整える。動くコードになったら終了。
- step3: 時間を計りながら書く。10分以内に3回連続でアクセプトされるまで。

# step1: 15分
## 方針を考える
leafだったらその時点の長さを返して最小値を保持する
DFSにする

## 実装
```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def is_leaf(self, node: TreeNode) -> bool:
        return node.left is None and node.right is None

    def minDepth(self, root: Optional[TreeNode]) -> int:
        if root is None:
            return 0
        if root.left is None and root.right is None:
            return 1
        
        stack = [(root, 1)]
        SENTINEL_DEPTH = 2 << 30
        min_depth = SENTINEL_DEPTH
        while stack:
            node, depth = stack.pop()
            if self.is_leaf(node):
                min_depth = min(depth, min_depth)
            if node.left is not None:
                stack.append((node.left, depth + 1))
            if node.right is not None:
                stack.append((node.right, depth + 1))
        return min_depth
```

## （必要に応じて）最適化
最適化じゃないけど、SENTINEL_DEPTHを分かりやすくしたい
float('inf')で良さそう
(追記)別で出しているPRで2の倍数である必要がなければ 1<<30のほうが良いというご指摘をいただいた

## テストケースを考える
rootがNone, rootのleft, rightがないとき、それ以外で分けて考えた 

# step2: 30分
## 他の人のPRやコメントを踏まえて実装
https://discord.com/channels/1084280443945353267/1196472827457589338/1237988315781664770
>そうですね。木の高さを求めるときには、上から数字を配っていくか、下から集めてくるかの2方向があって、それぞれ再帰で書くか、スタックとループで書くか、がありますね。

なるほど、自分の実装だと上から数字を配っているな
下から集める方法もある。ただ、下から集める方法は再帰じゃないと工夫が必要そう
（後で追記）Maximum Depth of Binary Treeでも上から数字を渡す方法と下から集める方法の2種類あるとご指摘をいただいた
https://github.com/garunitule/coding_practice/pull/21

https://github.com/shintaro1993/arai60/pull/26/files
minDepthを再帰的に呼び出す実装

https://github.com/tokuhirat/LeetCode/pull/22/files
こちらもminDepthを再帰的に呼び出す実装も書かれていた


DFS（再帰、上から数字を渡す）の実装
これ以上はシンプルにできなそう
leafへの最短距離という制約があるので

```python
class Solution:
    def minDepth(self, root: Optional[TreeNode]) -> int:
        if root is None:
            return 0

        def get_min_depth(root: Optional[TreeNode], depth: int) -> int:
            if root.left is None and root.right is None:
                return depth
            
            left_depth = float("inf")
            right_depth = float("inf")
            if root.left is not None:
                left_depth = get_min_depth(root.left, depth + 1)
            if root.right is not None:
                right_depth = get_min_depth(root.right, depth + 1)
            return min(left_depth, right_depth)

        return get_min_depth(root, 1)
```

DFS（再帰、下から集める方法）の実装
これ以上シンプルにはできなそう
leafへの最短距離という制約があるので

```python
class Solution:
    def minDepth(self, root: Optional[TreeNode]) -> int:
        if root is None:
            return 0
        if root.left is None and root.right is None:
            return 1
        
        min_depth = float("inf")
        if root.left is not None:
            min_depth = min(min_depth, self.minDepth(root.left) + 1)
        if root.right is not None:
            min_depth = min(min_depth, self.minDepth(root.right) + 1)
        return min_depth
```

DFS（再帰、いきがけ）の実装
インスタンス変数にmin_depthを格納する方法もあるが、スレッドセーフにならないのでやめる

```python
class Solution:
    def minDepth(self, root: Optional[TreeNode]) -> int:
        if root is None:
            return 0
        
        min_depth = float("inf")

        def get_min_depth(root: Optional[TreeNode], depth: int) -> None:
            if root is None:
                return
            
            nonlocal min_depth
            if root.left is None and root.right is None:
                min_depth = min(min_depth, depth)
                return
            
            get_min_depth(root.left, depth + 1)
            get_min_depth(root.right, depth + 1)

        get_min_depth(root, 1)
        return min_depth
```

## 最終的な選択
帰りがけで下から集める方法が一番シンプルわかりやすい
```python
class Solution:
    def minDepth(self, root: Optional[TreeNode]) -> int:
        if root is None:
            return 0
        if root.left is None and root.right is None:
            return 1
        
        min_depth = float("inf")
        if root.left is not None:
            min_depth = min(min_depth, self.minDepth(root.left) + 1)
        if root.right is not None:
            min_depth = min(min_depth, self.minDepth(root.right) + 1)
        return min_depth
```

# step3: 15分
※間違えがあればn回目を増やす

## 1回目
```python
class Solution:
    def minDepth(self, root: Optional[TreeNode]) -> int:
        if root is None:
            return 0
        if root.left is None and root.right is None:
            return 1
        
        min_depth = float("inf")
        if root.left is not None:
            min_depth = min(min_depth, self.minDepth(root.left) + 1)
        if root.right is not None:
            min_depth = min(min_depth, self.minDepth(root.right) + 1)
        return min_depth
```

## 2回目
leaf判定はメソッドに切りだした
本来ならTreeNodeにカプセル化すべき処理な気もするが

```python
class Solution:
    def _is_leaf(self, root: TreeNode) -> bool:
        return root.left is None and root.right is None

    def minDepth(self, root: Optional[TreeNode]) -> int:
        if root is None:
            return 0
        if self._is_leaf(root):
            return 1
        
        min_depth = float("inf")
        if root.left is not None:
            min_depth = min(min_depth, self.minDepth(root.left) + 1)
        if root.right is not None:
            min_depth = min(min_depth, self.minDepth(root.right) + 1)
        return min_depth
```

## 3回目
```python
class Solution:
    def _is_leaf(self, root: TreeNode) -> bool:
        return root.left is None and root.right is None

    def minDepth(self, root: Optional[TreeNode]) -> int:
        if root is None:
            return 0
        if self._is_leaf(root):
            return 1
        
        min_depth = float("inf")
        if root.left is not None:
            min_depth = min(min_depth, self.minDepth(root.left) + 1)
        if root.right is not None:
            min_depth = min(min_depth, self.minDepth(root.right) + 1)
        return min_depth
```