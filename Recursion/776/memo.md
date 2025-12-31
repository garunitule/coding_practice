# 進め方
- step1: 5分考えて分からなかったら答えを見る。答えを理解したら、答えを隠して書く。筆が進まず5分立ったら答えを見る。答えを送信して正解するまで。
- step2: コードを読みやすく整える。動くコードになったら終了。
- step3: 時間を計りながら書く。10分以内に3回連続でアクセプトされるまで。

# step1: 15分
深さ優先探索でたどりながら、target以下の部分木とtargetより大きい部分木を作れると思ったけど、うまく手順を言語化できなかった。
解答を見る。

# step2: 30分
部分問題に分解して再帰で実装している。

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

# step3: 15分
※間違えがあればn回目を増やす

## 1回目

## 2回目

## 3回目