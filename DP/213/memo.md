# 進め方
- step1: 5分考えて分からなかったら答えを見る。答えを理解したら、答えを隠して書く。筆が進まず5分立ったら答えを見る。答えを送信して正解するまで。
- step2: コードを読みやすく整える。動くコードになったら終了。
- step3: 時間を計りながら書く。10分以内に3回連続でアクセプトされるまで。

# step1: 15分
## 方針を考える
House Robberのようにdp[i]を「家iまでの最大強盗額」と置くと、i = n - 1のときに最初の家を含めたかどうかが分からない
そこで、dp_with_headとdp_without_headを考え、max(dp_with_head[-1], dp_without_head[-1])の最大値を取る

もっとシンプルな方法があるかもしれないが、これでやってみる

max_robbed_first[i]: 最初の家を強盗したときの、家iまでの最大強盗額
- 初期値
  - i = 0: nums[0]
  - i = 1: nums[0]（二つ目の家を強盗はできないため）
- 遷移
  - i != n - 1: max(max_robbed_first[i - 1], max_robbed_first[i - 2] + nums[i])
  - i = n - 1: max_whenrobbed_first[i - 1]（nums[i]を選ぶと隣接してしまうため）

max_skipped_first[i]: 2番目の家を強盗したときの、家iまでの最大強盗額
- 初期値
  - i = 0: 0（2番目の家を強盗するとき、最初の家は強盗できないので）
  - i = 1: nums[1]
- 遷移
  - max(max_skipped_first[i - 1], max_skipped_first[i - 2] + nums[i])

両者を計算して下記を返す
max(max_robbed_first[-1], max_skipped_first[-1])

## 実装
愚直な実装だができた
最初に長さが1のときの早期returnを忘れてWAが出たが、2回目に通った
この実装も分割統治と言えるのか？最初の家を強盗したときと強盗しなかったときで分割し、最後にがっちゃんこして解いている
```python
class Solution:
    def rob(self, nums: List[int]) -> int:
        if not nums:
            return 0
        if len(nums) == 1:
            return nums[0]
        
        num_houses = len(nums)
        max_robbed_first = [0] * num_houses
        max_robbed_first[0] = nums[0]
        max_robbed_first[1] = nums[0]
        max_skipped_first = [0] * num_houses
        max_skipped_first[0] = 0
        max_skipped_first[1] = nums[1]
        for i in range(2, num_houses):
            max_skipped_first[i] = max(max_skipped_first[i - 1], max_skipped_first[i - 2] + nums[i])
            max_robbed_first[i] = max_robbed_first[i - 1]
            if i != num_houses - 1:
                max_robbed_first[i] = max(max_robbed_first[i], max_robbed_first[i - 2] + nums[i])
        
        return max(max_robbed_first[-1], max_skipped_first[-1])
```

## （必要に応じて）最適化
House Robberと同じように前2つだけ保持すれば良いのだが、変数が多くごちゃごちゃする気がしている
かといってもっとスッキリ解ける解法を思いついてるわけじゃないので、いったんstep2に進む

# step2: 30分
## 他の人のPRやコメントを踏まえて実装

# step3: 15分
※間違えがあればn回目を増やす

## 1回目

## 2回目

## 3回目