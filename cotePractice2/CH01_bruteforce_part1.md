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

### 두 스티커
```
import sys

def isFit(a, b):
    global ans
    ax, ay, aSize = a[0], a[1], a[2]
    bx, by, bSize = b[0], b[1], b[2]
    if (ax + bx <= H and max(ay, by) <= W) or (ax + by <= H and max(ay, bx) <= W) or (ay + bx <= H and max(ax, by) <= W) or (ay + by <= H and max(ax, bx) <= W):
        ans = max(ans, aSize + bSize)
        return True
    if (ax + bx <= W and max(ay, by) <= H) or (ax + by <= W and max(ay, bx) <= H) or (ay + bx <= W and max(ax, by) <= H) or (ay + by <= W and max(ax, bx) <= H):
        ans = max(ans, aSize + bSize)
        return True
    return False

H, W = map(int, sys.stdin.readline().rstrip().split())
N = int(sys.stdin.readline().rstrip())
stickers = []
for _ in range(N):
    x, y = map(int, sys.stdin.readline().rstrip().split())
    stickers.append([x, y, x * y])

stickers.sort(key=lambda x:x[2], reverse=True)
ans = 0

for i in range(len(stickers) - 1):
    for j in range(i + 1, len(stickers)):
        isFit(stickers[i], stickers[j])
print(ans)
```

### 캠프 준비
```
import sys
from itertools import combinations

N, L, R, X = map(int, sys.stdin.readline().rstrip().split())
numList = list(map(int, sys.stdin.readline().rstrip().split()))
numList.sort()

ans = 0

for i in range(len(numList) - 1):
    for j in range(i + 1, len(numList)):
        if numList[j] - numList[i] >= X:
            if numList[j] + numList[i] >= L and numList[j] + numList[i] <= R:
                ans += 1
            additionalNums = numList[i + 1:j]
            for k in range(1, len(additionalNums) + 1):
                candidates = list(combinations(additionalNums, k))
                for elem in candidates:
                    numSum = sum(elem) + numList[i] + numList[j]
                    if numSum >= L and numSum <= R:
                        ans += 1
print(ans)
```

### 캠프 준비(모범 답안)
```
def backtracking(idx, total, easy, hard):
    global cnt
    if idx == n:
        if l <= total <= r and hard-easy >= x:
            cnt += 1
        return
    
    # 현재 문제를 추가하지 않을때
    backtracking(idx+1, total, easy, hard)
    
    if not total:  # 현재 문제가 가장 쉬울때
        backtracking(idx+1, A[idx], A[idx], A[idx])
    else:
        backtracking(idx+1, total+A[idx], easy, A[idx])
    
    

n, l, r, x = map(int, input().split())
A = sorted(map(int, input().split()))
cnt = 0
backtracking(0, 0, -1, -1)
print(cnt)
```
-> idx는 현재 추가할지 말지 결정하는 인덱스인것 같다. 각 경우를 함수로 나타내는 풀이이다.

### 숫자 재배치
```
import sys
import copy

def backtracking(nums, tempStr):
    global ans
    if len(nums) == 0:
        if int(tempStr) < B:
            ans = max(ans, int(tempStr))
    else:
        for num in nums:
            if tempStr == '' and num == 0:
                continue
            newStr = tempStr + str(num)
            newNums = copy.deepcopy(nums)
            newNums.remove(num)
            backtracking(newNums, newStr)

A, B = map(int, sys.stdin.readline().rstrip().split())
aStr = str(A)
aList = []
for char in aStr:
    aList.append(int(char))
aList.sort(reverse=True)

ans = -1
backtracking(aList, '')
print(ans)
```
-> 다른 성능이 좋은 코드에서는 비트마스킹 기법을 활용한 것 같은데, 비트마스킹에 대해서 공부해봐야겠다.
