# 進め方
- step1: 5分考えて分からなかったら答えを見る。答えを理解したら、答えを隠して書く。筆が進まず5分立ったら答えを見る。答えを送信して正解するまで。
- step2: コードを読みやすく整える。動くコードになったら終了。
- step3: 時間を計りながら書く。10分以内に3回連続でアクセプトされるまで。

# step1: 15分
## 方針を考える
奇数段目は最初から走査して、偶数段目は後ろから走査すれば良さそう

## 実装
next_level_nodesへの格納順を逆にするのは大変なので、resultへの格納を逆順にするようにした

```python
class Solution:
    def zigzagLevelOrder(self, root: Optional[TreeNode]) -> List[List[int]]:
        if root is None:
            return []
        
        result = []
        nodes = [root]
        is_traverse_from_left = True
        while nodes:
            if is_traverse_from_left:
                result.append([node.val for node in nodes])
            else:
                result.append([node.val for node in nodes[::-1]])
            
            next_level_nodes = []
            for node in nodes:
                if node.left is not None:
                    next_level_nodes.append(node.left)
                if node.right is not None:
                    next_level_nodes.append(node.right)
            nodes = next_level_nodes
            is_traverse_from_left = not is_traverse_from_left

        return result
```

# step2: 30分
## 他の人のPRやコメントを踏まえて実装
leetcodeの解法を見た、
- BFSで実装している。番兵のようにあるレベルが終わる段階でNoneを入れて、resultへの格納順を制御している。自分の実装のほうが分かりやすい気がするが、どうかな
- DFSでも実装できる。ただ直感的ではない気がする。

https://github.com/quinn-sasha/leetcode/pull/26/files#r2188598474
[::-1]はコピーを作るので、reversedのほうが良いという指摘。たしかに。 
dequeでresultに突っ込む値を作成する方法もある

https://github.com/tokuhirat/LeetCode/pull/27/files
いったんどのlevelもleft_to_rightの順番で格納して、あとでlevelの偶奇を見てright_to_leftに反転させる方法もある

# step3: 15分
※間違えがあればn回目を増やす

## 1回目

## 2回目

## 3回目