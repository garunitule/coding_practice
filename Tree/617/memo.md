# 進め方
- step1: 5分考えて分からなかったら答えを見る。答えを理解したら、答えを隠して書く。筆が進まず5分立ったら答えを見る。答えを送信して正解するまで。
- step2: コードを読みやすく整える。動くコードになったら終了。
- step3: 時間を計りながら書く。10分以内に3回連続でアクセプトされるまで。

# step1: 15分
## 方針を考える
少なくとも片方にノードがある場合は処理をする必要がある
DFSでstackに(Tree1のノード, Tree2のノード)を積めばできると思ったけど、足したノードをどこに着けるかが難しいと思った

再帰だとできるか？root1, 2のいずれかまたは両方がNoneの場合を除けば下記のようなイメージ
return TreeNode(
    val = root1.val + root2.val,
    left = self.mergeTrees(root1.left, root2.left),
    right = self.mergeTrees(root1.right, root2.right)
)

両方がNoneの場合と片方がNoneの場合を最初に書いて早期returnすればできそう

## 実装
```python
class Solution:
    def mergeTrees(self, root1: Optional[TreeNode], root2: Optional[TreeNode]) -> Optional[TreeNode]:
        if root1 is None and root2 is None:
            return None
        if root1 is None:
            return root2
        if root2 is None:
            return root1

        return TreeNode(
            val = root1.val + root2.val,
            left = self.mergeTrees(root1.left, root2.left),
            right = self.mergeTrees(root1.right, root2.right)
        )
        
```

## （必要に応じて）最適化
再帰を使わずにstackでのDFSやqueueでのBFSはノードの構築が難しいと思うのでこれ以上方法はなさそう

# step2: 30分
## 他の人のPRやコメントを踏まえて実装
leetcodeの解法を見た
たしかに、root is None and root2 is Noneの分岐は不要だな

```python
class Solution:
    def mergeTrees(self, root1: Optional[TreeNode], root2: Optional[TreeNode]) -> Optional[TreeNode]:
        if root1 is None:
            return root2
        if root2 is None:
            return root1

        return TreeNode(
            val = root1.val + root2.val,
            left = self.mergeTrees(root1.left, root2.left),
            right = self.mergeTrees(root1.right, root2.right)
        )
```

leetcodeにstackを使った解法がある
確かに元の変数root1を変更して返す解法

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def mergeTrees(self, root1: Optional[TreeNode], root2: Optional[TreeNode]) -> Optional[TreeNode]:
        if root1 is None:
            return root2

        node_to_visit = [(root1, root2)]
        while node_to_visit:
            node1, node2 = node_to_visit.pop()
            if node2 is None:
                continue

            node1.val += node2.val
            if node1.left is None:
                node1.left = node2.left
            else:
                node_to_visit.append((node1.left, node2.left))
            if node1.right is None:
                node1.right = node2.right
            else:
                node_to_visit.append((node1.right, node2.right))

        return root1 
```

https://discord.com/channels/1084280443945353267/1233603535862628432/1277243820118900847
破壊的か非破壊的かは賛否ありそうだが、あるときは破壊的であるときは非破壊的なのは避けたい
step1の自分の実装はこれに該当してしまうか
leetcodeの解法も場合によって返り値がroot1/root2と変わるので認知負荷が高いしバグを埋め込む可能性を上げてしまう

https://discordapp.com/channels/1084280443945353267/1295357747545505833/1329746538880041031
https://discordapp.com/channels/1084280443945353267/1295357747545505833/1329746604114055191
Sumを格納するノードとそれぞれのノードをstackに格納する方法
この方法だと新しいTreeNodeを作れる

```python
class Solution:
    def mergeTrees(self, root1: Optional[TreeNode], root2: Optional[TreeNode]) -> Optional[TreeNode]:
        roots = list(filter(None, [root1, root2]))
        if not roots:
            return None
        
        merged_tree = TreeNode()
        nodes_to_merge = [(merged_tree, roots)]
        while nodes_to_merge:
            dst_node, src_nodes = nodes_to_merge.pop()
            dst_node.val = sum(n.val for n in src_nodes)
            lefts = list(filter(None, (n.left for n in src_nodes)))
            if lefts:
                dst_node.left = TreeNode()
                nodes_to_merge.append((dst_node.left, lefts))
            rights = list(filter(None, (n.right for n in src_nodes)))
            if rights:
                dst_node.right = TreeNode()
                nodes_to_merge.append((dst_node.right, rights))

        return merged_tree
```

# step3: 15分
※間違えがあればn回目を増やす
入力非破壊な方が良いので、メソッド内で返り値を作る方法を選択

## 1回目
```python
class Solution:
    def mergeTrees(self, root1: Optional[TreeNode], root2: Optional[TreeNode]) -> Optional[TreeNode]:
        roots = list(filter(None, [root1, root2]))
        if not roots:
            return None
        
        merged_tree = TreeNode()
        nodes_to_merge = [(merged_tree, roots)]
        while nodes_to_merge:
            dst_node, src_nodes = nodes_to_merge.pop()
            dst_node.val = sum(n.val for n in src_nodes)

            lefts = list(filter(None, (n.left for n in src_nodes)))
            if lefts:
                dst_node.left = TreeNode()
                nodes_to_merge.append((dst_node.left, lefts))

            rights = list(filter(None, (n.right for n in src_nodes)))
            if rights:
                dst_node.right = TreeNode()
                nodes_to_merge.append((dst_node.right, rights))
        return merged_tree
```

## 2回目
```python
class Solution:
    def mergeTrees(self, root1: Optional[TreeNode], root2: Optional[TreeNode]) -> Optional[TreeNode]:
        roots = list(filter(None, [root1, root2]))
        if not roots:
            return None
        
        merged_tree = TreeNode()
        nodes_to_merge = [(merged_tree, roots)]
        while nodes_to_merge:
            dst_node, src_nodes = nodes_to_merge.pop()
            dst_node.val = sum(n.val for n in src_nodes)

            lefts = list(filter(None, (n.left for n in src_nodes)))
            if lefts:
                dst_node.left = TreeNode()
                nodes_to_merge.append((dst_node.left, lefts))

            rights = list(filter(None, (n.right for n in src_nodes)))
            if rights:
                dst_node.right = TreeNode()
                nodes_to_merge.append((dst_node.right, rights))
        
        return merged_tree
```

## 3回目
```python
class Solution:
    def mergeTrees(self, root1: Optional[TreeNode], root2: Optional[TreeNode]) -> Optional[TreeNode]:
        roots = list(filter(None, [root1, root2]))
        if not roots:
            return None
        
        merged_tree = TreeNode()
        nodes_to_merge = [(merged_tree, roots)]
        while nodes_to_merge:
            dst_node, src_nodes = nodes_to_merge.pop()
            dst_node.val = sum(n.val for n in src_nodes)

            lefts = list(filter(None, (n.left for n in src_nodes)))
            if lefts:
                dst_node.left = TreeNode()
                nodes_to_merge.append((dst_node.left, lefts))
            
            rights = list(filter(None, (n.right for n in src_nodes)))
            if rights:
                dst_node.right = TreeNode()
                nodes_to_merge.append((dst_node.right, rights))

        return merged_tree
```