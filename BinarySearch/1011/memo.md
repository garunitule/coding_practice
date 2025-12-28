# 進め方
- step1: 5分考えて分からなかったら答えを見る。答えを理解したら、答えを隠して書く。筆が進まず5分立ったら答えを見る。答えを送信して正解するまで。
- step2: コードを読みやすく整える。動くコードになったら終了。
- step3: 時間を計りながら書く。10分以内に3回連続でアクセプトされるまで。

# step1: 15分
不変条件を整理して実装できた

```python
class Solution:
    def calcDays(self, weights: List[int], capacity) -> int:
        days = 1
        loaded_weight = 0
        for weight in weights:
            if loaded_weight + weight <= capacity:
                loaded_weight += weight
                continue
            days += 1
            loaded_weight = weight

        return days

    def shipWithinDays(self, weights: List[int], days: int) -> int:
        """
        不変条件
        - capacity < left_capacityの場合、daysより大きい
        - right < capacityの場合、days以下
        """
        # len(weights)分だけ日数がかかる
        left_capacity = max(weights)
        # 1日で運べる
        right_capacity = sum(weights)
        while left_capacity <= right_capacity:
            mid_capacity = (left_capacity + right_capacity) // 2
            if self.calcDays(weights, mid_capacity) <= days:
                right_capacity = mid_capacity - 1
            else:
                left_capacity = mid_capacity + 1
        
        return left_capacity
```


# step2: 30分
leetcodeの解法も発想は同じ。
feasibleというメソッドの方が分割したい責務に近いと思った。

weightsを足して、capacity > max_capacityがTrueになったらdaysを進めるとweightを加える操作が1回で済む。意味的にはほんの少し分かりづらい気がする。好みの範囲だと思うが。

https://github.com/h1rosaka/arai60/pull/46/files


# step3: 15分
※間違えがあればn回目を増やす

## 1回目
```python
class Solution:
    def can_ship_within_days(self, weights: List[int], days: int, capacity: int) -> bool:
        days_to_ship = 1
        loaded_weight = 0
        for weight in weights:
            if loaded_weight + weight <= capacity:
                loaded_weight += weight
                continue
            days_to_ship += 1
            loaded_weight = weight
        
        return days_to_ship <= days

    def shipWithinDays(self, weights: List[int], days: int) -> int:
        left_capacity = max(weights)
        right_capacity = sum(weights)
        while left_capacity <= right_capacity:
            mid_capacity = (left_capacity + right_capacity) // 2
            if self.can_ship_within_days(weights, days, mid_capacity):
                right_capacity = mid_capacity - 1
            else:
                left_capacity = mid_capacity + 1

        return left_capacity 

```

## 2回目
こちらの実装の方が分かりやすいと判断し、step1から変えた
rightの不変条件を「right <= capacityではdays以下で出荷できる」と捉えた。
left = rightの状態で終了し、不変条件からleft(right)が回答になる

```python
class Solution:
    def can_ship_within_days(self, weights: List[int], days: int, capacity: int) -> bool:
        days_to_ship = 1
        loaded_weight = 0
        for weight in weights:
            if loaded_weight + weight <= capacity:
                loaded_weight += weight
                continue
            days_to_ship += 1
            loaded_weight = weight

        return days_to_ship <= days

    def shipWithinDays(self, weights: List[int], days: int) -> int:
        left_capacity = max(weights)
        right_capacity = sum(weights) + 1
        while left_capacity < right_capacity:
            mid_capacity = (left_capacity + right_capacity) // 2
            if self.can_ship_within_days(weights, days, mid_capacity):
                right_capacity = mid_capacity
            else:
                left_capacity = mid_capacity + 1
        return left_capacity
```

## 3回目
```python
class Solution:
    def can_ship_within_days(self, weights: List[int], days: int, capacity: int) -> bool:
        days_to_ship = 1
        loaded_weight = 0
        for weight in weights:
            if loaded_weight + weight <= capacity:
                loaded_weight += weight
                continue
            days_to_ship += 1
            loaded_weight = weight
        
        return days_to_ship <= days

    def shipWithinDays(self, weights: List[int], days: int) -> int:
        left_capacity = max(weights)
        right_capacity = sum(weights) + 1
        while left_capacity < right_capacity:
            mid_capacity = (left_capacity + right_capacity) // 2
            if self.can_ship_within_days(weights, days, mid_capacity):
                right_capacity = mid_capacity
            else:
                left_capacity = mid_capacity + 1
        
        return left_capacity 
```