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

### 주사위 윷놀이
```
import sys
import copy

def dfs(graph, positions, idx, score):
    global maxScore
    if idx == 10:
        maxScore = max(maxScore, score)
        return
    move = moves[idx]
    for i in range(4):
        position = positions[i]
        if position == 42:
            continue
        # 이동 위치 확인
        for j in range(move):
            for elem in routes:
                if elem[0] == position:
                    if j == 0 and position in [10, 20, 30]:
                        position = elem[2]
                    else:
                        position = elem[1]
                    break
            if position == 42:
                break
        if position > 100:
            exactPosition = position // 10
        else:
            exactPosition = position
        # 이동 못하는 경우 처리
        if position != 42 and len(graph[exactPosition]) != 0:
            continue
        newGraph = copy.deepcopy(graph)
        newPositions = copy.deepcopy(positions)
        # 도착 시 처리
        if position == 42:
            if positions[i] > 100:
                positions[i] = positions[i] // 10
            newGraph[positions[i]].remove(i)
            newPositions[i] = position
            dfs(newGraph, newPositions, idx + 1, score)
        # 이동 경우 처리
        if exactPosition != 42 and len(graph[exactPosition]) == 0:
            if positions[i] > 100:
                positions[i] = positions[i] // 10
            newGraph[positions[i]].remove(i)
            newPositions[i] = position
            newGraph[exactPosition].append(i)
            dfs(newGraph, newPositions, idx + 1, score + exactPosition)


routes = []
for i in range(21):
    routes.append([i * 2, (i + 1) * 2])
additionals = [[10, 13], [13, 161], [161, 19], [19, 25], [20, 221], [221, 241], [241, 25], [30, 281], [281, 27], [27, 261],
               [261, 25], [25, 301], [301, 35], [35, 40]]
graph = [[] for _ in range(41)]
graph[0] = [0, 1, 2, 3]  # 4개의 말 시작점은 0
positions = [0, 0, 0, 0]

for x, y in additionals:
    flag = True
    for i in range(len(routes)):
        if routes[i][0] == x:
            routes[i].append(y)
            flag = False
            break
    if flag:
        routes.append([x, y])
moves = list(map(int, sys.stdin.readline().rstrip().split()))

maxScore = 0
dfs(graph, positions, 0, 0)

print(maxScore)
```
-> 테스트 케이스들은 다 통과하는데 결국 틀렸습니다가 떴다.. 그리고 테스트 케이스 수행때도 수행 시간이 10초가 넘게 걸린다..ㅋㅋ 코드를 다시 단순화할 필요가 있겠다.

### 주사위 윷놀이(모범 답안)
```
graph = [[1], [2], [3], [4], [5],
         [6, 21], [7], [8], [9], [10],
         [11, 25], [12], [13], [14], [15],
         [16, 27], [17], [18], [19], [20],
         [32], [22], [23], [24], [30],
         [26], [24], [28], [29], [24],
         [31], [20], [32]]

score = [0, 2, 4, 6, 8,
         10, 12, 14, 16, 18,
         20, 22, 24, 26, 28,
         30, 32, 34, 36, 38,
         40, 13, 16, 19, 25,
         22, 24, 28, 27, 26,
         30, 35, 0]

dice = list(map(int, input().split()))
answer = 0


def backtracking(depth, result, horses):
    global answer
    if depth == 10:
        answer = max(answer, result)
        return

    for i in range(4):
        # 현재 말 위치
        x = horses[i]

        # 현재 말 위치가 2갈래 갈 수 있는 위치(10, 20, 30)인지 체크
        if len(graph[x]) == 2:
            # 파란 길 한 칸 진입
            x = graph[x][1]
        else:
            # 빨간 길 한 칸 진입
            x = graph[x][0]

        # 나온 주사위 값 만큼 말 이동(위에서 1칸 이동했기 때문에 1 덜 이동함)
        for _ in range(1, dice[depth]):
            x = graph[x][0]

        # 목적지에 도착했거나 or (아직 목적지가 아니고 and 거기에 말이 없다면)
        if x == 32 or (x < 32 and x not in horses):
            before = horses[i]  # 이전 말의 위치
            horses[i] = x  # 현재 말 위치 갱신

            backtracking(depth + 1, result + score[x], horses)

            horses[i] = before


backtracking(0, 0, [0, 0, 0, 0])
print(answer)
```

### 계란으로 계란치기
```
import sys

def backtracking(eggs, idx, temp):
    global ans
    if temp + (N - idx) * 2 <= ans:
        return
    if idx == N:
        ans = max(ans, temp)
        return
    if eggs[idx][0] <= 0:
        backtracking(eggs, idx + 1, temp)
    else:
        flag = True
        for i in range(N):
            if eggs[i][0] <= 0 or i == idx:
                continue
            flag = False
            newTemp = temp
            eggs[idx][0] -= eggs[i][1]
            if eggs[idx][0] <= 0:
                newTemp += 1
            eggs[i][0] -= eggs[idx][1]
            if eggs[i][0] <= 0:
                newTemp += 1
            backtracking(eggs, idx + 1, newTemp)
            eggs[idx][0] += eggs[i][1]
            eggs[i][0] += eggs[idx][1]
        if flag:
            backtracking(eggs, idx + 1, temp)


N = int(sys.stdin.readline().rstrip())

eggs = []
for _ in range(N):
    temp = list(map(int, sys.stdin.readline().rstrip().split()))
    eggs.append(temp)

ans = 0
backtracking(eggs, 0, 0)
print(ans)
```
-> backtracking 코드를 짤 때에는 pruning 코드를 활용하는 것을 잊지 말자

### Baaaaaaaaaduk2(Easy)
```
import sys
from collections import deque
from itertools import combinations

N, M = map(int, sys.stdin.readline().rstrip().split())

graph = []
blanks = []
dx = [0, 1, 0, -1]
dy = [1, 0, -1, 0]
for i in range(N):
    temp = list(map(int, sys.stdin.readline().rstrip().split()))
    for j in range(M):
        if temp[j] == 0:
            blanks.append([i, j])
    graph.append(temp)

candidates = list(combinations(blanks, 2))
ans = 0
for candidate in candidates:
    if candidate == ([0, 0], [4, 2]):
        print('', end='')
    graph[candidate[0][0]][candidate[0][1]] = 1
    graph[candidate[1][0]][candidate[1][1]] = 1
    visited = [[False] * M for _ in range(N)]
    score = 0
    for i in range(N):
        for j in range(M):
            if graph[i][j] == 2 and not visited[i][j]:
                q = deque()
                q.append([i, j])
                tempCnt = 1
                visited[i][j] = True
                flag = False
                while q:
                    x, y = q.popleft()
                    for k in range(4):
                        nx = x + dx[k]
                        ny = y + dy[k]
                        if nx < 0 or nx >= N or ny < 0 or ny >= M or visited[nx][ny] or graph[nx][ny] == 1:
                            continue
                        if graph[nx][ny] == 0:
                            flag = True
                        if graph[nx][ny] == 2:
                            tempCnt += 1
                            visited[nx][ny] = True
                            q.append([nx, ny])
                if flag:
                    continue
                else:
                    score += tempCnt
    ans = max(ans, score)
    graph[candidate[0][0]][candidate[0][1]] = 0
    graph[candidate[1][0]][candidate[1][1]] = 0

print(ans)
```

### 사다리 조작
```
import sys

input = sys.stdin.readline

n, m, h = map(int, input().split())

data = [[False] * (n + 1) for _ in range(h + 1)]

# 데이터 입력받기
for _ in range(m):
    x, y = map(int, input().split())
    data[x][y] = True


# 현재 그래프 상태에서 조건을 만족하는지 판단.
def check(data):
    for i in range(1, n):
        my_num = i
        for j in range(1, h + 1):
            # 내려가던중 오른쪽으로 선있으면 숫자 +1
            if data[j][my_num]:
                my_num += 1
            # 내려가던중 왼쪽에 선있으면 숫자 -1
            elif data[j][my_num - 1]:
                my_num -= 1

        if my_num != i:
            return False

    return True


result = 4


# 전체 탐색을 위한 재귀 함수
def dfs(depth, data, x, y):
    global result
    if depth >= result:
        return

    # 주어진 사다리가 조건을 충족하면 결과 갱신
    if check(data):
        result = min(result, depth)
        return

    if depth == 3:
        return

    # 현재 깊이에서 안되면 다음 깊이 탐색
    for i in range(x, h + 1):
        ########################
        if x == i:
            k = y
        else:
            k = 0
        ########################
        for j in range(k, n):
            if data[i][j] == False and data[i][j + 1] == False and data[i][j - 1] == False:
                data[i][j] = True
                # 방금 추가해준 사다리 위치를 기억하고 그다음 사다리부터 추가되도록 작성
                # 추가된 좌표를 하나의 값으로 바꿔서 다다음 함수로 보냄
                dfs(depth + 1, data, i, j + 2)
                data[i][j] = False


dfs(0, data, 1, 1)
if result == 4:
    result = -1

print(result)
```

### 매직 스퀘어로 변경하기
```
import sys
INF = int(1e9)

def isMagicSquare(graph):
    temp = sum(graph[0])
    for i in range(3):
        if temp != sum(graph[i]):
            return False
        if temp != graph[0][i] + graph[1][i] + graph[2][i]:
            return False
    if temp != graph[0][0] + graph[1][1] + graph[2][2]:
        return False
    if temp != graph[0][2] + graph[1][1] + graph[2][0]:
        return False
    return True

def backTracking(graph, now, idx, history, temp):
    global ans
    if idx == 9:
        if isMagicSquare(graph):
            ans = min(ans, temp)
        return
    if temp >= ans:
        return
    x, y = now
    originalValue = graph[x][y]
    candidates = [i for i in range(1, 10)]
    for elem in history:
        candidates.remove(elem)
    for elem in candidates:
        graph[x][y] = elem
        nx = x
        ny = y + 1
        if ny == 3:
            nx += 1
            ny = 0
        history.append(elem)
        backTracking(graph, [nx, ny], idx + 1, history, temp + abs(originalValue - elem))
        history.remove(elem)
        graph[x][y] = originalValue

graph = []
for _ in range(3):
    graph.append(list(map(int, sys.stdin.readline().rstrip().split())))

ans = INF
backTracking(graph, [0, 0], 0, [], 0)
print(ans)
```

### A -> B
```
import sys
INF = int(1e9)

def dfs(a, b, cnt):
    global ans
    if a > b:
        return
    if a == b:
        ans = min(ans, cnt)
    na = a * 2
    dfs(na, b, cnt + 1)
    na = a * 10 + 1
    dfs(na, b, cnt + 1)

a, b = map(int, sys.stdin.readline().rstrip().split())

ans = INF

dfs(a, b, 1)
print(ans if ans != INF else -1)
```

### 색종이 붙이기
```
import sys
INF = int(1e9)

def canCover(i, j, k):
    for ni in range(i, i + k):
        for nj in range(j, j + k):
            if ni > 9 or nj > 9:
                return False
            if visited[ni][nj]:
                return False
    return True

def dfs(now, numPapers, temp, visited):
    global ans

    if now == [10, 0]:
        for i in range(10):
            for j in range(10):
                if not visited[i][j]:
                    return
        ans = min(ans, temp)
        return
    if temp >= ans:
        return
    x, y = now
    nx = x
    ny = y + 1
    if ny == 10:
        nx += 1
        ny = 0
    if visited[x][y]:
        dfs([nx, ny], numPapers, temp, visited)
    else:
        if now == [3, 4]:
            print('', end='')

        maskSize = 1
        for k in range(1, 6):
                if canCover(x, y, k):
                    maskSize = k
                else:
                    break
        for ms in range(maskSize, 0, -1):
            if numPapers[ms - 1] > 0:
                # 큰 거 부터 채움
                for ni in range(x, x + ms):
                    for nj in range(y, y + ms):
                        visited[ni][nj] = True
                numPapers[ms - 1] -= 1
                dfs([nx, ny], numPapers, temp + 1, visited)
                for ni in range(x, x + ms):
                    for nj in range(y, y + ms):
                        visited[ni][nj] = False
                numPapers[ms - 1] += 1

graph = []
visited = [[True] * 10 for _ in range(10)]
for i in range(10):
    temp = list(map(int, sys.stdin.readline().rstrip().split()))
    for j in range(10):
        if temp[j] == 1:
            visited[i][j] = False
    graph.append(temp)

numPapers = [5, 5, 5, 5, 5]
ans = INF

dfs([0, 0], numPapers, 0, visited)


print(ans if ans != INF else -1)
```

### 게리맨더링
```
import sys
import copy
from itertools import combinations
from collections import deque
INF = int(1e9)

def isPossible(teamA, teamB):
    teams = [0] * N
    for elem in teamA:
        teams[elem] = 1
    for elem in teamB:
        teams[elem] = 2
    visited = [False] * N
    q = deque()
    q.append(teamA[0])
    aCheck = [teamA[0]]
    visited[teamA[0]] = True
    while q:
        now = q.popleft()
        for i in range(N):
            if graph[now][i] == 1 and teams[i] == 1 and not visited[i]:
                visited[i] = True
                q.append(i)
                aCheck.append(i)
    teamA = list(teamA)
    teamA.sort()
    aCheck.sort()
    if teamA != aCheck:
        return False
    q = deque()
    q.append(teamB[0])
    visited[teamB[0]] = True
    while q:
        now = q.popleft()
        for i in range(N):
            if graph[now][i] == 1 and teams[i] == 2 and not visited[i]:
                visited[i] = True
                q.append(i)
    if False in visited:
        return False
    return True

N = int(sys.stdin.readline().rstrip())

populations = list(map(int, sys.stdin.readline().rstrip().split()))
graph = [[0] * N for _ in range(N)]

for i in range(N):
    temp = list(map(int, sys.stdin.readline().rstrip().split()))
    for j in range(1, len(temp)):
        graph[i][temp[j] - 1] = 1

wholeGroup = [i for i in range(N)]

ans = INF
for i in range(1, N):
    aCandidates = list(combinations(wholeGroup, i))
    for aTeam in aCandidates:
        bTeam = copy.deepcopy(wholeGroup)
        for elem in aTeam:
            bTeam.remove(elem)
        # 팀 나눴으니 아래에서 연산 수행하면 됨
        if isPossible(aTeam, bTeam):
            aPopulation = 0
            bPopulation = 0
            for elem in aTeam:
                aPopulation += populations[elem]
            for elem in bTeam:
                bPopulation += populations[elem]
            ans = min(ans, abs(aPopulation - bPopulation))

print(ans if ans != INF else -1)
```

### Maaaaaaaaaze
```
import sys
from itertools import permutations
from collections import deque
INF = int(1e9)

def rotate90(graph):
    rotated = [[0] * 5 for _ in range(5)]
    for i in range(5):
        for j in range(5):
            rotated[j][4 - i] = graph[i][j]
    return rotated

def findShortestPath(graph):
    global ans
    if graph[0][0][0] == 0:
        return
    q = deque()
    visited = [[[False] * 5 for _ in range(5)] for _ in range(5)]
    q.append([0, 0, 0, 0])
    visited[0][0][0] = True
    while q:
        z, x, y, cost = q.popleft()
        if cost >= ans:
            return
        if z == 4 and x == 4 and y == 4:
            ans = min(ans, cost)
            if ans == 12:
                print(12)
                exit(0)
            return
        for i in range(6):
            nz = z + dz[i]
            nx = x + dx[i]
            ny = y + dy[i]
            if nz < 0 or nz > 4 or nx < 0 or nx > 4 or ny < 0 or ny > 4 or visited[nz][nx][ny] or graph[nz][nx][ny] == 0:
                continue
            visited[nz][nx][ny] = True
            q.append([nz, nx, ny, cost + 1])


def findAllCases(graph, depth):
    if depth == 5:
        tempGraph = [[[0] * 5 for _ in range(5)] for _ in range(5)]
        for elem in candidates:
            for i in range(5):
                tempGraph[i] = graph[elem[i]]
            findShortestPath(tempGraph)
        return

    for i in range(4):
        graph[depth] = rotate90(graph[depth])
        findAllCases(graph, depth + 1)

graph = [[[0] * 5 for _ in range(5)] for _ in range(5)]

zIndex = 0
xIndex = 0
for _ in range(25):
    temp = list(map(int, sys.stdin.readline().rstrip().split()))
    graph[zIndex][xIndex] = temp
    xIndex += 1
    if xIndex == 5:
        zIndex += 1
        xIndex = 0

dz = [0, 0, 0, 0, -1, 1]
dx = [0, 1, 0, -1, 0, 0]
dy = [1, 0, -1, 0, 0, 0]
layerNums = [i for i in range(5)]
candidates = list(permutations(layerNums, 5))
ans = INF
findAllCases(graph, 0)

print(ans if ans != INF else -1)
```

### 미로 탈출하기
```
import sys
from collections import deque

N, M = map(int, sys.stdin.readline().rstrip().split())
graph = []
for i in range(N):
    temp = sys.stdin.readline().rstrip()
    tempNum = []
    for j in range(M):
        if temp[j] == 'U':
            tempNum.append(0)
        elif temp[j] == 'R':
            tempNum.append(1)
        elif temp[j] == 'D':
            tempNum.append(2)
        else:
            tempNum.append(3)
    graph.append(tempNum)

dx = [-1, 0, 1, 0]
dy = [0, 1, 0, -1]

visited = [[False] * M for _ in range(N)]

ans = 0

for i in range(N):
    for j in range(M):
        if not visited[i][j]:
            q = deque()
            q.append([i, j])
            visited[i][j] = True
            cnt = 1
            flag = False
            while q:
                x, y = q.popleft()
                dir = graph[x][y]
                nx = x + dx[dir]
                ny = y + dy[dir]
                if nx < 0 or nx >= N or ny < 0 or ny >= M:
                    flag = True
                else:
                    if not visited[nx][ny]:
                        q.append([nx, ny])
                        visited[nx][ny] = True
                        cnt += 1
                for k in range(4):
                    nx = x + dx[k]
                    ny = y + dy[k]
                    if nx < 0 or nx >= N or ny < 0 or ny >= M or visited[nx][ny]:
                        continue
                    if (k == 0 and graph[nx][ny] == 2) or (k == 1 and graph[nx][ny] == 3) or (k == 2 and graph[nx][ny] == 0) or (k == 3 and graph[nx][ny] == 1):
                        q.append([nx, ny])
                        visited[nx][ny] = True
                        cnt += 1
            if flag:
                ans += cnt

print(ans)
```

### 두 배 더하기
```
import sys

N = int(sys.stdin.readline().rstrip())
B = list(map(int, sys.stdin.readline().rstrip().split()))
A = [0] * N

ans = 0
while B != A:
    flag = True
    for i in range(len(B)):
        if B[i] % 2 == 1:
            B[i] -= 1
            ans += 1
            flag = False
    if flag:
        for i in range(len(B)):
            B[i] //= 2
        ans += 1

print(ans)
```
-> 거꾸로 생각하면 풀기 쉽다

### 텔레포트
```
import sys
INF = int(1e9)

N, T = map(int, sys.stdin.readline().rstrip().split())
graph = [[INF] * (N + 1) for _ in range(N + 1)]
isSpecial = [False] * (N + 1)
positions = [[] for _ in range(N + 1)]

for i in range(N):
    s, x, y = map(int, sys.stdin.readline().rstrip().split())
    if s == 1:
        isSpecial[i + 1] = True
    positions[i + 1] = [x, y]

M = int(sys.stdin.readline().rstrip())
questions = []
for _ in range(M):
    questions.append(list(map(int, sys.stdin.readline().rstrip().split())))

for i in range(1, N):
    for j in range(i + 1, N + 1):
        if i == j:
            continue
        distance = abs(positions[i][0] - positions[j][0]) + abs(positions[i][1] - positions[j][1])
        if isSpecial[i] and isSpecial[j] and T < distance:
            distance = T
        graph[i][j] = distance
        graph[j][i] = distance

for i in range(1, N + 1):
    for j in range(1, N + 1):
        for k in range(1, N + 1):
            if graph[i][k] + graph[k][j] < graph[i][j]:
                graph[i][j] = graph[i][k] + graph[k][j]

for elem in questions:
    print(graph[elem[0]][elem[1]])
```

### 텔레포트3
```
import sys
INF = int(1e10)

def dfs(now, isPortUsed, dist):
    global ans
    # 현재 위치에서 end로 간다.
    ans = min(ans, dist + abs(now[0] - end[0]) + abs(now[1] - end[1]))
    # 현재 위치에서 1 - 1 teleport로 간다
    # 현재 위치에서 1 - 2 teleport로 간다
    for i in range(3):
        if not isPortUsed[i]:
            isPortUsed[i] = True
            # 앞의 장소를 통해 이동
            next = teleport[i][1]
            newDist = dist + 10 + abs(now[0] - teleport[i][0][0]) + abs(now[1] - teleport[i][0][1])
            dfs(next, isPortUsed, newDist)
            # 뒤의 장소를 통해 이동
            next = teleport[i][0]
            newDist = dist + 10 + abs(now[0] - teleport[i][1][0]) + abs(now[1] - teleport[i][1][1])
            dfs(next, isPortUsed, newDist)
            isPortUsed[i] = False


start = list(map(int, sys.stdin.readline().rstrip().split()))
end = list(map(int, sys.stdin.readline().rstrip().split()))

teleport = []
# 텔레포트 가능 장소 3곳
for _ in range(3):
    a, b, c, d = map(int, sys.stdin.readline().rstrip().split())
    teleport.append([[a, b], [c, d]])

ans = INF
visited = [False] * 3

dfs(start, visited, 0)

print(ans)
```

### 체스판 위의 공
```
import sys
from heapq import heappop, heappush

r, c = map(int, sys.stdin.readline().rstrip().split())
numGraph = []
for _ in range(r):
    numGraph.append(list(map(int, sys.stdin.readline().rstrip().split())))

ballGraph = [[1] * c for _ in range(r)]
destGraph = [[[-1, -1] for _ in range(c)] for _ in range(r)]

dx = [-1, -1, 0, 1, 1, 1, 0, -1]
dy = [0, -1, -1, -1, 0, 1, 1, 1]

# 현재 위치에서 시작하면 최종 목적지가 어딘지 확인하기
for x in range(r):
    for y in range(c):
        continueFlag = True
        mx, my = x, y
        while continueFlag:
            if destGraph[mx][my] != [-1, -1]:
                break
            continueFlag = False
            movePos = []
            for i in range(8):
                nx = mx + dx[i]
                ny = my + dy[i]
                if nx < 0 or nx >= r or ny < 0 or ny >= c:
                    continue
                if numGraph[nx][ny] < numGraph[mx][my]:
                    heappush(movePos, [numGraph[nx][ny], nx, ny])
            if len(movePos) > 0:
                continueFlag = True
                _, mx, my = heappop(movePos)
        if continueFlag:
            destGraph[x][y] = destGraph[mx][my]
        else:
            destGraph[x][y] = [mx, my]

for i in range(r):
    for j in range(c):
        dest = destGraph[i][j]
        if i == dest[0] and j == dest[1]:
            continue
        ballGraph[dest[0]][dest[1]] += ballGraph[i][j]
        ballGraph[i][j] = 0

for row in ballGraph:
    for elem in row:
        print(elem, end=' ')
    print()
```

### 배열 B의 값
```
import sys
import copy
INF = int(1e15)

def sumOfGraph(tempGraph):
    temp = 0
    for i in range(N - 1):
        for j in range(M - 1):
            temp += tempGraph[i][j]
    return temp

def createNewGraph(tempGraph):
    newGraph = [[0] * (M - 1) for _ in range(N - 1)]
    for i in range(N - 1):
        for j in range(M - 1):
            newGraph[i][j] = tempGraph[i][j] + tempGraph[i + 1][j] + tempGraph[i + 1][j + 1] + tempGraph[i][j + 1]
    return newGraph

def changeRow(graph, x, y):
    tempGraph = copy.deepcopy(graph)
    temp = tempGraph[x]
    tempGraph[x] = tempGraph[y]
    tempGraph[y] = temp
    return tempGraph

def changeCol(graph, x, y):
    tempGraph = copy.deepcopy(graph)
    temp = []
    for i in range(N):
        temp.append(tempGraph[i][x])
        tempGraph[i][x] = tempGraph[i][y]
        tempGraph[i][y] = temp[i]
    return tempGraph


N, M = map(int, sys.stdin.readline().rstrip().split())
graph = []
for _ in range(N):
    graph.append(list(map(int, sys.stdin.readline().rstrip().split())))

answer = INF * (-1)

newGraph = createNewGraph(graph)
sumOfOrigin = sumOfGraph(newGraph)
answer = max(answer, sumOfOrigin)

minRow = 1
minCol = 1
minRowVal = INF
minColVal = INF

# 가장 작은 row 찾기
for i in range(1, N - 1):
    temp = 0
    for j in range(M):
        if j == 0 or j == M - 1:
            temp += graph[i][j]
        else:
            temp += 2 * graph[i][j]
    if temp < minRowVal:
        minRowVal = temp
        minRow = i

# 가장 작은 col 찾기
for j in range(1, M - 1):
    temp = 0
    for i in range(N):
        if i == 0 or i == N - 1:
            temp += graph[i][j]
        else:
            temp += 2 * graph[i][j]
    if temp < minColVal:
        minColVal = temp
        minCol = j

# row 바꾸는 경우
tempGraph = changeRow(graph, minRow, 0)
newGraph = createNewGraph(tempGraph)
temp = sumOfGraph(newGraph)
answer = max(answer, temp)

tempGraph = changeRow(graph, minRow, N - 1)
newGraph = createNewGraph(tempGraph)
temp = sumOfGraph(newGraph)
answer = max(answer, temp)


# col 바꾸는 경우
tempGraph = changeCol(graph, minCol, 0)
newGraph = createNewGraph(tempGraph)
temp = sumOfGraph(newGraph)
answer = max(answer, temp)

tempGraph = changeCol(graph, minCol, M - 1)
newGraph = createNewGraph(tempGraph)
temp = sumOfGraph(newGraph)
answer = max(answer, temp)

print(answer)
```

### 다리 만들기 2
```
import sys
from collections import deque
import heapq

def find(x):
    if parent[x] == x:
        return x
    else:
        return find(parent[x])

def union(x, y):
    xp = find(x)
    yp = find(y)
    if xp < yp:
        parent[yp] = xp
    else:
        parent[xp] = yp

N, M = map(int, sys.stdin.readline().rstrip().split())
graph = []
for _ in range(N):
    graph.append(list(map(int, sys.stdin.readline().rstrip().split())))

dx = [1, 0, -1, 0]
dy = [0, 1, 0, -1]

# 섬들 찾기
visited = [[False] * M for _ in range(N)]
islandNum = 1
poses = [[]]
for i in range(N):
    for j in range(M):
        if graph[i][j] == 1 and not visited[i][j]:
            q = deque()
            q.append([i, j])
            visited[i][j] = True
            graph[i][j] = islandNum
            poses.append([[i, j]])
            while q:
                x, y = q.popleft()
                for k in range(4):
                    nx = x + dx[k]
                    ny = y + dy[k]
                    if nx < 0 or nx >= N or ny < 0 or ny >= M or graph[nx][ny] == 0 or visited[nx][ny]:
                        continue
                    q.append([nx, ny])
                    visited[nx][ny] = True
                    graph[nx][ny] = islandNum
                    poses[islandNum].append([nx, ny])
            islandNum += 1

# 섬들의 총 갯수
numIsland = islandNum - 1

# 그릴 수 있는 다리들을 다 구한 다음에 minimum spanning tree
bridges = []

for i in range(1, numIsland + 1):
    for x, y in poses[i]:
        flag = False
        for k in range(4):
            nx = x + dx[k]
            ny = y + dy[k]
            if nx < 0 or nx >= N or ny < 0 or ny >= M or graph[nx][ny] != 0:
                continue
            dist = 1
            while True:
                nx += dx[k]
                ny += dy[k]
                if nx < 0 or nx >= N or ny < 0 or ny >= M or graph[nx][ny] == i:
                    break
                if graph[nx][ny] != 0:
                    if dist == 1:
                        break
                    heapq.heappush(bridges, [dist, i, graph[nx][ny]])
                    break
                dist += 1

constructed = []
ans = 0
parent = [i for i in range(numIsland + 1)]

while bridges:
    dist, x, y = heapq.heappop(bridges)
    if [x, y] not in constructed and [y, x] not in constructed and (find(x) != 1 or find(y) != 1):
        constructed.append([x, y])
        union(x, y)
        ans += dist
    if len(constructed) == numIsland - 1:
        break

if len(constructed) == numIsland - 1:
    print(ans)
else:
    print(-1)
```
