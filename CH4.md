## Chapter 04 구현
### 시각 문제
```
def time():
    n = int(input())
    cnt = 0

    # 시간 스캔 반복문
    for i in range(n+1):
        # 시간에 3 포함 경우
        if i % 10 == 3:
            cnt += 3600
        else:
            # 분 스캔 반복문
            for j in range(60):
                if (j // 10) == 3 or (j % 10) == 3:
                    cnt += 60
                else:
                    cnt += 15
    print(cnt)

def time2():
    n = int(input())
    cnt = 00

    for i in range(n + 1):
        for j in range(60):
            for k in range(60):
                if '3' in str(i) + str(j) + str(k):
                    cnt += 1
    print(cnt)
```

### 왕실의 기사 문제
```
def knight():
    pos = input()
    xPos = ord(pos[0]) - ord('a') + 1
    yPos = int(pos[1])
    cnt = 0

    if xPos - 2 >= 1:
        if yPos + 1 <= 8:
            cnt += 1
        if yPos -1 >= 1:
            cnt += 1
    if xPos + 2 <= 8:
        if yPos + 1 <= 8:
            cnt += 1
        if yPos - 1 >= 1:
            cnt += 1
    if yPos - 2 >= 1:
        if xPos + 1<= 8:
            cnt += 1
        if xPos -1 >= 1:
            cnt += 1
    if yPos + 2 <= 8:
        if xPos + 1 <= 8:
            cnt += 1
        if xPos - 1 >= 1:
            cnt += 1
    print(cnt)
```
