# 進め方
- step1: 5分考えて分からなかったら答えを見る。答えを理解したら、答えを隠して書く。筆が進まず5分立ったら答えを見る。答えを送信して正解するまで。
- step2: コードを読みやすく整える。動くコードになったら終了。
- step3: 時間を計りながら書く。10分以内に3回連続でアクセプトされるまで。

# step1: 15分
どこまでローテーションされたか特定し、どちらかの区間で2分探索すればよい。

もっときれいに書けるかもしれないが動くコードは書けた。

```python
class Solution:
    def search_min_index(self, nums: List[int]) -> int:
        left = -1
        right = len(nums) - 1
        while left + 1 < right:
            mid = (left + right) // 2
            if nums[-1] < nums[mid]:
                left = mid
            else:
                right = mid
        return right

    def search(self, nums: List[int], target: int) -> int:
        if not nums:
            return []

        min_index = self.search_min_index(nums)
        left = 0
        right = len(nums) - 1
        if target <= nums[-1]:
            left = min_index
        else:
            right = min_index - 1
        
        while left <= right:
            mid = (left + right) // 2
            if nums[mid] == target:
                return mid
            if nums[mid] < target:
                left = mid + 1
            else:
                right = mid - 1

        return -1
        
```

search_min_indexの不変条件
- left: nums[-1]より大きい中で最大のインデックス
- right: nums[-1]より小さい中で最小のインデックス

searchの不変条件
- left: targetとなりうる最小のインデックス
- right: targetとなりうる最大のインデックス

# step2: 30分
https://github.com/skypenguins/coding-practice/pull/29/files
1つのループの中でやる方法。少し複雑に思える。

https://github.com/skypenguins/coding-practice/pull/29/files#r2558426858
1つのループの中で実装する方法の場合でも、この実装はわかりやすい。

下記の実装でも動くが、不変条件が下記のようになるため、nums[i] == targetを満たすiが複数存在する場合にどのインデックスを返すか分からない。
- left: i < leftはtargetが所属してる区間じゃない、または、nums[i] < target
- right: right < iはtargetが所属してる区間じゃない、または、target < nums[i]

```python
class Solution:
    def search(self, nums: List[int], target: int) -> int:
        left = 0
        right = len(nums) - 1

        while left <= right:
            mid = (left + right) // 2
            # midが左側の区間内かつtargetが右側にあるなら、mid以前は捨てていい
            if nums[-1] < nums[mid] and target <= nums[-1]:
                left = mid + 1
                continue
            # midが右側の区間内かつtargetが左側にあるなら、mid以降は捨てていい
            if nums[mid] <= nums[-1] and nums[-1] < target:
                right = mid - 1
                continue
            
            if nums[mid] == target:
                return mid
            elif nums[mid] < target:
                left = mid + 1
            else:
                right = mid - 1
        
        return -1
```

下記の実装にすると不変条件が下記のようになるため、nums[i] == targetを満たすiが複数存在する場合してもその中で最小のインデックスを返すようになる。ただ、両方の区間にnums[-1]と同じ値が存在すると、そもそも区間判定処理を変えなきゃいけない。が、これ以上は考えないことにする。
- left: i < leftはtargetが所属してる区間じゃない、または、nums[i] < target
- right: right < iはtargetが所属してる区間じゃない、または、target <= nums[i]

```python
class Solution:
    def search(self, nums: List[int], target: int) -> int:
        left = 0
        right = len(nums) - 1

        while left <= right:
            mid = (left + right) // 2
            # midが左側の区間内かつtargetが右側にあるなら、mid以前は捨てていい
            if nums[-1] < nums[mid] and target <= nums[-1]:
                left = mid + 1
                continue
            # midが右側の区間内かつtargetが左側にあるなら、mid以降は捨てていい
            if nums[mid] <= nums[-1] and nums[-1] < target:
                right = mid - 1
                continue
            
            if nums[mid] < target:
                left = mid + 1
            else:
                right = mid - 1

        if left < len(nums) and nums[left] == target:
            return left
        return -1
             
```

# step3: 15分
※間違えがあればn回目を増やす

## 1回目
```python
class Solution:
    def search(self, nums: List[int], target: int) -> int:
        left = 0
        right = len(nums) - 1
        while left <= right:
            mid = (left + right) // 2
            if nums[-1] < nums[mid] and target <= nums[-1]:
                left = mid + 1
                continue
            if nums[mid] <= nums[-1] and nums[-1] < target:
                right = mid - 1
                continue
            
            if nums[mid] < target:
                left = mid + 1
            else:
                right = mid - 1
        
        if left < len(nums) and nums[left] == target:
            return left
        return -1
```

## 2回目
```python
class Solution:
    def search(self, nums: List[int], target: int) -> int:
        left = 0
        right = len(nums) - 1
        while left <= right:
            mid = (left + right) // 2
            if nums[-1] < nums[mid] and target <= nums[-1]:
                left = mid + 1
                continue
            if nums[mid] <= nums[-1] and nums[-1] < target:
                right = mid - 1
                continue
            
            if nums[mid] < target:
                left = mid + 1
            else:
                right = mid - 1
        
        if left < len(nums) and nums[left] == target:
            return left
        return -1
```

## 3回目
```python
class Solution:
    def search(self, nums: List[int], target: int) -> int:
        left = 0
        right = len(nums) - 1
        while left <= right:
            mid = (left + right) // 2
            if nums[-1] < nums[mid] and target <= nums[-1]:
                left = mid + 1
                continue
            if nums[mid] <= nums[-1] and nums[-1] < target:
                right = mid - 1
                continue
            
            if nums[mid] < target:
                left = mid + 1
            else:
                right = mid - 1
        
        if left < len(nums) and nums[left] == target:
            return left
        return -1
```