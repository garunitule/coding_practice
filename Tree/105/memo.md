# 進め方
- step1: 5分考えて分からなかったら答えを見る。答えを理解したら、答えを隠して書く。筆が進まず5分立ったら答えを見る。答えを送信して正解するまで。
- step2: コードを読みやすく整える。動くコードになったら終了。
- step3: 時間を計りながら書く。10分以内に3回連続でアクセプトされるまで。

# step1: 15分
## 方針を考える
preorder, inorder片方だけだと木の構造が確定しないので、両方使って木を構築するということだと思った
手元でグラフを書きながら考えて、なんとなく下記のような感じだとは思う
- preorderの先頭は現在のノードに設定できる（最初は根）
- inorderを見て、preorderの先頭の値より前の要素は左部分木に入り、後の要素は右部分木に入る
- これを再帰的に実施し、探索範囲を狭めればできそう？

10分ほど実装を試みたができなそうなので回答を見る

回答を見た
preorder順にノードを構築している
inorderの範囲でどこに構築するか制御している
いまいちつかみきれてないので考えてみる

まず、inorderについて下記のように範囲を狭めればよい理由は、inorderは左右の位置関係を示しているから
```python
root.left = array_to_tree(left, inorder_index_map[root_value] - 1)
root.right = array_to_tree(inorder_index_map[root_value] + 1, right)
```
次にarray_to_tree内でleft > rightだったらNoneにしている理由は、子となる部分木がないことを示しているからか？
```python
if left > right:
    return None
```
left > rightとなる前は、left == rightとなっているはずで、その時すでにそのノードしか部分木に含まれないということ。だから、その子は存在しない。


## 実装
回答を見た後の実装
nonlocalでpreorder_indexを更新するよりも、引数に含めたほうが分かりやすいと判断し、下記のような実装にした

```python
class Solution:
    def buildTree(self, preorder: List[int], inorder: List[int]) -> Optional[TreeNode]:
        if not preorder or not inorder:
            return None

        inorder_to_index = {v: k for k, v in enumerate(inorder)}

        def build_tree(inorder_left: int, inorder_right: int, preorder_index: int) -> Tuple[Optional[TreeNode], int]:
            if inorder_right < inorder_left:
                return None, preorder_index

            node_val = preorder[preorder_index]
            preorder_index += 1
            node = TreeNode(node_val)
            if inorder_left <= inorder_to_index[node_val] - 1: 
                node.left, preorder_index = build_tree(inorder_left, inorder_to_index[node_val] - 1, preorder_index)
            if inorder_to_index[node_val] + 1 <= inorder_right:
                node.right, preorder_index = build_tree(inorder_to_index[node_val] + 1, inorder_right, preorder_index)
            return node, preorder_index
        
        result, _ = build_tree(0, len(inorder) - 1, 0)
        return result
```

# step2: 30分
## 他の人のPRやコメントを踏まえて実装
https://github.com/ryosuketc/leetcode_arai60/pull/29/files
アルゴリズムまで落とし込むの難しいという感想。自分も同じ感想をもった。それぞれどのように使うイメージは何となくできたけど、すらすら書き上げるのは難しそうという印象。

https://github.com/tokuhirat/LeetCode/pull/29/files
変数名はval_to_inorder_indexのほうが分かりやすい

また、計算量がO(n ** 2)になってしまうが、元の関数での再帰も実装できる
現在のノードの値のinorder上での位置から左部分木のノード数が分かる
左部分木のノード数が分かれば、左部分木構築用のpreorderも構築できる

```python
class Solution:
    def buildTree(self, preorder: List[int], inorder: List[int]) -> Optional[TreeNode]:
        if not preorder or not inorder:
            return None

        node_val = preorder[0]
        node = TreeNode(node_val)
        num_left_subtree_nodes = inorder.index(node_val)
        node.left = self.buildTree(preorder[1 : num_left_subtree_nodes+1], inorder[:num_left_subtree_nodes])
        node.right = self.buildTree(preorder[num_left_subtree_nodes+1 :], inorder[num_left_subtree_nodes+1 :])
        return node
```

# step3: 15分
※間違えがあればn回目を増やす

## 1回目
```python
class Solution:
    def buildTree(self, preorder: List[int], inorder: List[int]) -> Optional[TreeNode]:
        if not preorder or not inorder:
            return None
        
        val_to_inorder_index = {v: k for k, v in enumerate(inorder)}
        def build_tree(preorder_index: int, inorder_left_index: int, inorder_right_index: int) -> Tuple[Optional[TreeNode], int]:
            node_val = preorder[preorder_index]
            node = TreeNode(node_val)
            inorder_index = val_to_inorder_index[node_val]
            preorder_index += 1
            if inorder_left_index <= inorder_index - 1:
                node.left, preorder_index = build_tree(preorder_index, inorder_left_index, inorder_index - 1)
            if inorder_index + 1 <= inorder_right_index:
                node.right, preorder_index = build_tree(preorder_index, inorder_index + 1, inorder_right_index)
            return node, preorder_index
        
        result, _ = build_tree(0, 0, len(inorder) - 1)
        return result
```

## 2回目
```python
class Solution:
    def buildTree(self, preorder: List[int], inorder: List[int]) -> Optional[TreeNode]:
        if not preorder or not inorder:
            return None
        
        val_to_inorder_index = {v: k for k, v in enumerate(inorder)}
        def build_tree(preorder_index: int, inorder_left_index: int, inorder_right_index: int) -> Tuple[Optional[TreeNode], int]:
            node = TreeNode(preorder[preorder_index])
            preorder_index += 1
            inorder_index = val_to_inorder_index[node.val]
            if inorder_left_index <= inorder_index - 1:
                node.left, preorder_index = build_tree(preorder_index, inorder_left_index, inorder_index - 1)
            if inorder_index + 1 <= inorder_right_index:
                node.right, preorder_index = build_tree(preorder_index, inorder_index + 1, inorder_right_index)
            return node, preorder_index 

        result, _ = build_tree(0, 0, len(inorder) - 1)
        return result
```

## 3回目
```python
class Solution:
    def buildTree(self, preorder: List[int], inorder: List[int]) -> Optional[TreeNode]:
        if not preorder or not inorder:
            return None
        
        val_to_inorder_index = {v: k for k, v in enumerate(inorder)}
        def build_tree(preorder_index: int, inorder_left_index: int, inorder_right_index: int) -> Tuple[Optional[TreeNode], int]:
            node = TreeNode(preorder[preorder_index])
            preorder_index += 1
            inorder_index = val_to_inorder_index[node.val]
            if inorder_left_index <= inorder_index - 1:
                node.left, preorder_index = build_tree(preorder_index, inorder_left_index, inorder_index - 1)
            if inorder_index + 1 <= inorder_right_index:
                node.right, preorder_index = build_tree(preorder_index, inorder_index + 1, inorder_right_index)
            return node, preorder_index
        
        result, _ = build_tree(0, 0, len(inorder) - 1)
        return result
```