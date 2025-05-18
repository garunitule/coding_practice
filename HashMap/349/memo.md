# 進め方
- step1: 5分考えて分からなかったら答えを見る。答えを理解したら、答えを隠して書く。筆が進まず5分立ったら答えを見る。答えを送信して正解するまで。
- step2: コードを読みやすく整える。動くコードになったら終了。
- step3: 時間を計りながら書く。10分以内に3回連続でアクセプトされるまで。

# step1: 15分
## 方針を考える
pythonであれば両方をsetにしてから&でintersectionを取ればできる。
setにするのはO(n=m)
intersectionするのはどれくらいかかるんだろう。。

ただ、それを手で実装しろってことかな？
例えば最初にnums1, nums2両方の要素をsetに突っ込む
nums1をfor loopで回して、nums2に要素があるかどうか判定しあれば答えに格納するとか。

## 実装
setにしてintersectionしたコード
```python
class Solution:
    def intersection(self, nums1: List[int], nums2: List[int]) -> List[int]:
        unique_nums1 = set(nums1)
        unique_nums2 = set(nums2)
        return list(unique_nums1 & unique_nums2)
```

自分でintersectionしたコード
```python
class Solution:
    def intersection(self, nums1: List[int], nums2: List[int]) -> List[int]:
        unique_nums1 = set(nums1)
        unique_nums2 = set(nums2)
        result = []
        for num1 in unique_nums1:
            if num1 in unique_nums2:
                result.append(num1)
        return result
```

## （必要に応じて）最適化
特にない

## テストケースを考える
特にない
自分でテストケース作るなら、
- 片方に空を渡したとき
- 両方に空を渡したとき
- 片方の要素数が1のとき
- 両方の要素数が1のとき
- 片方に重複した値を含むとき
- 両方に重複した値を含むとき
かな？
用途によってはnums.length, numsの範囲を考えてもよいかもしれない。
ほぼないと思うがハッシュ可能じゃない要素がnums1, nums2に含まれるときとか？（これは考えすぎか）

# step2: 30分
## 時間の関係でstep1で書けなかった迷いや判断を書く
ない

## 自分でコード読みやすく整えて実装
step1のままで良いかな
結構納得いくコードなので他の人のPRを見て勉強する

## 他の人のPRやコメントを踏まえて実装
https://leetcode.com/problems/intersection-of-two-arrays/editorial/
leetcodeの解法を確認。
seenというsetを使うことで空間計算量をO(n)に下げている。
```python
class Solution:
    def intersection(self, nums1, nums2):

        # Initialize seen dictionary and res array
        seen = {}
        result = []

        # mark values occurring in nums1
        for x in nums1:
          seen[x] = 1
          
        for x in nums2:
          # Check if x is in the dictionary and not in the result
          if x in seen and seen[x] == 1:
            result.append(x)
            seen[x] = 0

        # Return the result
        return result
```

https://discord.com/channels/1084280443945353267/1303257587742933024/1320000574434971749

>そうですね。
>
>他、両方ソートされていてとても大きければ、マージソートの変形のように書くと思います。
>
>要するにこの問題の推定される出題意図は条件を変えたときに案がいくつか出てくるかです。
>
想定できてなかった。ソートアルゴリズムうろ覚えなので復習しておく。

https://github.com/olsen-blue/Arai60/pull/13/files
一個前の話に近いが、どれくらい想定出来ているかという話

https://github.com/Mike0121/LeetCode/pull/30/files#r1641596790
>merge sort を書くときの書き方を連想しました。
leetcodeに載っていたtwo-pointerについてのコメント。
やはりソートアルゴリズムちゃんと復習しておこう


# step3: 15分
※間違えがあればn回目を増やす

## 1回目

## 2回目

## 3回目