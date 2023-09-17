# 브루트 포스 part 2
### 게리맨더링 2
```
import sys
INF = int(1e9)

def countPopDiff(x, y, d1, d2):
    global ans
    sectors = [[0] * (N + 1) for _ in range(N + 1)]
    # 5번 경계 입력
    for i in range(d1 + 1):
        sectors[x + i][y - i] = 5
        sectors[x + d2 + i][y + d2 - i] = 5
    for i in range(d2 + 1):
        sectors[x + i][y + i] = 5
        sectors[x + d1 + i][y - d1 + i] = 5
    for i in range(1, N + 1):
        index = []
        for j in range(1, N + 1):
            if sectors[i][j] == 5:
                index.append(j)
        if len(index) == 2:
            for j in range(index[0], index[1]):
                sectors[i][j] = 5

    for i in range(1, N + 1):
        for j in range(1, N + 1):
            if 1 <= i < x + d1 and 1 <= j <= y and sectors[i][j] == 0:
                sectors[i][j] = 1
            elif 1 <= i <= x + d2 and y < j <= N and sectors[i][j] == 0:
                sectors[i][j] = 2
            elif x + d1 <= i <= N and 1 <= j < y - d1 + d2 and sectors[i][j] == 0:
                sectors[i][j] = 3
            elif x + d2 < i <= N and y - d1 + d2 <= j <= N and sectors[i][j] == 0:
                sectors[i][j] = 4

    populations = [0] * 5
    for i in range(1, N + 1):
        for j in range(1, N + 1):
            populations[sectors[i][j] - 1] += graph[i - 1][j - 1]

    return max(populations) - min(populations)


N = int(sys.stdin.readline().rstrip())
graph = []
for _ in range(N):
    graph.append(list(map(int, sys.stdin.readline().rstrip().split())))

nums = []
for i in range(1, N + 1):
    nums.append(i)

ans = INF
for i in range(1, N):
    for j in range(1, N):
        for x in range(1, N - i - j + 1):
            for y in range(1 + i, N - j + 1):
                if 1 <= x and x < x + i + j and x + i + j <= N and 1 <= y - i and y - i < y and y < y + j and y + j <= N:
                    ans = min(ans, countPopDiff(x, y, i, j))

print(ans)
```
-> 어렵지 않은 문제였는데 각 변수들의 scope를 잘못 설정하였다

### 파이프 옮기기1(DFS 풀이)
```
import sys

def checkType(head, tail):
    a = tail[0] - head[0]
    b = tail[1] - head[1]
    if a == 1 and b == 1:
        t = 2
    elif a == 1 and b == 0:
        t = 1
    else:
        t = 0
    return t

def dfs(head, tail):
    global ans
    t = checkType(head, tail)
    hx, hy = head
    tx, ty = tail
    if tx == N - 1 and ty == N - 1:
        ans += 1
        return

    # 이동
    nhx = hx + dx[t]
    nhy = hy + dy[t]
    ntx = tx + dx[t]
    nty = ty + dy[t]

    # 회전
    for i in range(len(rx[t])):
        flag = 0
        nntx = ntx + rx[t][i]
        nnty = nty + ry[t][i]
        if nntx < 0 or nntx >= N or nnty < 0 or nnty >= N or graph[nntx][nnty] == 1:
            continue
        nt = checkType([nhx, nhy], [nntx, nnty])
        if nt == 2:
            for j, k in ([[-1, 0], [0, -1]]):
                if nntx + j < 0 or nntx + j >= N or nnty + k < 0 or nnty + k >= N or graph[nntx + j][nnty + k] == 1:
                    flag = 1
                    break
        if flag:
            continue
        dfs([nhx, nhy], [nntx, nnty])



N = int(sys.stdin.readline().rstrip())
graph = []
for _ in range(N):
    graph.append(list(map(int, sys.stdin.readline().rstrip().split())))

head = [0, 0]
tail = [0, 1]

dx = [0, 1, 1]
dy = [1, 0, 1]
rx = [[0, 1], [0, 0], [0, -1, 0]]
ry = [[0, 0], [0, 1], [0, 0, -1]]

ans = 0
dfs(head, tail)

print(ans)
```
-> dfs로 풀었는데 시간초과가 나서 위 문제는 dp로 풀 수 있을거 같다

### 파이프 옮기기1(dp 풀이)
```
import sys

N = int(sys.stdin.readline().rstrip())
graph = []
for _ in range(N):
    graph.append(list(map(int, sys.stdin.readline().rstrip().split())))

dx = [0, -1, 0]
dy = [0, 0, -1]

types = [[[0, 0, 0] for _ in range(N)] for _ in range(N)]
types[0][1][0] = 1

for i in range(N):
    for j in range(N):
        if graph[i][j] == 1:
            continue
        flag = 0
        # 옆에서
        if 0 <= j - 1 < N:
            types[i][j][0] += types[i][j - 1][0]
            types[i][j][0] += types[i][j - 1][2]
        # 위에서
        if 0 <= i - 1 < N:
            types[i][j][1] += types[i - 1][j][1]
            types[i][j][1] += types[i - 1][j][2]
        # 대각선에서
        for k in range(3):
            nx = i + dx[k]
            ny = j + dy[k]
            if nx < 0 or nx >= N or ny < 0 or ny >= N or graph[nx][ny] == 1:
                flag = 1
                break
        if flag:
            continue
        types[i][j][2] += sum(types[i - 1][j - 1])

print(sum(types[N - 1][N - 1]))
```

### 괄호 추가하기2
```
import sys
INF = int(1e10)

def operate(a, b, sign):
    if sign == '+':
        return a + b
    elif sign == '-':
        return a - b
    elif sign == '*':
        return a * b

# 괄호 연산 먼저한 후에 곱하기 연산, 그 후에 순차 연산
def calculate(isBrackets):
    if isBrackets == [False, False, False, False, True, False, True, False, True]:
        print('', end='')

    newNums = []
    newSigns = []
    # 괄호 먼저 연산
    i = 0
    while i < len(isBrackets):
        if isBrackets[i]:
            if len(newNums) > 0:
                newNums[-1] = operate(newNums[-1], nums[i + 1], signs[i])
            else:
                newNums.append(operate(nums[i], nums[i + 1], signs[i]))
            i += 1
        else:
            if len(newNums) == 0:
                newNums.append(nums[0])
            newNums.append(nums[i + 1])
            newSigns.append(signs[i])
            i += 1

    # 곱셈 연산
    fNums = []
    fSigns = []
    i = 0
    while i < len(newSigns):
        if newSigns[i] == '*':
            if len(fNums) > 0:
                fNums[-1] = fNums[-1] * newNums[i + 1]
            else:
                fNums.append(newNums[i] * newNums[i + 1])
            i += 1
        else:
            if len(fNums) == 0:
                fNums.append(newNums[0])
            fNums.append(newNums[i + 1])
            fSigns.append(newSigns[i])
            i += 1

    # 순차 연산
    val = fNums[0]
    for i in range(1, len(fNums)):
        val = operate(val, fNums[i], fSigns[i - 1])

    return val


def dfs(isBrackets, idx):
    global ans
    if idx == len(isBrackets):
        ans = max(ans, calculate(isBrackets))
    else:
        if idx == 0:
            dfs(isBrackets, idx + 1)
            isBrackets[idx] = True
            dfs(isBrackets, idx + 1)
            isBrackets[idx] = False
        else:
            if isBrackets[idx - 1] == True:
                dfs(isBrackets, idx + 1)
            else:
                dfs(isBrackets, idx + 1)
                isBrackets[idx] = True
                dfs(isBrackets, idx + 1)
                isBrackets[idx] = False


N = int(sys.stdin.readline().rstrip())

temp = list(sys.stdin.readline().rstrip())
nums = []
signs = []
for i in range(len(temp)):
    if i % 2 == 0:
        nums.append(int(temp[i]))
    else:
        signs.append(temp[i])

ans = (-1) * INF

isBrackets = [False] * len(signs)

if N == 1:
    ans = nums[0]
elif N == 3:
    ans = operate(nums[0], nums[1], signs[0])
else:
    dfs(isBrackets, 0)

print(ans)
```

### 십자가 2개 놓기
```
import sys
from itertools import combinations

def checkBiggest(apos, bpos):
    global ans
    ax, ay = apos
    bx, by = bpos
    asize = sizes[ax][ay]
    bsize = sizes[bx][by]

    aList = []
    for i in range(asize + 1):
        for k in range(4):
            nax = ax + dx[k] * i
            nay = ay + dy[k] * i
            aList.append([nax, nay])
        bList = []
        for j in range(bsize + 1):
            for k in range(4):
                nbx = bx + dx[k] * j
                nby = by + dy[k] * j
                bList.append([nbx, nby])

            flag = True
            for aelem in aList:
                for belem in bList:
                    if aelem == belem:
                        flag = False
                        break
                if not flag:
                    break
            if flag:
                ans = max(ans, (i * 4 + 1) * (j * 4 + 1))


N, M = map(int, sys.stdin.readline().rstrip().split())

graph = []
for _ in range(N):
    graph.append(list(sys.stdin.readline().rstrip()))

dx = [-1, 0, 1, 0]
dy = [0, 1, 0, -1]

candidatesPos = []
sizes = [[-1] * M for _ in range(N)]

for x in range(N):
    for y in range(M):
        for k in range(8):
            breakFlag = False
            for i in range(4):
                nx = x + dx[i] * k
                ny = y + dy[i] * k
                if nx < 0 or nx >= N or ny < 0 or ny >= M or graph[nx][ny] == '.':
                    breakFlag = True
                    break
            if breakFlag:
                break
            sizes[x][y] = max(sizes[x][y], k)
            if [x, y] not in candidatesPos:
                candidatesPos.append([x, y])

candidates = list(combinations(candidatesPos, 2))

ans = 1
for candidate in candidates:
    apos, bpos = candidate
    checkBiggest(apos, bpos)
print(ans)
```