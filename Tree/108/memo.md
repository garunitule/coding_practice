# 進め方
- step1: 5分考えて分からなかったら答えを見る。答えを理解したら、答えを隠して書く。筆が進まず5分立ったら答えを見る。答えを送信して正解するまで。
- step2: コードを読みやすく整える。動くコードになったら終了。
- step3: 時間を計りながら書く。10分以内に3回連続でアクセプトされるまで。

# step1: 15分
## 方針を考える
ふわっとしたイメージだが、
- numsの真ん中を選ぶ
- 真ん中の値を現在のノードの値にして左右に分割する
のようなことを繰り返せばできそう？

で、分割を繰り返した場合に
- numsの長さが1: その要素がそのままノードになる
が終了条件になりそう

再帰が表現しやすそう
stackやqueueでもできそうだけど

## 実装
いったん動くコードは書けた
nをnumsの長さとしたとき、
時間計算量はO(n)
空間計算量もO(n)

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def sortedArrayToBST(self, nums: List[int]) -> Optional[TreeNode]:
        if len(nums) == 0:
            return None

        def to_height_balanced_binary_search_tree(source_node: TreeNode, nums: list[int]) -> None:
            if len(nums) == 1:
                source_node.val = nums[0]
                return

            middle_index = len(nums) // 2
            source_node.val = nums[middle_index]

            left_nums = nums[:middle_index]
            source_node.left = TreeNode()
            to_height_balanced_binary_search_tree(source_node.left, left_nums)

            right_nums = nums[middle_index+1:]
            if 0 < len(right_nums):
                source_node.right = TreeNode()
                to_height_balanced_binary_search_tree(source_node.right, right_nums)
        

        result = TreeNode()
        to_height_balanced_binary_search_tree(result, nums)
        return result
```

## （必要に応じて）最適化
やや認知負荷が高いコードなので、もっとシンプルにできるか考える
処理速度はこれ以上早くならないはずなので考えない（一度は要素に訪れる必要があるから）

下記のようにシンプルにできた
leftは要素数が1以上なのに対し、rightは要素数が0になることもあるが、処理は同じで良い
要素数が0だったらNoneで返るだけなので

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def sortedArrayToBST(self, nums: List[int]) -> Optional[TreeNode]:
        if len(nums) == 0:
            return None
        
        result = TreeNode()
        middle_index = len(nums) // 2
        result.val = nums[middle_index]

        if len(nums) == 1:
            return result

        left_nums = nums[:middle_index]
        result.left = self.sortedArrayToBST(left_nums)

        right_nums = nums[middle_index+1:]
        result.right = self.sortedArrayToBST(right_nums)
        
        return result
```


## テストケースを考える
最初の実装するとき、要素数が1, 2, 3以上で考えていた
結局要素が1のときだけ終了条件に含めて、それ以外は再帰関数で処理すればよいことに気づいた

# step2: 30分
## 時間の関係でstep1で書けなかった迷いや判断を書く
ノードを構築する場合は下から作った方が分かりやすいので下から構築した
上から構築する方法だと値を設定するノードを上から渡す必要があって複雑になると考えた

## 他の人のPRやコメントを踏まえて実装
leetcodeの解法
height-balancedだから空間計算量はO(logn)か

https://github.com/ryosuketc/leetcode_arai60/pull/24/files#diff-868565f6ce7d3b57fc6a8a22e86a00e6ca759ee32f9b513c23eeb031594441a7
たしかにもっとスッキリ書けるな
len(nums) == 1の場合に早期returnすると少し処理をスキップできるけど、大きな差はなさそうなので読みやすさ優先した方が良いな

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def sortedArrayToBST(self, nums: List[int]) -> Optional[TreeNode]:
        if not nums:
            return None
        
        middle_index = len(nums) // 2
        result = TreeNode(nums[middle_index])

        left_nums = nums[:middle_index]
        result.left = self.sortedArrayToBST(left_nums)

        right_nums = nums[middle_index+1:]
        result.right = self.sortedArrayToBST(right_nums)
        
        return result
```

indexを狭めていく方式もある
```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def sortedArrayToBST(self, nums: List[int]) -> Optional[TreeNode]:
        if not nums:
            return None
        
        def subtree_to_bst(start_index: int, end_index: int) -> TreeNode:
            if start_index > end_index:
                return None

            middle_index = (start_index + end_index) // 2
            root = TreeNode(nums[middle_index])
            root.left = subtree_to_bst(start_index, middle_index - 1)
            root.right = subtree_to_bst(middle_index + 1, end_index)
            return root
        
        return subtree_to_bst(0, len(nums) - 1)
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
    def sortedArrayToBST(self, nums: List[int]) -> Optional[TreeNode]:
        if not nums:
            return None
        
        middle_index = len(nums) // 2
        root = TreeNode(nums[middle_index])
        root.left = self.sortedArrayToBST(nums[:middle_index])
        root.right = self.sortedArrayToBST(nums[middle_index+1:])
        return root
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
    def sortedArrayToBST(self, nums: List[int]) -> Optional[TreeNode]:
        if not nums:
            return None
        
        middle_index = len(nums) // 2
        root = TreeNode(nums[middle_index])
        root.left = self.sortedArrayToBST(nums[:middle_index])
        root.right = self.sortedArrayToBST(nums[middle_index+1:])
        return root
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
    def sortedArrayToBST(self, nums: List[int]) -> Optional[TreeNode]:
        if not nums:
            return None
        
        middle_index = len(nums) // 2
        root = TreeNode(nums[middle_index])
        root.left = self.sortedArrayToBST(nums[:middle_index])
        root.right = self.sortedArrayToBST(nums[middle_index+1:])
        return root
```