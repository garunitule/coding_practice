# 進め方
- step1: 5分考えて分からなかったら答えを見る。答えを理解したら、答えを隠して書く。筆が進まず5分立ったら答えを見る。答えを送信して正解するまで。
- step2: コードを読みやすく整える。動くコードになったら終了。
- step3: 時間を計りながら書く。10分以内に3回連続でアクセプトされるまで。

# step1: 15分
nums[left] > nums[right]が成り立つ一番狭い区間を探せばよさそう。

初期値
- left: 0
- right: len(nums) - 1
終了条件
- right - left <= 1になったら、rightを返す。継続条件はright - left > 1
不変条件
- leftは元の位置から左に移動した中で最大となりうるインデックス
- rightは元の位置から右に移動した中で最小となりうるインデックス
更新
- nums[left] <= nums[mid]の場合、left = mid
- nums[left] > nums[mid]の場合、right = mid 

もしnums[0] <= nums[-1]ならローテーション前と変わらないので、nums[0]を返す

最初失敗したが、nums[right]を返すようにしたら通った。返すのをインデックスと値で間違っていた。
```python
class Solution:
    def findMin(self, nums: List[int]) -> int:
        if nums[0] <= nums[-1]:
            return nums[0]
        
        left = 0
        right = len(nums) - 1
        while left + 1 < right:
            mid = (left + right) // 2
            if nums[left] <= nums[mid]:
                left = mid
            else:
                right= mid
        
        return nums[right]
```

# step2: 30分
https://discord.com/channels/1084280443945353267/1245404801177616394/1308622011601518623
>「2で割る処理がありますがこれは切り捨てでも切り上げでも構わないのでしょうか。」
「nums[middle] <= nums[right] とありますが、これは < でもいいですか。」
「nums[right] は、nums[nums.length - 1] でもいいですか。」
「right の初期値は nums.length でもいいですか。」

変数の役割や不変条件、終了条件を考えていれば答えられる。

https://github.com/yas-2023/leetcode_arai60/pull/22/files#r2600002883
>nums[0] <= nums[i] な領域と nums[0] > nums[i] な領域の境界を探せ
nums[-1] < nums[i] な領域と nums[-1] >= nums[i] な領域の境界を探せ

たしかに比較要素は固定した方が確かに分かりやすいな。
この場合は、
left: nums[0]以上の最大となりうるインデックス
right: nums[0]以下となる最小となりうるインデックス

```python
class Solution:
    def findMin(self, nums: List[int]) -> int:
        if nums[0] <= nums[-1]:
            return nums[0]
        
        left = 0
        right = len(nums) - 1
        while left + 1 < right:
            mid = (left + right) // 2
            if nums[0] <= nums[mid]:
                left = mid
            else:
                right = mid

        return nums[right]
```



# step3: 15分
※間違えがあればn回目を増やす

## 1回目
```python
class Solution:
    def findMin(self, nums: List[int]) -> int:
        if nums[0] <= nums[-1]:
            return nums[0]
        
        left = 0
        right = len(nums) - 1
        while left + 1 < right:
            mid = (left + right) // 2
            if nums[0] <= nums[mid]:
                left = mid
            else:
                right = mid

        return nums[right]
```

## 2回目
```python
class Solution:
    def findMin(self, nums: List[int]) -> int:
        if nums[0] <= nums[-1]:
            return nums[0]
        
        left = 0
        right = len(nums) - 1
        while left + 1 < right:
            mid = (left + right) // 2
            if nums[0] <= nums[mid]:
                left = mid
            else:
                right = mid

        return nums[right]
```

## 3回目
```python
class Solution:
    def findMin(self, nums: List[int]) -> int:
        if nums[0] <= nums[-1]:
            return nums[0]
        
        left = 0
        right = len(nums) - 1
        while left + 1 < right:
            mid = (left + right) // 2
            if nums[0] <= nums[mid]:
                left = mid
            else:
                right = mid
        
        return nums[right]
```