# 進め方
- step1: 5分考えて分からなかったら答えを見る。答えを理解したら、答えを隠して書く。筆が進まず5分立ったら答えを見る。答えを送信して正解するまで。
- step2: コードを読みやすく整える。動くコードになったら終了。
- step3: 時間を計りながら書く。10分以内に3回連続でアクセプトされるまで。

# step1: 15分
## 方針を考える
ルールを素直に実装すればよいと考えた。
なんかこういうのあった気がするが覚えていない。

## 実装
```python
class Solution:
    def myAtoi(self, s: str) -> int:
        # 先頭の空白をスキップ
        i = 0
        while i < len(s) and s[i] == " ":
            i += 1
        if i == len(s):
            return 0
        
        # 符号判定
        is_negative = False
        if s[i] == "-":
            is_negative = True
            i += 1
        elif s[i] == "+":
            i += 1
        if i == len(s):
            return 0
        
        # 先頭の0をスキップ
        while i < len(s) and s[i] == "0":
            i += 1
        if i == len(s):
            return 0
        
        # 数字を構築
        result = 0
        digits = {"1", "2", "3", "4", "5", "6", "7", "8", "9", "0"}
        while i < len(s) and s[i] in digits:
            if result == 0:
                result += int(s[i])
            else:
                result = 10 * result + int(s[i])
            i += 1
        
        # 符号付与および丸め処理
        if is_negative:
            result *= -1
            result = max(result, -2 ** 31)
            return result 
        result = min(result, 2 ** 31 - 1)
        return result

```

# step2: 30分
オートマトンでも実装できる。
q0: start
- q0, q1, q2, qdに遷移可能
q1: sign
- q2, qdに遷移可能
q2: digit
- q2, qdに遷移可能
qd: dead state
- qdに遷移可能

愚直に実装した。
```python
from enum import Enum, auto


class State(Enum):
    START = auto()
    SIGN = auto()
    DIGIT = auto()
    STOP = auto()

class StateMachine:
    def __init__(self):
        self.__current_state = State.START
        self.__sign = 1
        self.__current_value = 0
    
    def transition(self, c: str) -> None:
        if self.__current_state == State.START:
            if c == " ":
                return
            elif c == "-" or c == "+":
                self.to_sign(c)
            elif c.isdigit():
                self.to_digit(int(c))
            else:
                self.to_stop()
        elif self.__current_state == State.SIGN:
            if c.isdigit():
                self.to_digit(int(c))
            else:
                self.to_stop()
        elif self.__current_state == State.DIGIT:
            if c.isdigit():
                self.to_digit(int(c))
            else:
                self.to_stop()
    
    def to_sign(self, c: str) -> None:
        self.__current_state = State.SIGN
        if c == "-":
            self.__sign = -1
    
    def to_digit(self, i: int) -> None:
        self.__current_state = State.DIGIT
        if self.__current_value == 0:
            self.__current_value = i
            return
        self.__current_value = 10 * self.__current_value + i
    
    def to_stop(self) -> None:
        self.__current_state = State.STOP
    
    def get_integer(self) -> int:
        result = self.__sign * self.__current_value
        if result < 0:
            return max(result, -2 ** 31)
        return min(result, 2 ** 31 - 1)
    
    def is_stopped(self) -> bool:
        return self.__current_state == State.STOP

class Solution:
    def myAtoi(self, s: str) -> int:
        state_machine = StateMachine()
        for c in s:
            state_machine.transition(c)
            if state_machine.is_stopped():
                break
        return state_machine.get_integer()
```

# step3: 15分
※間違えがあればn回目を増やす

## 1回目
```python
from enum import Enum, auto

class State(Enum):
    START = auto()
    SIGN = auto()
    DIGIT = auto()
    STOP = auto()

class StateMachine:
    def __init__(self):
        self.__current_state = State.START
        self.__sign = 1
        self.__current_value = 0
    
    def transition(self, c: str) -> None:
        if self.__current_state == State.START:
            if c == " ":
                return
            elif c in ("-", "+"):
                self.to_sign(c)
            elif c.isdigit():
                self.to_digit(int(c))
            else:
                self.to_stop()
        elif self.__current_state == State.SIGN:
            if c.isdigit():
                self.to_digit(int(c))
            else:
                self.to_stop()
        elif self.__current_state == State.DIGIT:
            if c.isdigit():
                self.to_digit(int(c))
            else:
                self.to_stop()

    def to_sign(self, c: str) -> None:
        self.__current_state = State.SIGN
        if c == "-":
            self.__sign = -1

    def to_digit(self, i: int) -> None:
        self.__current_state = State.DIGIT
        if self.__current_value == 0:
            self.__current_value = i
        else:
            self.__current_value = 10 * self.__current_value + i

    def to_stop(self) -> None:
        self.__current_state = State.STOP

    def is_stopped(self) -> bool:
        return self.__current_state == State.STOP

    def get_integer(self) -> int:
        result = self.__sign * self.__current_value
        if result < 0:
            return max(result, -2 ** 31)
        return min(result, 2 ** 31 - 1)
 

class Solution:
    def myAtoi(self, s: str) -> int:
        state_machine = StateMachine()
        for c in s:
            state_machine.transition(c)
            if state_machine.is_stopped():
                break
        
        return state_machine.get_integer()
```

## 2回目
```python
from enum import Enum, auto

class State(Enum):
    START = auto()
    SIGN = auto()
    DIGIT = auto()
    STOP = auto()

class StateMachine:
    def __init__(self):
        self.__current_state = State.START
        self.__sign = 1
        self.__current_value = 0
    
    def transition(self, c: str):
        if self.__current_state == State.START:
            if c == " ":
                return
            elif c in ("-", "+"):
                self.to_sign(c)
            elif c.isdigit():
                self.to_digit(int(c))
            else:
                self.to_stop()
        elif self.__current_state == State.SIGN:
            if c.isdigit():
                self.to_digit(int(c))
            else:
                self.to_stop()
        elif self.__current_state == State.DIGIT:
            if c.isdigit():
                self.to_digit(int(c))
            else:
                self.to_stop()
    
    def to_sign(self, c: str) -> None:
        self.__current_state = State.SIGN
        if c == "-":
            self.__sign = -1
    
    def to_digit(self, i: int) -> None:
        self.__current_state = State.DIGIT
        if self.__current_value == 0:
            self.__current_value = i
        else:
            self.__current_value = 10 * self.__current_value + i
    
    def to_stop(self) -> None:
        self.__current_state = State.STOP

    def is_stopped(self) -> bool:
        return self.__current_state == State.STOP
    
    def get_integer(self) -> int:
        if self.__sign == -1:
            return max(self.__sign * self.__current_value, -2 ** 31)
        return min(self.__current_value, 2 ** 31 - 1)

class Solution:
    def myAtoi(self, s: str) -> int:
        state_machine = StateMachine()
        for c in s:
            state_machine.transition(c)
            if state_machine.is_stopped():
                break
        return state_machine.get_integer()
```

## 3回目
```python
from enum import Enum, auto

class State(Enum):
    START = auto()
    SIGN = auto()
    DIGIT = auto()
    STOP = auto()

class StateMachine:
    def __init__(self):
        self.__current_state = State.START
        self.__sign = 1
        self.__current_value = 0
    
    def transition(self, c: str) -> None:
        if self.__current_state == State.START:
            if c == " ":
                return
            elif c in ("-", "+"):
                self.to_sign(c)
            elif c.isdigit():
                self.to_digit(int(c))
            else:
                self.to_stop()
        elif self.__current_state == State.SIGN:
            if c.isdigit():
                self.to_digit(int(c))
            else:
                self.to_stop()
        elif self.__current_state == State.DIGIT:
            if c.isdigit():
                self.to_digit(int(c))
            else:
                self.to_stop()
    
    def to_sign(self, c: str) -> None:
        self.__current_state = State.SIGN
        if c == "-":
            self.__sign = -1
    
    def to_digit(self, i: int) -> None:
        self.__current_state = State.DIGIT
        if self.__current_value == 0:
            self.__current_value = i
        else:
            self.__current_value = 10 * self.__current_value + i
    
    def to_stop(self) -> None:
        self.__current_state = State.STOP

    def is_stopped(self) -> bool:
        return self.__current_state == State.STOP
    
    def get_integer(self) -> int:
        if self.__sign == -1:
            return max(-1 * self.__current_value, -2 ** 31)
        return min(self.__current_value, 2 ** 31 - 1)

class Solution:
    def myAtoi(self, s: str) -> int:
        state_machine = StateMachine()
        for c in s:
            state_machine.transition(c)
            if state_machine.is_stopped():
                break
        
        return state_machine.get_integer()
```