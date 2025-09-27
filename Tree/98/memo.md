# 進め方
- step1: 5分考えて分からなかったら答えを見る。答えを理解したら、答えを隠して書く。筆が進まず5分立ったら答えを見る。答えを送信して正解するまで。
- step2: コードを読みやすく整える。動くコードになったら終了。
- step3: 時間を計りながら書く。10分以内に3回連続でアクセプトされるまで。

# step1: 15分
## 方針を考える
下から渡していくなら、左なら部分木の最大値、右なら部分木の最小値を返してもらって現在のノードの値と比較すればできそう？
left_subtree_max < node.val < right_subtree_max が成立条件で、それ以外は許容しない

上から渡していく方式だと、根 -> 右 -> 左　みたいなパターンがあって考えづらい

実装イメージがあまりわかないので回答を見る
部分問題に分解できそうでできない

回答を見た
なるほど、上から渡していく方式で実現している
下記コードは大筋はleetcodeと一緒だが、一部アレンジしている
```python
class Solution:
    def isValidBST(self, root: Optional[TreeNode]) -> bool:
        def validate_bst(node, low=-float('inf'), high=float('inf')) -> bool:
            if node is None:
                return True
            
            if not (low < node.val < high):
                return False
            
            return validate_bst(node.left, low, node.val) and validate_bst(node.right, node.val, high)
        
        return validate_bst(root)
```
再帰の分割の仕方について考えを整理する
- 左部分木の確認：左部分木の全ノードの値は現在のノードの値より小さいので、lowにlowを、highにnode.valを渡している
- 右部分木の確認：右部分木の全ノードの値は現在のノードの値より大きいので、lowにnode.valを、highにhighを渡している


## 実装
```python
class Solution:
    def isValidBST(self, root: Optional[TreeNode]) -> bool:
        def validate_bst(node, low=-float('inf'), high=float('inf')):
            if node is None:
                return True
            
            if not (low < node.val < high):
                return False
            
            return validate_bst(node.left, low, node.val) and validate_bst(node.right, node.val, high)

        return validate_bst(root)
``` 

# step2: 30分
## 他の人のPRやコメントを踏まえて実装
leetcodeの他の回答を見る

DFSをwhile loopで実現する方法

```python
class Solution:
    def isValidBST(self, root: Optional[TreeNode]) -> bool:
        stack = [(root, -float('inf'), float('inf'))]
        while stack:
            node, low, high = stack.pop()
            if node is None:
                continue
            
            if not (low < node.val < high):
                return False
            
            stack.append((node.left, low, node.val))
            stack.append((node.right, node.val, high))
        
        return True
```

DFS Inorderだとたしかに left -> node -> rightの順番になる
だから直前の訪問時の値を保持しておいて、「直前の訪問時の値 < 現在のノードの値」を満たしていなければ、その時点でFalseということ

```python
class Solution:
    def isValidBST(self, root: Optional[TreeNode]) -> bool:
        prev_val = -float('inf')

        def traverse(node: Optional[TreeNode]) -> bool:
            nonlocal prev_val
            if node is None:
                return True
            
            if not traverse(node.left):
                return False

            if node.val <= prev_val:
                return False

            prev_val = node.val
            return traverse(node.right)    

        return traverse(root)
```

https://github.com/quinn-sasha/leetcode/pull/27/files
解法としては上述した3パターン

https://github.com/ryosuketc/leetcode_arai60/pull/28/files
inorderかつyield fromを活用したパターン
面白い

個人的には再帰が好きだが再帰を使いがちなので、step3はstackを使ったDFSで実装する
inorderも慣れたいのでstep3で実装する

# step3: 15分
※間違えがあればn回目を増やす

## stackを使ったDFS
### 1回目
```python
class Solution:
    def isValidBST(self, root: Optional[TreeNode]) -> bool:
        stack = [(root, -float('inf'), float('inf'))]
        while stack:
            node, low, high = stack.pop()
            if node is None:
                continue
            if not (low < node.val < high):
                return False
            stack.append((node.left, low, node.val))
            stack.append((node.right, node.val, high))
        
        return True
```

### 2回目
```python
class Solution:
    def isValidBST(self, root: Optional[TreeNode]) -> bool:
        stack = [(root, -float('inf'), float('inf'))]
        while stack:
            node, low, high = stack.pop()

            if node is None:
                continue

            if not (low < node.val < high):
                return False

            stack.append((node.left, low, node.val))
            stack.append((node.right, node.val, high))

        return True
```

### 3回目
```python
class Solution:
    def isValidBST(self, root: Optional[TreeNode]) -> bool:
        stack = [(root, -float('inf'), float('inf'))]
        while stack:
            node, low, high = stack.pop()
            
            if node is None:
                continue

            if not (low < node.val < high):
                return False
            
            stack.append((node.left, low, node.val))
            stack.append((node.right, node.val, high))

        return True
```

## inorderを使ったDFS
### 1回目
```python
class Solution:
    def isValidBST(self, root: Optional[TreeNode]) -> bool:
        prev_val = -float('inf')

        def traverse(node: Optional[TreeNode]) -> bool:
            nonlocal prev_val
            
            if node is None:
                return True
            
            if not traverse(node.left):
                return False
            
            if node.val <= prev_val:
                return False

            prev_val = node.val
            return traverse(node.right) 

        return traverse(root)
```

### 2回目
```python
class Solution:
    def isValidBST(self, root: Optional[TreeNode]) -> bool:
        prev_val = -float('inf')
        
        def validate_bst(node: Optional[TreeNode]) -> bool:
            nonlocal prev_val
            if node is None:
                return True
            
            if not validate_bst(node.left):
                return False
            
            if node.val <= prev_val:
                return False
            
            prev_val = node.val
            return validate_bst(node.right)

        return validate_bst(root)
```

### 3回目
```python
class Solution:
    def isValidBST(self, root: Optional[TreeNode]) -> bool:
        prev_val = -float('inf')

        def validate_bst(node: Optional[TreeNode]) -> bool:
            nonlocal prev_val

            if node is None:
                return True
            
            if not validate_bst(node.left):
                return False
            
            if node.val <= prev_val:
                return False
            
            prev_val = node.val
            return validate_bst(node.right)

        return validate_bst(root)
```