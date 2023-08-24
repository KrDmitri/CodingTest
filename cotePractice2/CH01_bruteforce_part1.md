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

### 양념 반 후라이드 반
```
import sys
# 양치 가격, 후치 가격, 반치 가격, 양x, 후y
A, B, C, X, Y = map(int, sys.stdin.readline().rstrip().split())

if A + B <= 2 * C:
    print(X * A + Y * B)
    exit(0)
else:
    ans = 0
    minC = min(X, Y)
    ans += (minC * 2) * C
    X -= minC
    Y -= minC
    # 후치 더 사야함
    if X == 0:
        if B > 2 * C:
            ans += (Y * 2) * C
        else:
            ans += Y * B
    # 양치 더 사야함
    else:
        if A > 2 * C:
            ans += (X * 2) * C
        else:
            ans += X * A

print(ans)
```

### 로마 숫자 만들기
```
import sys

def makeNumber(num, tempSum, depth):
    global ans
    if num == 0:
        if not visited[depth + 1][tempSum]:
            ans += 1
            visited[depth + 1][tempSum] = True
        return
    else:
        if not visited[depth + 1][tempSum + 1]:
            visited[depth + 1][tempSum + 1] = True
            makeNumber(num - 1, tempSum + 1, depth + 1)
        if not visited[depth + 1][tempSum + 5]:
            visited[depth + 1][tempSum + 5] = True
            makeNumber(num - 1, tempSum + 5, depth + 1)
        if not visited[depth + 1][tempSum + 10]:
            visited[depth + 1][tempSum + 10] = True
            makeNumber(num - 1, tempSum + 10, depth + 1)
        if not visited[depth + 1][tempSum + 50]:
            visited[depth + 1][tempSum + 50] = True
            makeNumber(num - 1, tempSum + 50, depth + 1)


N = int(sys.stdin.readline().rstrip())
visited = [[False] * 1001 for _ in range(N + 1)]
ans = 0

makeNumber(N, 0, -1)

print(ans)
```

### 십자가 찾기
```
import sys

def putCross(x, y):
    size = 1
    while True:
        for i in range(4):
            nx = x + (size * dx[i])
            ny = y + (size * dy[i])
            if nx < 0 or nx >= N or ny < 0 or ny >= M or graph[nx][ny] == '.':
                return
        visited[x][y] = True
        for i in range(4):
            nx = x + (size * dx[i])
            ny = y + (size * dy[i])
            visited[nx][ny] = True
        crossInfo.append([x + 1, y + 1, size])
        size += 1

N, M = map(int, sys.stdin.readline().rstrip().split())
visited = [[True] * M for _ in range(N)]
graph = []
for i in range(N):
    tempList = list(sys.stdin.readline().rstrip())
    for j in range(M):
        if tempList[j] == '*':
            visited[i][j] = False
    graph.append(tempList)

crossInfo = []
dx = [1, 0, -1, 0]
dy = [0, 1, 0, -1]

for i in range(N):
    for j in range(M):
        if graph[i][j] == '*':
            putCross(i, j)

# 만들 수 없는 경우
for i in range(N):
    for j in range(M):
        if not visited[i][j]:
            print(-1)
            exit(0)

# 만들 수 있는 경우
print(len(crossInfo))
for elem in crossInfo:
    print(elem[0], elem[1], elem[2])
```

### 나3곱2
```
import sys
import copy

def checkOrder(now, nList, history):
    if len(history) == N:
        for elem in history:
            print(elem, end=' ')
        exit(0)

    flag = False
    if now % 3 == 0 and (now // 3) in nList:
        flag = True
        newHistory = copy.deepcopy(history)
        newHistory.append(now // 3)
        newNList = copy.deepcopy(nList)
        newNList.remove(now // 3)
        checkOrder(now // 3, newNList, newHistory)
    if now * 2 in nList:
        flag = True
        newHistory = copy.deepcopy(history)
        newHistory.append(now * 2)
        newNList = copy.deepcopy(nList)
        newNList.remove(now * 2)
        checkOrder(now * 2, newNList, newHistory)
    if not flag:
        return

N = int(sys.stdin.readline().rstrip())
numList = list(map(int, sys.stdin.readline().rstrip().split()))

for i in range(N):
    nList = copy.deepcopy(numList)
    nList.remove(numList[i])
    checkOrder(numList[i], nList, [numList[i]])
```
