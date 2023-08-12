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
