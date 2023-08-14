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
