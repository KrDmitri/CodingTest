# BFS 알고리즘
### 뱀과 사다리 게임
```
import sys

INF = int(1e9)
def findNext(num, type):
    for elem in type:
        if elem[0] == num:
            return elem[1]

n, m = map(int, sys.stdin.readline().split())
ladder = []
ladderBlank = []
snake = []
snakeBlank = []
for _ in range(n):
    x, y = map(int, sys.stdin.readline().split())
    ladder.append([x, y])
    ladderBlank.append(x)
for _ in range(m):
    x, y = map(int, sys.stdin.readline().split())
    snake.append([x, y])
    snakeBlank.append(x)
history = [INF] * 101
q = [[1, 0]]
answer = []

while len(q) > 0:
    q.sort(key=lambda x:x[1])
    nowInfo = q.pop(0)
    nowNum = nowInfo[0]
    nowTry = nowInfo[1]
    if nowNum >= 94:
        answer.append(nowTry + 1)
        break
    for i in range(1, 7):
        next = nowNum + i
        if next in snakeBlank:
            nextBlank = findNext(next, snake)
            if history[nextBlank] > nowTry + 1:
                history[nextBlank] = nowTry + 1
                q.append([nextBlank, nowTry + 1])
        elif next in ladderBlank:
            nextBlank = findNext(next, ladder)
            if nextBlank == 100:
                answer.append(nowTry)
                break
            if history[nextBlank] > nowTry + 1:
                history[nextBlank] = nowTry + 1
                q.append([nextBlank, nowTry + 1])
        else:
            if history[next] > nowTry + 1:
                history[next] = nowTry + 1
                q.append([next, nowTry + 1])

print(min(answer))
```

### 데스 나이트
```
import sys
from collections import deque

n = int(sys.stdin.readline())
x1, y1, x2, y2 = map(int, sys.stdin.readline().split())
dx = [-2, 0, 2, 2, 0, -2]
dy = [-1, -2, -1, 1, 2, 1]
visited = [[False] * n for _ in range(n)]

q = deque()
q.append([x1, y1, 0])
visited[x1][y1] = True

while q:
    now = q.popleft()
    nowx, nowy, nowCnt = now[0], now[1], now[2]
    for i in range(6):
        nx = nowx + dx[i]
        ny = nowy + dy[i]
        if nx == x2 and ny == y2:
            print(nowCnt + 1)
            exit(0)
        if nx >= 0 and nx < n and ny >= 0 and ny < n and not visited[nx][ny]:
            q.append([nx, ny, nowCnt + 1])
            visited[nx][ny] = True

print(-1)
```

### 연구소
```
import sys
import copy
from itertools import combinations
from collections import deque

def countBlanks(tempLab):
    num = 0
    for i in range(n):
        for j in range(m):
            if tempLab[i][j] == 0:
                num += 1
    return num

def spreadVirus(tempLab, virusList):
    q = deque(virusList)
    visited = [[False] * m for _ in range(n)]
    visited[q[0][0]][q[0][1]] = True
    while q:
        now = q.popleft()
        x, y = now[0], now[1]
        for i in range(4):
            nx = x + dx[i]
            ny = y + dy[i]
            if nx >= 0 and nx < n and ny >= 0 and ny < m and not visited[nx][ny] and tempLab[nx][ny] != 1:
                q.append([nx, ny])
                tempLab[nx][ny] = 2
                visited[nx][ny] = True
    blanks = countBlanks(tempLab)
    return blanks


n, m = map(int, sys.stdin.readline().split())
lab = []
virusPos = []
blanks = []
dx = [-1, 0, 1, 0]
dy = [0, -1, 0, 1]

for i in range(n):
    tempList = list(map(int, sys.stdin.readline().split()))
    for j in range(m):
        if tempList[j] == 2:
            virusPos.append([i, j])
        if tempList[j] == 0:
            blanks.append([i, j])
    lab.append(tempList)

candidates = list(combinations(blanks, 3))

answer = 0
for candidate in candidates:
    tempLab = copy.deepcopy(lab)
    for elem in candidate:
        tempLab[elem[0]][elem[1]] = 1
    blanks = spreadVirus(tempLab, virusPos)
    answer = max(answer, blanks)

print(answer)
```
-> 이 문제는 5달 전에도 풀었었는데 코드 길이를 반으로 줄이고 실행 시간도 3배 이상 단축했다.

### 돌 그룹
```
import sys
from collections import deque

def operate(x, y):
    if x < y:
        temp = x
        x += temp
        y -= temp
    else:
        temp = y
        y += temp
        x -= temp
    return x, y

inputList = list(map(int, sys.stdin.readline().split()))
if sum(inputList) % 3 != 0:
    print(0)
else:
    inputList.sort()
    visited = [[False] * 1501 for _ in range(1501)]
    visited[inputList[0]][inputList[2]] = True
    cases = [[0, 1, 2], [1, 2, 0], [2, 0, 1]]
    answer = sum(inputList) // 3

    q = deque()
    q.append(inputList)
    if inputList[0] == inputList[1] == inputList[2]:
        print(1)
        exit(0)

    while q:
        now = q.popleft()
        for case in cases:
            x, y = now[case[0]], now[case[1]]
            z = now[case[2]]
            if x == y:
                continue
            x, y = operate(x, y)
            if x == y and y == z:
                print(1)
                exit(0)
            tempList = [x, y, z]
            tempList.sort()
            if not visited[tempList[0]][tempList[2]]:
                q.append(tempList)
                visited[tempList[0]][tempList[2]] = True

    print(0)
```
-> 처음엔 위와 같이 if not visited[x][y]: 로 조건을 짜지 않고 in 메서드를 활용하여 코드를 짰는데 시간 초과가 났었다. in 메서드를 사용하면서 매번 완전 탐색을 하면서 시간이 오래걸린 것 같다.

### 벽 부수고 이동하기
```
import copy
import sys
from collections import deque
INF = int(1e9)

def bfs(graph):
    visited = [[False] * m for _ in range(n)]
    visited[0][0] = True
    q = deque()
    q.append([0, 0, 1, False, visited])
    while q:
        now = q.popleft()
        x, y, nowCnt, nowWallFlag, nowVisited = now[0], now[1], now[2], now[3], now[4]
        if x == n - 1 and y == m - 1:
            return nowCnt
        for i in range(4):
            nx = x + dx[i]
            ny = y + dy[i]
            if nx == n - 1 and ny == m - 1:
                return nowCnt + 1
            if nx >= 0 and nx < n and ny >= 0 and ny < m and not nowVisited[nx][ny]:
                if graph[nx][ny] == 1:
                    if not nowWallFlag:
                        tempVisited = copy.deepcopy(nowVisited)
                        tempVisited[nx][ny] = True
                        q.append([nx, ny, nowCnt + 1, True, tempVisited])
                    else:
                        continue
                # 빈 칸으로 이동시
                else:
                    if nowWallFlag == False:
                        shortestPath[nx][ny] = min(shortestPath[nx][ny], nowCnt + 1)
                        tempVisited = copy.deepcopy(nowVisited)
                        tempVisited[nx][ny] = True
                        q.append([nx, ny, nowCnt + 1, nowWallFlag, tempVisited])
                    # 벽을 한 번 깻을 경우
                    else:
                        if shortestPath[nx][ny] < nowCnt + 1:
                            continue
                        if shortestPathBreak[nx][ny] > nowCnt + 1:
                            shortestPathBreak[nx][ny] = nowCnt + 1
                            tempVisited = copy.deepcopy(nowVisited)
                            tempVisited[nx][ny] = True
                            q.append([nx, ny, nowCnt + 1, nowWallFlag, tempVisited])
                        else:
                            continue

    return INF


n, m = map(int, sys.stdin.readline().split())
graph = []
for i in range(n):
    tempStr = sys.stdin.readline()
    tempList = []
    for j in range(m):
        tempList.append(int(tempStr[j]))
    graph.append(tempList)

dx = [1, 0, -1, 0]
dy = [0, 1, 0, -1]
shortestPath = [[INF] * m for _ in range(n)]
shortestPathBreak = [[INF] * m for _ in range(n)]

ans = bfs(graph)

if ans == INF:
    print(-1)
else:
    print(ans)
```
-> 시간 초과가 난다.

### 벽 부수고 이동하기(모범 답안)
```
from collections import deque

n, m = map(int, input().split())
graph = []

# 3차원 행렬을 통해 벽의 파괴를 파악함. visited[x][y][0]은 벽 파괴 가능. [x][y][1]은 불가능.
visited = [[[0] * 2 for _ in range(m)] for _ in range(n)]
visited[0][0][0] = 1

for i in range(n):
    graph.append(list(map(int, input())))

# 상하좌우
dx = [0, 0, 1, -1]
dy = [1, -1, 0, 0]


def bfs(x, y, z):
    queue = deque()
    queue.append((x, y, z))

    while queue:
        a, b, c = queue.popleft()
        # 끝 점에 도달하면 이동 횟수를 출력
        if a == n - 1 and b == m - 1:
            return visited[a][b][c]
        for i in range(4):
            nx = a + dx[i]
            ny = b + dy[i]
            if nx < 0 or nx >= n or ny < 0 or ny >= m:
                continue
            # 다음 이동할 곳이 벽이고, 벽파괴기회를 사용하지 않은 경우
            if graph[nx][ny] == 1 and c == 0 :
                visited[nx][ny][1] = visited[a][b][0] + 1
                queue.append((nx, ny, 1))
            # 다음 이동할 곳이 벽이 아니고, 아직 한 번도 방문하지 않은 곳이면
            elif graph[nx][ny] == 0 and visited[nx][ny][c] == 0:
                visited[nx][ny][c] = visited[a][b][c] + 1
                queue.append((nx, ny, c))
    return -1


print(bfs(0, 0, 0))
```
-> 내 코드랑 전체적인 아이디어는 같은데 어느 부분에서 시간 효율을 가르는지 분석해봐야겠다.

### 벽 부수고 이동하기4
```
import sys
from collections import deque

def bfs(start, groupIndex):
    global visited, group
    q = deque()
    q.append(start)
    num = 0
    history = []
    visited[start[0]][start[1]] = 1
    while q:
        now = q.popleft()
        num += 1
        history.append(now)
        x, y = now[0], now[1]
        for i in range(4):
            nx = x + dx[i]
            ny = y + dy[i]
            if nx >= 0 and nx < n and ny >=0 and ny < m and graph[nx][ny] == 0 and visited[nx][ny] == 0:
                q.append([nx, ny])
                visited[nx][ny] = num + 1

    for elem in history:
        visited[elem[0]][elem[1]] = num
        group[elem[0]][elem[1]] = groupIndex


n, m = map(int, sys.stdin.readline().split())

graph = []
for i in range(n):
    tempStr = sys.stdin.readline().rstrip()
    tempList = []
    for j in range(len(tempStr)):
        tempList.append(int(tempStr[j]))
    graph.append(tempList)

dx = [1, 0, -1, 0]
dy = [0, 1, 0, -1]
visited = [[0] * m for _ in range(n)]
group = [[0] * m for _ in range(n)]
groupIndex = 1

for i in range(n):
    for j in range(m):
        if graph[i][j] == 0 and visited[i][j] == 0:
            start = [i, j]
            bfs(start, groupIndex)
            groupIndex += 1

for i in range(n):
    for j in range(m):
        if graph[i][j] == 0:
            print(0, end='')
        else:
            num = 1
            checked = []
            for x in range(4):
                ni = i + dx[x]
                nj = j + dy[x]
                if ni >= 0 and ni < n and nj >= 0 and nj < m and group[ni][nj] not in checked:
                    num += visited[ni][nj]
                    checked.append(group[ni][nj])
            print(num % 10, end='')
    print()
```
-> 처음엔 모든 벽에 대한 bfs를 수행해 해결하려 했지만 시간초과가 나서 위와 같이 bfs는 한 번 수행하여 상하좌우에 몇 개의 빈칸이 있는지 계산하는 방식으로 해결하였다.

### 벽 부수고 이동하기2
```
import sys
from collections import deque
INF = int(1e9)

def bfs(visited):
    q = deque()
    q.append([[0, 0], 0, 1])
    visited[0][0][0] = 1
    while q:
        now = q.popleft()
        nowPos, nowBreak, nowCnt = now[0], now[1], now[2]
        x, y = nowPos[0], nowPos[1]
        for i in range(4):
            nx = x + dx[i]
            ny = y + dy[i]
            if nx >= 0 and nx < n and ny >= 0 and ny < m:
                if graph[nx][ny] == 1:
                    if nowBreak + 1 > k:
                        continue
                    else:
                        if visited[nx][ny][nowBreak + 1] > nowCnt + 1:
                            visited[nx][ny][nowBreak + 1] = nowCnt + 1
                            q.append([[nx, ny], nowBreak + 1, nowCnt + 1])
                else:
                    if visited[nx][ny][nowBreak] > nowCnt + 1:
                        visited[nx][ny][nowBreak] = nowCnt + 1
                        q.append([[nx, ny], nowBreak, nowCnt + 1])
    return min(visited[n - 1][m - 1])

n, m, k = map(int, sys.stdin.readline().split())
graph = []
for _ in range(n):
    tempStr = sys.stdin.readline().rstrip()
    tempList = []
    for j in range(len(tempStr)):
        tempList.append(int(tempStr[j]))
    graph.append(tempList)
visited = [[[INF for _ in range(k + 1)] for _ in range(m)] for _ in range(n)]
dx = [1, 0, -1, 0]
dy = [0, 1, 0, -1]

ans = bfs(visited)

if ans == INF:
    print(-1)
else:
    print(ans)
```

### 벽 부수고 이동하기2(모범답안)
```
# 힙큐를 쓰면, 3차원 visited가 필요할까?
# 방문 여부는 확인해야 하니까 필요는 하겠다
# 그럼 2차원으로 만들어서 level을 표시하고
# 해당 레벨보다 낮으면 등록되게?
# 이게 맞는듯 ㅇㅇ

from sys import stdin;

input = stdin.readline


def BFS(N, M, K, field):
    visited = [[K + 1] * M for _ in range(N)]
    dr = [(0, 1), (1, 0), (-1, 0), (0, -1)]
    visited[0][0] = 0
    que = [(0, 0,)]
    turn = 1
    while que:
        new_que = []

        for x, y in que:
            if x == N - 1 and y == M - 1:
                return turn

            for dx, dy in dr:
                nx, ny = x + dx, y + dy

                if nx >= N or nx < 0 or ny >= M or ny < 0:
                    continue

                nw = int(field[nx][ny]) + visited[x][y]

                if nw < visited[nx][ny]:
                    visited[nx][ny] = nw
                    new_que.append((nx, ny))

        que = new_que
        turn += 1

    return -1


N, M, K = map(int, input().split())
field = [input().rstrip() for _ in range(N)]

print(BFS(N, M, K, field))
```
-> 위 코드는 3차원 리스트를 활용한 나의 코드와 다르게 2차원 리스트로도 해결하였고, 흥미로운 점은 매 턴마다 큐를 새로 업데이트 한 것이다. 이 방법을 사용하면 더욱 빠르게 본 문제를 풀 수 있다.

### 벽 부수고 이동하기3
```
from collections import deque
import sys
input = sys.stdin.readline

def bfs(start):
    q = deque()
    q.append(start)
    ans = 1
    time = True
    while q:
        for _ in range(len(q)):
            i, j, w = q.popleft()
            
            if i == n - 1 and j == m - 1:
                print(ans)
                return
            
            for dy, dx in dir:
                ni, nj = i + dy, j + dx
                if ni < 0 or ni >= n or nj < 0 or nj >= m or visited[ni][nj] <= w:
                    continue
                #벽이 아닌 경우 낮이든 밤이든 이동 가능
                if graph[ni][nj] == '0':
                        q.append((ni, nj, w))
                        visited[ni][nj] = w
                #벽인 경우
                elif w < k:
                    if not time: #밤 인 경우
                        q.append((i, j, w))
                    else:
                        visited[ni][nj] = w
                        q.append((ni, nj, w + 1))
        ans += 1
        time = not time
    print(-1)
    return

n, m, k = map(int, input().split())
graph = [input().rstrip() for _ in range(n)]
visited = [[k + 1 for _ in range(m)] for _ in range(n)]
visited[0][0] = 0

dir = ((1, 0), (-1, 0), (0, 1), (0, -1))

bfs((0,0,0))
```
-> 나중에 다시 풀어보면 좋을거 같다

### 움직이는 미로 탈출
```
import sys
from collections import deque

def moveBoard(board):
    for i in range(7, 0, -1):
        for j in range(8):
            board[i][j] = board[i - 1][j]
    for i in range(8):
        board[0][i] = '.'
    return board

def bfs(board):
    pos = [7, 0]
    q = deque()
    q.append(pos)
    for k in range(8):
        if k == 6:
            print('', end='')
        newQ = deque()
        for elem in q:
            x, y = elem[0], elem[1]
            for i in range(9):
                nx = x + dx[i]
                ny = y + dy[i]
                if nx < 0 or nx >= 8 or ny < 0 or ny >= 8:
                    continue
                if board[nx][ny] == '.' and [nx, ny] not in newQ:
                    newQ.append([nx, ny])

        board = moveBoard(board)
        for i in range(8):
            for j in range(8):
                if board[i][j] == '#' and [i, j] in newQ:
                    newQ.remove([i, j])
        if len(newQ) == 0:
            return 0
        q = newQ
    return 1


board = []
for _ in range(8):
    tempStr = sys.stdin.readline()
    tempList = []
    for i in range(8):
        tempList.append(tempStr[i])
    board.append(tempList)

dx = [-1, -1, 0, 1, 1, 1, 0, -1, 0]
dy = [0, -1, -1, -1, 0, 1, 1, 1, 0]

flag = bfs(board)
print(flag)
```

### 아기 상어
```
import sys
from collections import deque

def findFish():
    global graph, stomach, size, sharkPos
    q = deque()
    firstItem = [sharkPos[0], sharkPos[1], 0]
    q.append(firstItem)
    visited = [[False] * n for _ in range(n)]
    visited[sharkPos[0]][sharkPos[1]] = True
    eatable = []
    while q:
        newQ = deque()
        for now in q:
            x, y, cnt = now[0], now[1], now[2]
            for i in range(4):
                nx = x + dx[i]
                ny = y + dy[i]
                if nx < 0 or nx >= n or ny < 0 or ny >= n or graph[nx][ny] > size or visited[nx][ny]:
                    continue
                if graph[nx][ny] == 0 or graph[nx][ny] == size:
                    newQ.append([nx, ny, cnt + 1])
                    visited[nx][ny] = True
                elif graph[nx][ny] < size:
                    eatable.append([nx, ny, cnt + 1])
                    visited[nx][ny] = True
        q = newQ
        if len(eatable) > 0:
            break
    if len(eatable) == 0:
        return None
    else:
        eatable.sort(key=lambda x:(x[0], x[1]))
        return eatable[0]


n = int(sys.stdin.readline())
graph = []
sharkPos = [-1, -1]
size = 2
stomach = 0
time = 0
dx = [1, 0, -1, 0]
dy = [0, 1, 0, -1]

for i in range(n):
    tempList = list(map(int, sys.stdin.readline().split()))
    for j in range(n):
        if tempList[j] == 9:
            sharkPos = [i, j]
    graph.append(tempList)
graph[sharkPos[0]][sharkPos[1]] = 0

nextFish = findFish()
while nextFish != None:
    nx, ny, dt = nextFish[0], nextFish[1], nextFish[2]
    graph[nx][ny] = 0
    stomach += 1
    if stomach == size:
        size += 1
        stomach = 0
    time += dt
    sharkPos = [nx, ny]
    nextFish = findFish()

print(time)
```
-> 3달 전에 3시간 넘게 풀다 결국 못풀어서 답안 코드를 본 문제, 이번에는 40분 만에 쉽게 풀어냈다😎

### 레이저 통신
```
import sys
from collections import deque
INF = int(1e9)

def bfs():
    global visited, start
    q = deque()
    q.append([start[0], start[1], -1, 0])
    visited[start[0]][start[1]] = 0
    while q:
        now = q.popleft()
        x, y, direction, cnt = now[0], now[1], now[2], now[3]
        for i in range(4):
            nx = x + dx[i]
            ny = y + dy[i]
            if nx < 0 or nx >= w or ny < 0 or ny >= h or cnt > visited[nx][ny] or graph[nx][ny] == '*':
                continue
            if direction == -1 or direction == i:
                if [nx, ny, i, cnt] not in q:
                    q.append([nx, ny, i, cnt])
                    visited[nx][ny] = cnt
            else:
                if cnt + 1 <= visited[nx][ny] and [nx, ny, i, cnt + 1] not in q:
                    q.append([nx, ny, i, cnt + 1])
                    visited[nx][ny] = cnt + 1


h, w = map(int, sys.stdin.readline().split())
graph = []
start = []
end = []
flag = True
dx = [1, 0, -1, 0]
dy = [0, 1, 0, -1]
visited = [[INF] * h for _ in range(w)]
for i in range(w):
    tempStr = sys.stdin.readline()
    tempList = []
    for j in range(h):
        tempList.append(tempStr[j])
        if tempStr[j] == 'C':
            if flag:
                start = [i, j]
                flag = False
            else:
                end = [i, j]
    graph.append(tempList)


bfs()

print(visited[end[0]][end[1]])
```

### 소수경로
```
import sys
from collections import deque

def bfs(x, y):
    q = deque()
    q.append([x, 0])
    visited = [False] * 10000
    visited[x] = True
    while q:
        now = q.popleft()
        x, cnt = now[0], now[1]
        if x == y:
            return cnt
        nowStr = str(x)
        for i in range(4):
            for j in range(10):
                tempStr = nowStr[:i] + str(j) + nowStr[i + 1:]
                tempNum = int(tempStr)
                if not visited[tempNum] and isPrimeNum[tempNum] and tempNum > 1000:
                    visited[tempNum] = True
                    q.append([tempNum, cnt + 1])
    return None


t = int(sys.stdin.readline().rstrip())
cases = []
for _ in range(t):
    x, y = map(int, sys.stdin.readline().rstrip().split())
    cases.append([x, y])

isPrimeNum = [True] * 100000
isPrimeNum[0], isPrimeNum[1] = False, False

for i in range(100):
    if isPrimeNum[i] == True:
        for j in range(2 * i, 10000, i):
            isPrimeNum[j] = False

for i in range(t):
    case = cases[i]
    x, y = case[0], case[1]
    result = bfs(x, y)
    print(result if result != None else "Impossible")
```
-> 숫자형과 문자형을 바꿔줄 일이 있을 때에는 우선 숫자형으로 받아두자

### 적록색약
```
import sys
from collections import deque

def countNormal(x, y, colorNum):
    q = deque()
    q.append([x, y])
    thisColor = graph[x][y]
    visited[x][y] = colorNum
    while q:
        now = q.popleft()
        x, y = now[0], now[1]
        for i in range(4):
            nx = x + dx[i]
            ny = y + dy[i]
            if nx < 0 or nx >= n or ny < 0 or ny >= n or graph[nx][ny] != thisColor or visited[nx][ny] != -1:
                continue
            q.append([nx, ny])
            visited[nx][ny] = colorNum

def countAbnormal(x, y, abColorNum):
    q = deque()
    q.append([x, y])
    thisColor = graph[x][y]
    abVisited[x][y] = colorNum
    while q:
        now = q.popleft()
        x, y = now[0], now[1]
        for i in range(4):
            nx = x + dx[i]
            ny = y + dy[i]
            if nx < 0 or nx >= n or ny < 0 or ny >= n or abVisited[nx][ny] != -1:
                continue
            if thisColor == 'R' or thisColor == 'G':
                if graph[nx][ny] == 'R' or graph[nx][ny] == 'G':
                    q.append([nx, ny])
                    abVisited[nx][ny] = abColorNum
            else:
                if graph[nx][ny] == thisColor:
                    q.append([nx, ny])
                    abVisited[nx][ny] = abColorNum


n = int(sys.stdin.readline().rstrip())
graph = []
for _ in range(n):
    tempStr = sys.stdin.readline().rstrip()
    graph.append(tempStr)

dx = [1, 0, -1, 0]
dy = [0, 1, 0, -1]

visited = [[-1] * n for _ in range(n)]
colorNum = 0
abVisited = [[-1] * n for _ in range(n)]
abColorNum = 0

for i in range(n):
    for j in range(n):
        if visited[i][j] == -1:
            countNormal(i, j, colorNum)
            colorNum += 1
        if abVisited[i][j] == -1:
            countAbnormal(i, j, abColorNum)
            abColorNum += 1

print(colorNum, end=' ')
print(abColorNum)
```

### 적록색약(모범답안)
```
N = int(input())
arr = [input() for _ in range(N)]
drc = [(0, -1), (0, 1), (1, 0), (-1, 0)]


def solution(diff_colors):
    visited = [[0] * N for _ in range(N)]
    cnt = 0
    for r in range(N):
        for c in range(N):
            if visited[r][c]:
                continue
            cnt += 1
            visited[r][c] = 1
            color = arr[r][c]
            stack = [(r, c)]
            while stack:
                _r, _c = stack.pop()
                for dr, dc in drc:
                    nr, nc = _r + dr, _c + dc
                    if not (0 <= nr < N and 0 <= nc < N) or visited[nr][nc] or arr[nr][nc] in diff_colors[color]:
                        continue

                    visited[nr][nc] = 1
                    stack.append((nr, nc))

    return cnt


diff_colors1 = {"R": set(("G", "B")), "G": set(("R", "B")), "B": set(("R", "G"))}
diff_colors2 = {"R": set(("B")), "G": set(("B")), "B": set(("R", "G"))}

print(solution(diff_colors1), solution(diff_colors2))
```
-> 딕셔너리 자료구조를 활용하여 함수를 2개 사용한 나의 코드와 다르게 함수를 1개만 사용하였으며 수행 시간 또한 4배 가까이 절약한 코드다.
