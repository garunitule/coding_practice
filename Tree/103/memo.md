# 進め方
- step1: 5分考えて分からなかったら答えを見る。答えを理解したら、答えを隠して書く。筆が進まず5分立ったら答えを見る。答えを送信して正解するまで。
- step2: コードを読みやすく整える。動くコードになったら終了。
- step3: 時間を計りながら書く。10分以内に3回連続でアクセプトされるまで。

# step1: 15分
## 方針を考える
まず問題設定的に、BFSで探索するのが良さそう
偶数段目を逆順にすればいい

## 実装
```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def zigzagLevelOrder(self, root: Optional[TreeNode]) -> List[List[int]]:
        if root is None:
            return []
        
        result = []
        nodes = [root]
        is_reversed_traverse_order = False
        while nodes:
            if is_reversed_traverse_order:
                result.append([node.val for node in nodes[::-1]])
            else:
                result.append([node.val for node in nodes])
            
            new_nodes = []
            for node in nodes:
                if node.left is not None:
                    new_nodes.append(node.left)
                if node.right is not None:
                    new_nodes.append(node.right)
            nodes = new_nodes
            is_reversed_traverse_order = not is_reversed_traverse_order 
    
        return result
```

# step2: 30分
## 時間の関係でstep1で書けなかった迷いや判断を書く
whileの中の繰り返しをまとめたかったが、まとめる方法を思いつかなかった

## 他の人のPRやコメントを踏まえて実装
https://github.com/h1rosaka/arai60/pull/30/files
最初に次のlevelのnodesを作ってからnode.valを突っ込む実装
この実装のほうが少し読みやすい気がする

step2の実装で1つのfor文で値の格納と次のlevelのnode格納をやっている
たしかにdeque使えばできるか
そのためだけにdeque使うのは少し気になったが、好みか（for文を減らすための工夫なのにdequeからlistに変換する際に要素数分だけコピーが発生すると思ったので）

https://github.com/quinn-sasha/leetcode/pull/26/files#r2188598474
[::-1]を使うとコピーを作るので、in-placeのreverseを使おうというコメント
選択肢として思いついたけど、自分が想定する実装だと逆順にすると後続の次のlevelのnode作る際に困ると思ってやめた
ただ、最初に次のlevelのnodesを作っておけば影響しないのでその方がよさそう

```python
class Solution:
    def zigzagLevelOrder(self, root: Optional[TreeNode]) -> List[List[int]]:
        if root is None:
            return []
        
        result = []
        is_reverse_traverse_order = False
        level_nodes = [root]
        while level_nodes:
            next_level_nodes = []
            for node in level_nodes:
                if node.left is not None:
                    next_level_nodes.append(node.left)
                if node.right is not None:
                    next_level_nodes.append(node.right)
            
            if is_reverse_traverse_order:
                level_nodes.reverse()
            result.append([node.val for node in level_nodes])

            is_reverse_traverse_order = not is_reverse_traverse_order
            level_nodes = next_level_nodes

        return result
```


# step3: 15分
※間違えがあればn回目を増やす


## 1回目
```python
class Solution:
    def zigzagLevelOrder(self, root: Optional[TreeNode]) -> List[List[int]]:
        if root is None:
            return []
        
        result = []
        is_reverse_traverse_order = False
        level_nodes = [root]
        while level_nodes:
            next_level_nodes = []
            for node in level_nodes:
                if node.left is not None:
                    next_level_nodes.append(node.left)
                if node.right is not None:
                    next_level_nodes.append(node.right)
            
            if is_reverse_traverse_order:
                level_nodes.reverse()
            result.append([node.val for node in level_nodes])

            is_reverse_traverse_order = not is_reverse_traverse_order
            level_nodes = next_level_nodes
 
        return result
```

## 2回目
```python
class Solution:
    def zigzagLevelOrder(self, root: Optional[TreeNode]) -> List[List[int]]:
        if root is None:
            return []
        
        result = []
        is_reverse_traverse_order = False
        level_nodes = [root]
        while level_nodes:
            next_level_nodes = []
            for node in level_nodes:
                if node.left is not None:
                    next_level_nodes.append(node.left)
                if node.right is not None:
                    next_level_nodes.append(node.right)
            
            if is_reverse_traverse_order:
                level_nodes.reverse()
            result.append([node.val for node in level_nodes])

            is_reverse_traverse_order = not is_reverse_traverse_order
            level_nodes = next_level_nodes 

        return result
```

## 3回目
```python
class Solution:
    def zigzagLevelOrder(self, root: Optional[TreeNode]) -> List[List[int]]:
        if root is None:
            return []
        
        result = []
        is_reverse_traverse_order = False
        level_nodes = [root]
        while level_nodes:
            next_level_nodes = []
            for node in level_nodes:
                if node.left is not None:
                    next_level_nodes.append(node.left)
                if node.right is not None:
                    next_level_nodes.append(node.right)
            
            if is_reverse_traverse_order:
                level_nodes.reverse()
            result.append([node.val for node in level_nodes])

            is_reverse_traverse_order = not is_reverse_traverse_order
            level_nodes = next_level_nodes

        return result
```