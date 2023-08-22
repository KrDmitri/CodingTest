# 브루트포스 Part1
### 차량 번호판 1
```
import sys

def countNum(format, d, c, prev, cnt):
    if format == '':
        print(cnt)
    else:
        now = format[0]
        format = format[1:]
        if prev == now and now == 'c':
            cnt *= (c - 1)
        elif prev == now and now == 'd':
            cnt *= (d - 1)
        elif prev != now and now == 'c':
            cnt *= cNum
        elif prev != now and now == 'd':
            cnt *= dNum
        prev = now
        countNum(format, d, c, prev, cnt)

T = sys.stdin.readline().rstrip()

dNum = 10
cNum = 26
if T[0] == 'c':
    prev = 'd'
else:
    prev = 'c'

if T == '':
    print(0)
else:
    countNum(T, dNum, cNum, prev, 1)
```
