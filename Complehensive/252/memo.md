# 進め方
- step1: 5分考えて分からなかったら答えを見る。答えを理解したら、答えを隠して書く。筆が進まず5分立ったら答えを見る。答えを送信して正解するまで。
- step2: コードを読みやすく整える。動くコードになったら終了。
- step3: 時間を計りながら書く。10分以内に3回連続でアクセプトされるまで。

# step1: 15分
## 方針を考える
startでソートして、i番目のアイテムのendとi+1番目のアイテムのstartについて、end <= startが成立するか確認すれば解けそう。

O(n)で解ける方法がないか検討したけど思いつかなかった。

## 実装
```python
class Solution:
    def canAttendMeetings(self, intervals: List[List[int]]) -> bool:
        intervals_sorted_by_start = sorted(intervals, key=lambda interval: interval[0])
        for i in range(len(intervals_sorted_by_start) - 1):
            if intervals_sorted_by_start[i][1] > intervals_sorted_by_start[i + 1][0]:
                return False
        return True
```

# step2: 30分
leetcodeの解法を見たら、全組み合わせを確認する解法とソートして確認する解法だった。
Pythonだと空間計算量はO(n)だけど、JavaだとO(logn)とのこと。実装アルゴリズムが異なるらしい。


# step3: 15分
※間違えがあればn回目を増やす

## 1回目
```python
class Solution:
    def canAttendMeetings(self, intervals: List[List[int]]) -> bool:
        intervals_sorted_by_start = sorted(intervals, key=lambda interval: interval[0])
        for i in range(len(intervals_sorted_by_start) - 1):
            if intervals_sorted_by_start[i][1] > intervals_sorted_by_start[i + 1][0]:
                return False
        return True
```

## 2回目
```python
class Solution:
    def canAttendMeetings(self, intervals: List[List[int]]) -> bool:
        intervals_sorted_by_started = sorted(intervals, key=lambda interval: interval[0])
        for i in range(len(intervals_sorted_by_started) - 1):
            if intervals_sorted_by_started[i][1] > intervals_sorted_by_started[i + 1][0]:
                return False
        return True
```

## 3回目
```python
class Solution:
    def canAttendMeetings(self, intervals: List[List[int]]) -> bool:
        intervals_sorted_by_start = sorted(intervals, key=lambda interval: interval[0])
        for i in range(len(intervals_sorted_by_start) - 1):
            if intervals_sorted_by_start[i][1] > intervals_sorted_by_start[i + 1][0]:
                return False
        return True
```