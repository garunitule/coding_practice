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

## テストケースを考える
rootがNone, rootのleft, rightがないとき、それ以外で分けて考えた 

# step2: 30分
## 他の人のPRやコメントを踏まえて実装
https://discord.com/channels/1084280443945353267/1196472827457589338/1237988315781664770
>そうですね。木の高さを求めるときには、上から数字を配っていくか、下から集めてくるかの2方向があって、それぞれ再帰で書くか、スタックとループで書くか、がありますね。

なるほど、自分の実装だと上から数字を配っているな
下から集める方法もあるのか。ただ、下から集める方法は再帰じゃないと工夫が必要そう

https://github.com/shintaro1993/arai60/pull/26/files
minDepthを再帰的に呼び出す実装

https://github.com/tokuhirat/LeetCode/pull/22/files
こちらもminDepthを再帰的に呼び出す実装も書かれていた

いきがけ、帰りがけ両方で実装してみよう

DFS（再帰、いきがけ）
```python
# TODO
```

DFS（再帰、帰りがけ）の最初の実装
```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
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

DFS（再帰、帰りがけ）のスッキリさせた実装
```python

```


# step3: 15分
※間違えがあればn回目を増やす

## 1回目

## 2回目

## 3回目