# 시뮬레이션과 구현
### 인구 이동
```
import sys
from collections import deque

def checkStop():
    global graph
    for x in range(n):
        for y in range(n):
            for i in range(4):
                nx = x + dx[i]
                ny = y + dy[i]
                if nx < 0 or nx >= n or ny < 0 or ny >= n:
                    continue
                populationDiff = abs(graph[x][y] - graph[nx][ny])
                if populationDiff >= l and populationDiff <= r:
                    return False
    return True



def populationMove(visited, i, j):
    global graph
    q = deque()
    q.append([i, j])
    countryNum, populationSum = 1, graph[i][j]
    countryList = [[i, j]]
    visited[i][j] = True
    while q:
        x, y = q.popleft()
        for i in range(4):
            nx = x + dx[i]
            ny = y + dy[i]
            if nx < 0 or nx >= n or ny < 0 or ny >= n or visited[nx][ny]:
                continue
            populationDiff = abs(graph[x][y] - graph[nx][ny])
            if populationDiff < l or populationDiff > r:
                continue
            visited[nx][ny] = True
            countryNum += 1
            populationSum += graph[nx][ny]
            q.append([nx, ny])
            countryList.append([nx, ny])
    eachPopulation = populationSum // countryNum
    for elem in countryList:
        graph[elem[0]][elem[1]] = eachPopulation
    return visited


n, l, r = map(int, sys.stdin.readline().rstrip().split())
graph = []
for _ in range(n):
    graph.append(list(map(int, sys.stdin.readline().rstrip().split())))
dx = [1, 0, -1, 0]
dy = [0, 1, 0, -1]
day = 0
while True:
    if checkStop():
        print(day)
        break
    visited = [[False] * n for _ in range(n)]
    for i in range(n):
        for j in range(n):
            if not visited[i][j]:
                visited = populationMove(visited, i, j)
    day += 1
```

### 나무 재테크
```
import sys

n, m, k = map(int, sys.stdin.readline().rstrip().split())
graph = [[[5, []] for _ in range(n)] for _ in range(n)]
treeNum = m
plusNutrient = []
dx = [-1, -1, -1, 0, 0, 1, 1, 1]
dy = [-1, 0, 1, -1, 1, -1, 0, 1]
for _ in range(n):
    plusNutrient.append(list(map(int, sys.stdin.readline().rstrip().split())))
for _ in range(m):
    x, y, z = map(int, sys.stdin.readline().rstrip().split())
    graph[x - 1][y - 1][1].append(z)
    graph[x - 1][y - 1][1].sort()

for year in range(k):
    deadTree = []
    # 봄
    for x in range(n):
        for y in range(n):
            if len(graph[x][y][1]) > 0:
                tempDead = []
                for treeIndex in range(len(graph[x][y][1])):
                    if graph[x][y][0] >= graph[x][y][1][treeIndex]:
                        graph[x][y][0] -= graph[x][y][1][treeIndex]
                        graph[x][y][1][treeIndex] += 1
                    else:
                        deadTree.append([x, y, graph[x][y][1][treeIndex]])
                        tempDead.append(graph[x][y][1][treeIndex])
                        treeNum -= 1
                for tree in tempDead:
                    graph[x][y][1].remove(tree)
                graph[x][y][1].sort()
    # 여름
    for tree in deadTree:
        x, y, treeAge = tree[0], tree[1], tree[2]
        nutrient = treeAge // 2
        graph[x][y][0] += nutrient
    # 가을
    for x in range(n):
        for y in range(n):
            if len(graph[x][y][1]) > 0:
                for tree in graph[x][y][1]:
                    if tree % 5 == 0:
                        for direction in range(8):
                            nx = x + dx[direction]
                            ny = y + dy[direction]
                            if nx < 0 or nx >= n or ny < 0 or ny >= n:
                                continue
                            graph[nx][ny][1].append(1)
                            graph[nx][ny][1].sort()
                            treeNum += 1
    # 겨울
    for x in range(n):
        for y in range(n):
            graph[x][y][0] += plusNutrient[x][y]

print(treeNum)
```

### 나무 재테크(모범답안)
```
from collections import deque

n, m, k = map(int, input().split(' '))

a = [list(map(int, input().split(' '))) for _ in range(n)]
graph = [[5] * n for _ in range(n)]
trees = [[deque() for _ in range(n)] for _ in range(n)]
dead_trees = [[list() for _ in range(n)] for _ in range(n)]
for _ in range(m):
    x, y, z = map(int, input().split(' '))
    trees[x - 1][y - 1].append(z)

dx = [-1, -1, 0, 1, 1, 1, 0, -1]
dy = [0, 1, 1, 1, 0, -1, -1, -1]


def spring_summer():
    for i in range(n):
        for j in range(n):
            len_ = len(trees[i][j])
            for k in range(len_):
                if graph[i][j] < trees[i][j][k]:
                    for _ in range(k, len_):
                        dead_trees[i][j].append(trees[i][j].pop())
                    break
                else:
                    graph[i][j] -= trees[i][j][k]
                    trees[i][j][k] += 1

    for i in range(n):
        for j in range(n):
            while dead_trees[i][j]:
                graph[i][j] += dead_trees[i][j].pop() // 2


def fall_winter():
    for i in range(n):
        for j in range(n):
            for k in range(len(trees[i][j])):
                if trees[i][j][k] % 5 == 0:
                    for l in range(8):
                        nx, ny, = i + dx[l], j + dy[l]

                        if nx < 0 or nx >= n or ny < 0 or ny >= n:
                            continue
                        trees[nx][ny].appendleft(1)

            graph[i][j] += a[i][j]


for i in range(k):
    spring_summer()
    fall_winter()

answer = 0
for i in range(n):
    for j in range(n):
        answer += len(trees[i][j])

print(answer)
```
-> 내가 작성한 코드는 시간초과가 난다. 정답 처리 코드와의 가장 큰 차이는 정답 코드는 큐를 사용했다는 것인데, 나는 이에 처음 입력값을 줄 때 각 칸에 나무의 나이를 순서대로 주지 않으면 오답이 나지 않을까 생각이 든다. 또, 나의 코드는 sorting하는 과정에서 시간을 많이 할애하는것 같다.

### 미세먼지 안녕!
```
import sys

def dustMove():
    global graph
    dustSpots = []
    for i in range(r):
        for j in range(c):
            if graph[i][j] > 0:
                dustSpots.append([i, j, graph[i][j]])
    while dustSpots:
        x, y, dust = dustSpots.pop()
        neighbors = 0
        for i in range(4):
            nx = x + ddx[i]
            ny = y + ddy[i]
            if nx < 0 or nx >= r or ny < 0 or ny >= c or graph[nx][ny] == -1:
                continue
            neighbors += 1
            graph[nx][ny] += dust // 5
        graph[x][y] -= (dust // 5) * neighbors

def machineWork(graph):
    x, y = uMachine[0], uMachine[1]
    direction = 0
    while True:
        nx = x + udx[direction]
        ny = y + udy[direction]
        if nx == uMachine[0] and ny == uMachine[1]:
            graph[x][y] = 0
            break
        if nx < 0 or nx > uMachine[0] or ny < 0 or ny >= c:
            direction += 1
            continue
        if nx == uMachine[0] and ny == uMachine[1]:
            graph[x][y] = 0
            break
        if graph[x][y] != -1:
            graph[x][y] = graph[nx][ny]
        x, y = nx, ny
    x, y = dMachine[0], dMachine[1]
    direction = 0
    while True:
        nx = x + ddx[direction]
        ny = y + ddy[direction]
        if nx == dMachine[0] and ny == dMachine[1]:
            graph[x][y] = 0
            break
        if nx >= r or nx < dMachine[0] or ny < 0 or ny >= c:
            direction += 1
            continue
        if nx == dMachine[0] and ny == dMachine[1]:
            graph[x][y] = 0
            break
        if graph[x][y] != -1:
            graph[x][y] = graph[nx][ny]
        x, y = nx, ny



r, c, t = map(int, sys.stdin.readline().rstrip().split())
graph = []
flag = True
uMachine = []
dMachine = []
udx = [-1, 0, 1, 0]
udy = [0, 1, 0, -1]
ddx = [1, 0, -1, 0]
ddy = [0, 1, 0, -1]
for i in range(r):
    tempList = list(map(int, sys.stdin.readline().rstrip().split()))
    for j in range(c):
        if tempList[j] == -1 and flag:
            uMachine = [i, j]
            flag = False
        elif tempList[j] == -1 and not flag:
            dMachine = [i, j]
    graph.append(tempList)

for _ in range(t):
    dustMove()
    machineWork(graph)

answer = 0
for i in range(r):
    for j in range(c):
        if graph[i][j] == -1:
            continue
        answer += graph[i][j]

print(answer)
```

### 낚시왕
```
import sys

def catchShark(j):
    global graph, answer
    for i in range(1, R + 1):
        if len(graph[i][j]) != 0:
            answer += graph[i][j][0][2]
            graph[i][j] = []
            sharks.remove([i, j])
            return

def changeDirection(d):
    if d == 0:
        d = 1
    elif d == 1:
        d = 0
    elif d == 2:
        d = 3
    else:
        d = 2
    return d

R, C, M = map(int, sys.stdin.readline().rstrip().split())
graph = [[[] for _ in range(C + 1)] for _ in range(R + 1)]
sharks = []
for _ in range(M):
    r, c, s, d, z = map(int, sys.stdin.readline().rstrip().split())
    graph[r][c].append([s, d - 1, z])
    sharks.append([r, c])

# 순서: 상, 하, 우, 좌
dx = [-1, 1, 0, 0]
dy = [0, 0, 1, -1]
answer = 0

# 속도, 방향, 크기 순서
for i in range(1, C + 1):
    catchShark(i)
    newSharks = []
    newVisited = [[0] * (C + 1) for _ in range(R + 1)]
    newGraph = [[[] for _ in range(C + 1)] for _ in range(R + 1)]
    needToCheck = []
    for shark in sharks:
        x, y = shark[0], shark[1]
        s, d, z = graph[x][y][0]
        graph[x][y] = []
        for _ in range(s):
            x += dx[d]
            y += dy[d]
            if x <= 0 or x > R or y <= 0 or y > C:
                x -= dx[d]
                y -= dy[d]
                d = changeDirection(d)
                x += dx[d]
                y += dy[d]
        newGraph[x][y].append([s, d, z])
        if newVisited[x][y] == 0:
            newSharks.append([x, y])
            newVisited[x][y] += 1
        elif newVisited[x][y] == 1:
            needToCheck.append([x, y])
            newVisited[x][y] += 1
    # 중복 위치 상어 처리
    for spot in needToCheck:
        x, y = spot[0], spot[1]
        newGraph[x][y].sort(reverse=True, key=lambda x:x[2])
        newGraph[x][y] = [newGraph[x][y][0]]

    sharks = newSharks
    graph = newGraph

print(answer)
```

### 낚시왕(모범답안)
```
import sys

sys.setrecursionlimit(10 ** 8)
input = lambda: sys.stdin.readline().rstrip()


def fish(j):
    for i in range(R):
        if board[i][j]:
            x = board[i][j][2]
            board[i][j] = 0
            return x
    return 0


def move():
    global board  # board[i][j] 안에는 (s,d,z)의 값이 들어있음. 상어가 없는 경우엔 0이 들어있음
    new_board = [[0 for _ in range(C)] for _ in range(R)]  # 상어들의 새 위치를 담을 공간
    for i in range(R):
        for j in range(C):
            if board[i][j]:
                # 이 상어의 다음 위치는
                ni, nj, nd = get_next_loc(i, j, board[i][j][0], board[i][j][1])
                if new_board[ni][nj]:
                    new_board[ni][nj] = max(
                        new_board[ni][nj],
                        (board[i][j][0], nd, board[i][j][2]),
                        key=lambda x: x[2],
                    )
                else:
                    new_board[ni][nj] = (board[i][j][0], nd, board[i][j][2])

    board = new_board  # board가 이제 상어들의 새 위치를 가리키도록


def get_next_loc(i, j, speed, dir):

    if dir == UP or dir == DOWN:  # i
        cycle = R * 2 - 2
        if dir == UP:
            speed += 2 * (R - 1) - i
        else:
            speed += i

        speed %= cycle
        if speed >= R:
            return (2 * R - 2 - speed, j, UP)
        return (speed, j, DOWN)

    else:  # j
        cycle = C * 2 - 2
        if dir == LEFT:
            speed += 2 * (C - 1) - j
        else:
            speed += j

        speed %= cycle
        if speed >= C:
            return (i, 2 * C - 2 - speed, LEFT)
        return (i, speed, RIGHT)


UP, DOWN, RIGHT, LEFT = 1, 2, 3, 4
R, C, M = map(int, input().split())
board = [[0 for _ in range(C)] for _ in range(R)]

for i in range(M):
    r, c, s, d, z = map(int, input().split())
    r, c = r - 1, c - 1
    board[r][c] = (s, d, z)
    # s : speed
    # d : 1(up), 2(down), 3(right), 4(left)
    # z : size


ans = 0
for j in range(C):
    ans += fish(j)
    move()

print(ans)
```
-> 나는 본 문제를 풀 때 graph 전체를 순회하면 그만큼 시간이 많이 소모될 것이라 생각이 되어 sharks라는 리스트를 사용하여 상어들이 있는 공간만 탐색하도록 했다. 그래도 시간 소모가 컸는데, 위 모범 답안 코드를 보면 완전 탐색을 했지만 각 상어들이 이동할 때 반복문을 돌며 한 칸씩 이동하게 한 나의 코드와 달리 수학적인 계산을 통해 상어의 다음 이동 위치를 바로 구한 위의 코드가 시간 소모가 더 적은 것을 볼 수 있었다.
