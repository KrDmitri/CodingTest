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

### 괄호 추가하기
```
import sys
INF = int(1e10)

def calc(a, b, sign):
    if sign == '+':
        return a + b
    elif sign == '-':
        return a - b
    else:
        return a * b

def operate(isBracket):
    # 괄호 연산
    newNums = []
    newOperators = []
    i = 0
    while i < len(isBracket):
        if not isBracket[i]:
            newNums.append(nums[i])
            newOperators.append(operators[i])
            i += 1
        else:
            newNums.append(calc(nums[i], nums[i + 1], operators[i]))
            if i + 1 < len(operators):
                newOperators.append(operators[i + 1])
            i += 2
    newNums.append(nums[-1])

    # 남은 수 연산
    val = newNums[0]
    for i in range(len(newOperators)):
        val = calc(val, newNums[i + 1], newOperators[i])
    return val

def dfs(isBracket, idx):
    global ans
    if idx == len(isBracket):
        ans = max(ans, operate(isBracket))
        return

    if idx == 0 or not isBracket[idx - 1]:
        dfs(isBracket, idx + 1)
        isBracket[idx] = True
        dfs(isBracket, idx + 1)
        isBracket[idx] = False
    else:
        dfs(isBracket, idx + 1)

N = int(sys.stdin.readline().rstrip())
equation = sys.stdin.readline().rstrip()
nums = []
operators = []
for i in range(len(equation)):
    if i % 2 == 0:
        nums.append(int(equation[i]))
    else:
        operators.append(equation[i])

brackets = [False] * len(operators)

ans = (-1) * INF
dfs(brackets, 0)
print(ans)
```

### 감시
```
import sys
INF = int(1e9)

def countBlindZones(directions):
    visited = [[False] * M for _ in range(N)]
    for i in range(len(cameras)):
        x, y = cameras[i]
        visited[x][y] = True
        camType = graph[x][y]
        direction = directions[i]
        if camType == 2:
            for _ in range(2):
                nx, ny = x, y
                while True:
                    nx += dx[direction]
                    ny += dy[direction]
                    if nx < 0 or nx >= N or ny < 0 or ny >= M or graph[nx][ny] == 6:
                        break
                    visited[nx][ny] = True
                direction += 2
        else:
            for j in range(camLookNum[camType]):
                nx, ny = x, y
                while True:
                    nx += dx[direction]
                    ny += dy[direction]
                    if nx < 0 or nx >= N or ny < 0 or ny >= M or graph[nx][ny] == 6:
                        break
                    visited[nx][ny] = True
                direction += 1
                direction %= 4
    blinds = 0
    for i in range(N):
        for j in range(M):
            if graph[i][j] == 6:
                continue
            if not visited[i][j]:
                blinds += 1
    return blinds

def cameraDir(directions, idx):
    global ans
    if idx == len(cameras):
        ans = min(ans, countBlindZones(directions))
    else:
        nowx, nowy = cameras[idx]
        camNum = graph[nowx][nowy]
        for i in range(camDirNum[camNum]):
            directions[idx] = i
            cameraDir(directions, idx + 1)
            

N, M = map(int, sys.stdin.readline().rstrip().split())
graph = []
cameras = []
for i in range(N):
    temp = list(map(int, sys.stdin.readline().rstrip().split()))
    for j in range(M):
        if 1 <= temp[j] <= 5:
            cameras.append([i, j])
    graph.append(temp)

dx = [-1, 0, 1, 0]
dy = [0, 1, 0, -1]
camDirNum = [0, 4, 2, 4, 4, 1]
camLookNum = [0, 1, 2, 2, 3, 4]
ans = INF
cameraDir([0] * len(cameras), 0)

print(ans)
```

### 등차수열 변환
```
import sys
INF = int(1e9)
sys.setrecursionlimit(100000)

def backTracking(idx, prev, diff, changes):
    global ans
    if idx == B:
        ans = min(ans, changes)
        return

    now = numList[idx]
    for i in range(-1, 2):
        newNum = now + i
        if newNum - prev == diff:
            if i == 0:
                backTracking(idx + 1, newNum, diff, changes)
            else:
                backTracking(idx + 1, newNum, diff, changes + 1)


B = int(sys.stdin.readline().rstrip())
numList = list(map(int, sys.stdin.readline().rstrip().split()))

if B <= 2:
    print(0)
    exit(0)
else:
    firstNum = numList[0]
    secondNum = numList[1]
    ans = INF
    for i in range(-1, 2):
        fNum = firstNum + i
        fChanges = 1
        if i == 0:
            fChanges = 0
        for j in range(-1, 2):
            sNum = secondNum + j
            if j == -1 or j == 1:
                sChanges = fChanges + 1
            else:
                sChanges = fChanges
            numDiff = sNum - fNum
            backTracking(2, sNum, numDiff, sChanges)

if ans == INF:
    print(-1)
else:
    print(ans)
```

### 치킨 배달
```
import sys
from itertools import combinations
INF = int(1e9)

def chickenDistance(stores):
    dist = 0
    for house in houses:
        temp = INF
        for store in stores:
            temp = min(temp, abs(store[0] - house[0]) + abs(store[1] - house[1]))
        dist += temp
    return dist

N, M = map(int, sys.stdin.readline().rstrip().split())
graph = []
chickens = []
houses = []
for i in range(N):
    temp = list(map(int, sys.stdin.readline().rstrip().split()))
    for j in range(N):
        if temp[j] == 1:
            houses.append([i, j])
        elif temp[j] == 2:
            chickens.append([i, j])

candidates = list(combinations(chickens, M))
ans = INF
for candidate in candidates:
    ans = min(ans, chickenDistance(candidate))

print(ans)
```

### 숫자판 점프
```
import sys

def makeNum(temp, x, y):
    global visited, ans
    if len(temp) == 6:
        temp = int(temp)
        if not visited[temp]:
            visited[temp] = True
            ans += 1
        return

    for i in range(4):
        nx = x + dx[i]
        ny = y + dy[i]
        if nx < 0 or nx >= 5 or ny < 0 or ny >= 5:
            continue
        newTemp = temp + graph[nx][ny]
        makeNum(newTemp, nx, ny)

graph = []
for _ in range(5):
    graph.append(list(sys.stdin.readline().rstrip().split()))

visited = [False] * 1000000
dx = [-1, 0, 1, 0]
dy = [0, 1, 0, -1]
ans = 0
for i in range(5):
    for j in range(5):
        temp = graph[i][j]
        makeNum(temp, i, j)


print(ans)
```

### 한윤정이 이탈리아에 가서 아이스크림을 사먹는데
```
import sys

N, M = map(int, sys.stdin.readline().rstrip().split())
visited = [[False] * 201 for _ in range(201)]
for _ in range(M):
    x, y = map(int, sys.stdin.readline().rstrip().split())
    visited[x][y] = True
    visited[y][x] = True

ans = 0

for i in range(1, N - 1):
    for j in range(i + 1, N):
        if visited[i][j]:
            continue
        for k in range(j + 1, N + 1):
            if visited[i][k] or visited[j][k]:
                continue
            ans += 1
print(ans)
```

### 세 친구
```
import sys
INF = int(1e9)

N, M = map(int, sys.stdin.readline().rstrip().split())
graph = [[False] * (N + 1) for _ in range(N + 1)]
friends = [0] * (N + 1)
for _ in range(M):
    x, y = map(int, sys.stdin.readline().rstrip().split())
    graph[x][y] = True
    graph[y][x] = True
    friends[x] += 1
    friends[y] += 1

ans = INF
for i in range(1, N - 1):
    for j in range(i + 1, N):
        if graph[i][j]:
            for k in range(j, N + 1):
                if graph[i][k] and graph[j][k]:
                    ans = min(ans, friends[i] + friends[j] + friends[k] - 6)

print(ans if ans != INF else -1)
```

### 배열 돌리기 4
```
import sys
import copy
from itertools import permutations
INF = int(1e9)

def rotate(info, graph):
    r, c, s = info
    for i in range(1, s + 1):
        x, y = r - i, c - i
        temp = graph[x][y]
        for direction in range(4):
            for j in range(i * 2):
                nx = x + dx[direction]
                ny = y + dy[direction]
                graph[x][y] = graph[nx][ny]
                x, y = nx, ny
        graph[r - i][c - i + 1] = temp
    return graph

def searchMin(graph):
    ans = INF
    for i in range(N):
        ans = min(ans, sum(graph[i]))
    return ans

N, M, K = map(int, sys.stdin.readline().rstrip().split())
graph = []
rotations = []
for _ in range(N):
    graph.append(list(map(int, sys.stdin.readline().rstrip().split())))
for _ in range(K):
    temp = list(map(int, sys.stdin.readline().rstrip().split()))
    temp[0] -= 1
    temp[1] -= 1
    rotations.append(temp)
dx = [1, 0, -1, 0]
dy = [0, 1, 0, -1]
ans = INF

candidates = list(permutations(rotations, len(rotations)))
for candidate in candidates:
    newGraph = copy.deepcopy(graph)
    for rotation in candidate:
        newGraph = rotate(rotation, newGraph)
    ans = min(ans, searchMin(newGraph))

print(ans)
```

### 캐슬 디펜스
```
import sys
import copy
from collections import deque
from itertools import combinations

def killEnemy(archers, graph):
    tempAns = 0
    for move in range(N):
        shootingSpots = []
        for archer in archers:
            q = deque()
            q.append(archer)
            visited = [[False] * M for _ in range(N + 1)]
            visited[archer[0]][archer[1]] = True
            tempSpots = []
            while q:
                x, y, d = q.popleft()
                if d == D:
                    break
                for i in range(3):
                    nx = x + dx[i]
                    ny = y + dy[i]
                    if nx < 0 or nx >= N or ny < 0 or ny >= M or visited[nx][ny]:
                        continue
                    visited[nx][ny] = True
                    q.append([nx, ny, d + 1])
                    if graph[nx][ny] == 1:
                        tempSpots.append([nx, ny, d])
            if len(tempSpots) > 0:
                tempSpots.sort(key=lambda x:(x[2], x[1]))
                shootingSpots.append(tempSpots[0])

        for x, y, z in shootingSpots:
            if graph[x][y] == 1:
                tempAns += 1
                graph[x][y] = 0
        for i in range(N, 0, -1):
            for j in range(M):
                graph[i][j] = graph[i - 1][j]
        for i in range(M):
            graph[N][i] = 0
            graph[0][i] = 0
    return tempAns


N, M, D = map(int, sys.stdin.readline().rstrip().split())

graph = []
for _ in range(N):
    graph.append(list(map(int, sys.stdin.readline().rstrip().split())))
graph.append([0] * M)

archerSpots = []
for i in range(M):
    archerSpots.append([N, i, 0])
dx = [0, -1, 0]
dy = [-1, 0, 1]

archerCandidates = list(combinations(archerSpots, 3))
ans = 0
for archers in archerCandidates:
    newGraph = copy.deepcopy(graph)
    ans = max(ans, killEnemy(archers, newGraph))

print(ans)
```

### ⚾
```
import sys
from itertools import permutations

def simulate(order):
    inning = 0
    score = 0
    playerIndex = 0
    outCount = 0
    while inning < N:
        # 새로운 이닝 시작
        base = []
        while True:
            try:
                result = graph[order[playerIndex]][inning]
            except IndexError:
                print('', end='')
            if result == 0:
                outCount += 1
                if outCount == 3:
                    inning += 1
                    playerIndex += 1
                    playerIndex %= 9
                    break
            elif 1 <= result <= 3:
                newBase = []
                for i in range(len(base)):
                    base[i] += result
                    if base[i] > 3:
                        score += 1
                    else:
                        newBase.append(base[i])
                newBase.append(result)
                base = newBase
            elif result == 4:
                score += len(base) + 1
                base = []
            playerIndex += 1
            playerIndex %= 9

        outCount = 0
    return score

N = int(sys.stdin.readline().rstrip())
graph = [[] for _ in range(9)]
for _ in range(N):
    temp = list(map(int, sys.stdin.readline().rstrip().split()))
    for i in range(9):
        graph[i].append(temp[i])
players = []
for i in range(9):
    if i == 0:
        continue
    players.append(i)
candidates = list(permutations(players, 8))
ans = 0
for playerList in candidates:
    playerList = list(playerList)
    playerList.insert(3, 0)
    ans = max(ans, simulate(playerList))

print(ans)
```
-> 문제를 똑바로 안 읽어서 푸는데 시간을 좀 낭비했다.. 문제를 똑바로 읽자..!!
