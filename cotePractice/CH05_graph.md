# 그래프 알고리즘
### Two Dots
```
def nextDirection(direction):
    return (direction + 1) % 4

def searchCycle(start, now, direction):
    x, y = now[0], now[1]
    nextX = x + dx[direction]
    nextY = y + dy[direction]
    if nextX >= 0 and nextX < n and nextY >= 0 and nextY < m:
        next = [nextX, nextY]
        if board[start[0]][start[1]] == board[nextX][nextY]:
            if next == start:
                return True
            flag = searchCycle(start, next, direction)
            if flag:
                return True

    if direction != 3:
        direction = nextDirection(direction)
        nextX = x + dx[direction]
        nextY = y + dy[direction]
        if nextX >= 0 and nextX < n and nextY >= 0 and nextY < m:
            next = [nextX, nextY]
            if board[start[0]][start[1]] == board[nextX][nextY]:
                if next == start:
                    return True
                flag = searchCycle(start, next, direction)
                if flag:
                    return True
    # 아래 코드 맞나?
    return False


n, m = map(int, input().split())
board = []
for _ in range(n):
    board.append(input())

dx = [1, 0, -1, 0]
dy = [0, 1, 0, -1]
flag = 0

for i in range(n):
    for j in range(m):
        flag = searchCycle([i, j], [i, j], 0)
        if flag:
            break
    if flag:
        break

if flag:
    print('Yes')
else:
    print('No')
```
-> 위 답은 틀린 답인데, 문제를 잘못 이해했다. 나는 문제에서 말하는 싸이클이 꼭 직사각형이어야하는 줄 알았는데 그렇지 않았다..😤

### Two Dots (두번째 시도)
```
def searchCycle(start, now, history):
    x = now[0]
    y = now[1]
    flag = False

    for i in range(4):
        nx = x + dx[i]
        ny = y + dy[i]
        next = [nx, ny]
        if next == start and len(history) >= 4:
            flag = True
            break
        if nx >= 0 and nx < n and ny >= 0 and ny < m and [nx, ny] not in history and board[start[0]][start[1]] == board[nx][ny]:
            history.append(next)
            flag = searchCycle(start, next, history)
            if flag:
                break
            history.remove(next)
    if flag:
        return True
    else:
        return False

n, m = map(int, input().split())
board = []
dx = [1, 0, -1, 0]
dy = [0, 1, 0, -1]

for _ in range(n):
    board.append(input())

for i in range(n):
    for j in range(m):
        flag = searchCycle([i, j], [i, j], [[i, j]])
        if flag:
            break
    if flag:
        break

if flag:
    print('Yes')
else:
    print('No')
```
-> 알고보니 더 쉬운 문제였다

### 서울 지하철 2호선
```
import collections

def findSpanningTree(start, now, history):
    global spanningTree
    if len(history) == 0:
        for i in range(n):
            if graph[start][i] == 1:
                history.append(i)
                next = i
                findSpanningTree(start, next, history)
                history.remove(i)
    else:
        for i in range(n):
            if len(history) >= 3 and graph[now][start] == 1 and set(history) not in spanningTree:
                spanningTree.append(set(history))
            if i not in history and graph[now][i] == 1:
                history.append(i)
                next = i
                findSpanningTree(start, next, history)
                history.remove(i)

def distToSpanningTree(elem, visited):
    global spanningTree
    q = collections.deque([elem])
    while q:
        now = q.popleft()
        nowIndex = now[0]
        nowDist = now[1]
        for i in range(n):
            if graph[nowIndex][i] == 1 and i not in visited:
                if i in spanningTree:
                    return nowDist + 1
                q.append([i, nowDist + 1])
                visited.append(i)

n = int(input())

graph = [[0] * n for _ in range(n)]
spanningTree = []

for _ in range(n):
    x, y = map(int, input().split())
    graph[x - 1][y - 1] = 1
    graph[y - 1][x - 1] = 1

for i in range(n):
    findSpanningTree(i, i, [i])
    # 순환이 두 개 이상일 경우 아래 코드는 무효할듯?
    if len(spanningTree) != 0:
        break

spanningTree = list(spanningTree.pop())

# print(spanningTree)

for i in range(n):
    if i in spanningTree:
        print(0, end=' ')
    else:
        print(distToSpanningTree([i, 0], [i]), end=' ')
```
-> 싸이클 찾는 것은 dfs, 최소 거리 구하는 것은 bfs를 사용하는 방법으로 다른 사람들의 모범답안과 비슷한데 내 코드는 시간 초과가 난다

### 서울 지하철 2호선 (2번째 시도)
```
import collections

def findSpanningTree(start, now, cnt):
    global isCycle, visited
    if cnt >= 2 and graph[now][start] == 1:
        if start == 3:
            print('', end='')
        isCycle[start] = True
        return

    for i in range(n):
        if graph[now][i] == 1 and not visited[i]:
            visited[i] = True
            findSpanningTree(start, i, cnt + 1)
            visited[i] = False


def distToSpanningTree():
    global spanningTree, distance
    q = collections.deque()
    for node in spanningTree:
        q.append([node, 0])

    while q:
        now = q.popleft()
        nowNode = now[0]
        nowDist = now[1]
        for i in range(n):
            if graph[nowNode][i] == 1 and i not in spanningTree and distance[i] == -1:
                distance[i] = nowDist + 1
                q.append([i, nowDist + 1])

n = int(input())

graph = [[0] * n for _ in range(n)]
isCycle = [False] * n

for _ in range(n):
    x, y = map(int, input().split())
    graph[x - 1][y - 1] = 1
    graph[y - 1][x - 1] = 1

for i in range(n):
    visited = [False] * n
    visited[i] = True
    findSpanningTree(i, i, 0)

# 위에서 isCyle 구하고, 기반으로 cycleTree 리스트 만들어주기
spanningTree = []
for i in range(len(isCycle)):
    if isCycle[i]:
        spanningTree.append(i)

distance = [-1] * n

for elem in spanningTree:
    distance[elem] = 0

distToSpanningTree()

for elem in distance:
    print(elem, end=' ')
```
-> 다른 모범 답안을 참고하여 푼 코드인데 이 코드도 시간 초과가 난다. 모범 답안 코드도 메모리 초과가 나던데 우선 원리는 이해했으므로 나중에 다시 풀어봐야겠다.
