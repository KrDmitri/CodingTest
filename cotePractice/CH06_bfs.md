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
