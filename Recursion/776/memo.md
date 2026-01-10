# 進め方
- step1: 5分考えて分からなかったら答えを見る。答えを理解したら、答えを隠して書く。筆が進まず5分立ったら答えを見る。答えを送信して正解するまで。
- step2: コードを読みやすく整える。動くコードになったら終了。
- step3: 時間を計りながら書く。10分以内に3回連続でアクセプトされるまで。

# step1: 15分
深さ優先探索でたどりながら、target以下の部分木とtargetより大きい部分木を作れると思ったけど、うまく手順を言語化できなかった。
解答を見る。

# step2: 30分
部分問題に分解して再帰で実装している。
root.val <= targetの場合は、root.left以降の部分木のノードは全てtarget以下なのが確定する。そのため、rightを分割すればいい。
root.val > targetの場合はその逆。

```python
class Solution:
    def splitBST(self, root: Optional[TreeNode], target: int) -> List[Optional[TreeNode]]:
        if not root:
            return [None, None]
        
        if root.val <= target:
            right = self.splitBST(root.right, target)
            root.right = right[0]
            return [root, right[1]]
        else:
            left = self.splitBST(root.left, target)
            root.left = left[1]
            return [left[0], root]
```

再帰なのでループでも実装できる。
呼び出し順でスタックに積んで、popで取り出して構築すればよい。
取り出してnode.val <= targetの場合、node.left以下はtarget以下なのが確定している。node.right以降のうちtarget以下のノードで構築された部分木がlowerに入っているので、node.right = lowerでいい。
```python
class Solution:
    def splitBST(self, root: Optional[TreeNode], target: int) -> List[Optional[TreeNode]]:
        split_path_stack = []
        while root:
            split_path_stack.append(root)
            if root.val <= target:
                root = root.right
            else:
                root = root.left
        
        lower = upper = None
        while split_path_stack:
            node = split_path_stack.pop()
            if node.val <= target:
                node.right = lower
                lower = node
            else:
                node.left = upper
                upper = node

        return [lower, upper]
```

https://github.com/goto-untrapped/Arai60/pull/54#discussion_r1780641914
図をイメージすると分かりやすくなると感じた。
元々与えられたrootがあって、target以下の領域とtargetより大きい領域がどのように確定していくかイメージできたらコードがすっと書けた。

# step3: 15分
※間違えがあればn回目を増やす
再帰の方が分かりやすいが、理解の確認を兼ねてループで実装する。

## 1回目
```python
class Solution:
    def splitBST(self, root: Optional[TreeNode], target: int) -> List[Optional[TreeNode]]:
        split_path_stack = []
        while root:
            split_path_stack.append(root)
            if root.val <= target:
                root = root.right
            else:
                root = root.left
        
        lower = upper = None
        while split_path_stack:
            node = split_path_stack.pop()
            if node.val <= target:
                node.right = lower
                lower = node
            else:
                node.left = upper
                upper = node
        
        return [lower, upper]
```

## 2回目
```python
class Solution:
    def splitBST(self, root: Optional[TreeNode], target: int) -> List[Optional[TreeNode]]:
        split_path_stack = []
        while root:
            split_path_stack.append(root)
            if root.val <= target:
                root = root.right
            else:
                root = root.left
        
        lower = upper = None
        while split_path_stack:
            node = split_path_stack.pop()
            if node.val <= target:
                node.right = lower
                lower = node
            else:
                node.left = upper
                upper = node
        
        return [lower, upper]
```

## 3回目
```python
class Solution:
    def splitBST(self, root: Optional[TreeNode], target: int) -> List[Optional[TreeNode]]:
        split_path_stack = []
        while root:
            split_path_stack.append(root)
            if root.val <= target:
                root = root.right
            else:
                root = root.left
        
        lower = upper = None
        while split_path_stack:
            node = split_path_stack.pop()
            if node.val <= target:
                node.right = lower
                lower = node
            else:
                node.left = upper
                upper = node
        
        return [lower, upper]

```