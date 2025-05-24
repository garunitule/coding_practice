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

## 想定質問の例
https://github.com/katataku/leetcode/pull/12#discussion_r1893968021

以下、計算量を見積もる時、
m: nums1の長さ
n: nums2の長さ
とする

1. 片方がとても大きくて片方がとても小さい場合
この場合小さい方をSetに変s換して、大きい方をloopしてSetに存在するかどうか確認すればよい。
こうすると、時間計算量はO(m+n), 空間計算量はO(min(m, n))になる

```python
class Solution:
    def intersection(self, nums1: List[int], nums2: List[int]) -> List[int]:
        if len(nums1) < len(nums2):
            short_nums = nums1
            long_nums = nums2
        else:
            short_nums = nums2
            long_nums = nums1
        
        short_nums_set = set(short_nums)
        result = []
        for num in long_nums:
            if num in short_nums_set:
                result.append(num)
                short_nums_set.discard(num)
        
        return result

```

2. 片方がとても大きくてかつソート済み、もう片方がとても小さい場合

この場合は小さい方をloopし、二分探索で大きい方に存在するかどうか確認すればいい。
時間計算量はO(min(m, n) * log(max(m, n)))になり、空間計算量はO(1)（計算のために確保する変数はleft, rightくらいのため）

```python
class Solution:
    def intersection(self, nums1: List[int], nums2: List[int]) -> List[int]:
        # 大きい方がソート済みという状況を準備する
        if len(nums1) < len(nums2):
            short_nums = nums1
            long_nums = nums2
        else:
            short_nums = nums2
            long_nums = nums1
        long_nums = sorted(long_nums)
        result = []
        for num in short_nums:
            left = 0
            right = len(long_nums) - 1
            while left <= right:
                mid = (left + right) // 2
                if long_nums[mid] == num:
                    result.append(num)
                    break
                elif long_nums[mid] < num:
                    left = mid + 1
                else:
                    right = mid - 1
        return list(set(result))
```

最後にsetでユニークにしている部分が直感的じゃないので、resultにnumが存在したらスキップするように修正した。resultがListのままだと存在チェックでO(n)かかるのでresultをSetに変更した.

```python
class Solution:
    def intersection(self, nums1: List[int], nums2: List[int]) -> List[int]:
        # 大きい方がソート済みという状況を準備する
        if len(nums1) < len(nums2):
            short_nums = nums1
            long_nums = nums2
        else:
            short_nums = nums2
            long_nums = nums1
        long_nums = sorted(long_nums)
        result = set()
        for num in short_nums:
            if num in result:
                continue 
            left = 0
            right = len(long_nums) - 1
            while left <= right:
                mid = (left + right) // 2
                if long_nums[mid] == num:
                    result.add(num)
                    break
                elif long_nums[mid] < num:
                    left = mid + 1
                else:
                    right = mid - 1
        return list(result)
```


3. 両方ともとても大きくてソート済みの場合
マージソートのマージ処理のように書く
i1, i2いずれかが末尾の次を指すインデックスになった時点で止めていい
例えば、i1 == len(nums)になったが、i2 < len(nums2)だったとする
このときこれ以上resultに追加する候補はnums2には存在しない
理由は残りのnums2の要素ははnums1のどの要素よりも大きいため
これはnums1とnums2を逆にしても成立する
以上のことから`i1 < len(nums1) and i2 < len(nums2)`の間だけ処理を続ければよい

```python
class Solution:
    def intersection(self, nums1: List[int], nums2: List[int]) -> List[int]:
        # 両方がソート済みという状況を作る
        nums1 = sorted(nums1)
        nums2 = sorted(nums2)

        # マージソートのマージ処理のように書く
        i1 = 0
        i2 = 0
        result = []
        while i1 < len(nums1) and i2 < len(nums2):
            if nums1[i1] < nums2[i2]:
                i1 += 1
                continue
            if nums2[i2] < nums1[i1]:
                i2 += 1
                continue
            result.append(nums1[i1])
            val = nums1[i1]
            while i1 < len(nums1) and nums1[i1] == val:
                i1 += 1
            while i2 < len(nums2) and nums2[i2] == val:
                i2 += 1
        return result
```


# step3: 15分
※間違えがあればn回目を増やす
小さい方のみをSetにして空間計算量はO(min(m, n))に落とす方法で実装する

## 1回目
```python
class Solution:
    def intersection(self, nums1: List[int], nums2: List[int]) -> List[int]:
        if len(nums1) < len(nums2):
            short_nums = nums1
            long_nums = nums2
        else:
            short_nums = nums2
            long_nums = nums1
        
        unique_short_nums = set(short_nums)
        seen = set()
        result = []
        for num in long_nums:
            if num in seen:
                continue
            if num in unique_short_nums:
                result.append(num)
                seen.add(num)
        return result
```

## 2回目
```python
class Solution:
    def intersection(self, nums1: List[int], nums2: List[int]) -> List[int]:
        if len(nums1) < len(nums2):
            short_nums = nums1
            long_nums = nums2
        else:
            short_nums = nums2
            long_nums = nums1
        short_nums_set = set(short_nums)
        seen = set()
        result = []
        for num in long_nums:
            if num in seen:
                continue
            if num in short_nums_set:
                result.append(num)
                seen.add(num)
        return result
```

## 3回目
```python
class Solution:
    def intersection(self, nums1: List[int], nums2: List[int]) -> List[int]:
        if len(nums1) < len(nums2):
            short_nums = nums1
            long_nums = nums2
        else:
            short_nums = nums2
            long_nums = nums1
        short_nums_set = set(short_nums)
        result = []
        seen = set()
        for num in long_nums:
            if num in seen:
                continue
            if num in short_nums_set:
                result.append(num)
                seen.add(num)
        return result
```