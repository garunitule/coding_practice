# 進め方
- step1: 5分考えて分からなかったら答えを見る。答えを理解したら、答えを隠して書く。筆が進まず5分立ったら答えを見る。答えを送信して正解するまで。
- step2: コードを読みやすく整える。動くコードになったら終了。
- step3: 時間を計りながら書く。10分以内に3回連続でアクセプトされるまで。

# step1: 15分
## 方針を考える
時間の重複が内容にグループ分けをしてグループ数を返せばよいと思った。
ただ、所属グループの判定に今まで走査した要素分時間がかかりそう。その場合O(n!)になってしまい遅すぎる。n <= 10 ** 4なので。
グループ判定をもっと楽にしたいけど思いつかない。

ある時間でのMTGの重複の最大値を返せばよいのか？
0からendまで1刻みで時刻を進めて、各時刻で各intervalが含むかどうかを判定する。
start, end <= 10 ** 4, n <= 10 ** 4なので10 ** 8回の計算になる。
秒間1000万ステップとすると10秒程度かかるのでTLEするかもしれない。

動かないコード。このコードだと、[[13, 15], [1, 13]]のようなケースに対応できない。
```python
class Solution:
    def minMeetingRooms(self, intervals: List[List[int]]) -> int:
        max_time = 0
        for interval in intervals:
            max_time = max(max_time, interval[1])
        
        min_meeting_rooms = 0
        for t in range(max_time + 1):
            meeting_rooms = 0
            for interval in intervals:
                if interval[0] <= t and t <= interval[1]:
                    meeting_rooms += 1
            min_meeting_rooms = max(min_meeting_rooms, meeting_rooms)
        
        return min_meeting_rooms
```

各時刻で必要な部屋数を求める場合も、グループ分けで考えた場合と同じようにその時刻で開催したアイテムとの被りを考える必要がありそう。

# step2: 30分
LeetCodeの解法とは違うが、heapを使った実装。
各MTGを開始する際に必要な部屋数を求め、その最大値を返すという実装。
各ループでは実施中のMTGを保持している。

```python
import heapq


class Solution:
    def minMeetingRooms(self, intervals: List[List[int]]) -> int:
        intervals_sorted_by_start = sorted(intervals, key=lambda interval: interval[0])
        ongoing_meeting_end_times = []
        min_meeting_rooms = 0
        for start, end in intervals_sorted_by_start:
            while ongoing_meeting_end_times and ongoing_meeting_end_times[0] <= start:
                heapq.heappop(ongoing_meeting_end_times)
            heapq.heappush(ongoing_meeting_end_times, end)
            min_meeting_rooms = max(min_meeting_rooms, len(ongoing_meeting_end_times))
        
        return min_meeting_rooms
```

leetcodeだとあるMTGまででの最大数を保持するように実装している。
```python
import heapq


class Solution:
    def minMeetingRooms(self, intervals: List[List[int]]) -> int:
        intervals_sorted_by_start = sorted(intervals, key=lambda interval: interval[0])
        occupied_room_end_times = [intervals_sorted_by_start[0][1]]
        for start, end in intervals_sorted_by_start[1:]:
            if occupied_room_end_times[0] <= start:
                heapq.heappop(occupied_room_end_times)
            heapq.heappush(occupied_room_end_times, end)
        
        return len(occupied_room_end_times)
```

MTGが終わっているかどうかが分かればよいので、startとendを個別で扱うアプローチ。なるほど。
ある時点のMTGのstartより大きい最小のendを保持しているという点ではヒープと一緒か。
```python
class Solution:
    def minMeetingRooms(self, intervals: List[List[int]]) -> int:
        starts = [start for start, _ in intervals]
        starts.sort()
        ends = [end for _, end in intervals]
        ends.sort()

        end_index = 0
        min_meeting_rooms = 0
        for start in starts:
            if ends[end_index] <= start:
                end_index += 1
                continue
            min_meeting_rooms += 1
        
        return min_meeting_rooms

```

# step3: 15分
※間違えがあればn回目を増やす

## 1回目
```python
import heapq

class Solution:
    def minMeetingRooms(self, intervals: List[List[int]]) -> int:
        intervals_sorted_by_start = sorted(intervals, key=lambda interval: interval[0])
        occupied_room_end_times = [intervals_sorted_by_start[0][1]]
        for start, end in intervals_sorted_by_start[1:]:
            if occupied_room_end_times[0] <= start:
                heapq.heappop(occupied_room_end_times)
            heapq.heappush(occupied_room_end_times, end)
        
        return len(occupied_room_end_times)
```

## 2回目
```python
import heapq


class Solution:
    def minMeetingRooms(self, intervals: List[List[int]]) -> int:
        intervals_sorted_by_start = sorted(intervals, key=lambda interval: interval[0])
        occupied_room_end_times = [intervals_sorted_by_start[0][1]]
        for start, end in intervals_sorted_by_start[1:]:
            if occupied_room_end_times[0] <= start:
                heapq.heappop(occupied_room_end_times)
            heapq.heappush(occupied_room_end_times, end)
        
        return len(occupied_room_end_times)
```

## 3回目
```python
import heapq


class Solution:
    def minMeetingRooms(self, intervals: List[List[int]]) -> int:
        intervals_sorted_by_start = sorted(intervals, key=lambda interval: interval[0])
        occupied_room_end_times = [intervals_sorted_by_start[0][1]]
        for start, end in intervals_sorted_by_start[1:]:
            if occupied_room_end_times[0] <= start:
                heapq.heappop(occupied_room_end_times)
            heapq.heappush(occupied_room_end_times, end)
        
        return len(occupied_room_end_times)
```