## Chapter 13 DFS/BFS 문제
### 특정 거리의 도시 찾기
```
from collections import deque
INF = 1e9

def findCity():
    n, m, k, x = map(int, input().split())
    roads = []
    visited = [False] * (n + 1)
    distance = [INF] * (n + 1)

    for _ in range(m):
        a, b = map(int, input().split())
        roads.append((a, b))
    q = deque([])
    q.append(x)
    visited[x] = True
    distance[x] = 0
    while q:
        curCity = q.popleft()
        for road in roads:
            if road[0] == curCity:
                if not visited[road[1]]:
                    q.append(road[1])
                    visited[road[1]] = True
                    distance[road[1]] = distance[curCity] + 1

    for i in range(len(distance)):
        if distance[i] == k:
            print(i)

    if k not in distance:
        print(-1)
```
-> 값은 맞는데 백준에 코드 제출시 시간 초과로 뜸, 아마 도로의 개수 M이 1,000,000개라서 이것들을 하나하나 다 저장해서 초과 나는듯, 1 -> (2, 3) 이런 느낌으로 코드 수정해봄

```
def findCity2():
    n, m, k, x = map(int, input().split())
    roads = [[] for _ in range(n + 1)]
    visited = [False] * (n + 1)
    distance = [INF] * (n + 1)

    for _ in range(m):
        a, b = map(int, input().split())
        roads[a].append(b)

    q = deque([])
    q.append(x)
    visited[x] = True
    distance[x] = 0
    while q:
        curCity = q.popleft()
        road = roads[curCity]
        for i in road:
            if not visited[i]:
                q.append(i)
                visited[i] = True
                distance[i] = distance[curCity] + 1

    for i in range(len(distance)):
        if distance[i] == k:
            print(i)

    if k not in distance:
        print(-1)
```
-> 그래도 시간 초과남..ㅎㅎ

```
import sys
from collections import deque
input = sys.stdin.readline
INF = int(1e7)


n, m, k, x = map(int, input().split())
cities = [[] for _ in range(n+1)]
d = [INF for _ in range(n+1)]
d[x] = 0
for _ in range(m):
    i, c = map(int, input().split())
    cities[i].append(c)
q = deque([x])

while q:
    i = q.popleft()
    if d[i] >= k:
        break
    for city in cities[i]:
        if d[city] == INF:
            d[city] = d[i]+1
            q.append(city)

no = True
for i in range(1, n+1):
    if d[i] == k:
        no = False
        print(i)

if no:
    print(-1)
```
-> 답안 코드도 내 코드랑 비슷한데, 위 코드처럼 현재 탐색중인 노드가 k 이상일 때는 그 다음 노드까지의 거리가 항상 k보다 크기 때문에 탐색할 필요가 없기 때문에, 이같은 방식으로 시간을 절약할 수 있음

### 연구소
```
from itertools import combinations
import copy

def laboratory():
    def spreadVirus(tempGraph, tempVisited, vx, vy):
        if tempGraph[vx][vy] != 1:
            tempGraph[vx][vy] = 2
            tempVisited[vx][vy] = True
            if vx - 1 >= 0 and tempVisited[vx-1][vy] == False and tempGraph[vx-1][vy] != 1:
                spreadVirus(tempGraph, tempVisited, vx - 1, vy)
            if vx + 1 < n and tempVisited[vx + 1][vy] == False and tempGraph[vx+1][vy] != 1:
                spreadVirus(tempGraph, tempVisited, vx + 1, vy)
            if vy - 1 >= 0 and tempVisited[vx][vy-1] == False and tempGraph[vx][vy-1] != 1:
                spreadVirus(tempGraph, tempVisited, vx, vy - 1)
            if vy + 1 < m and tempVisited[vx][vy+1] == False and tempGraph[vx][vy+1] != 1:
                spreadVirus(tempGraph, tempVisited, vx, vy + 1)
        return


    n, m = map(int, input().split())
    graph = []
    virus = []
    blank = []
    visited = [[False] * m for _ in range(n)]

    for i in range(n):
        row = list(map(int, input().split()))
        graph.append(row)
        for j in range(len(row)):
            if row[j] == 2:
                virus.append((i, j))
            elif row[j] == 0:
                blank.append((i, j))
            elif row[j] == 1:
                visited[i][j] = True

    protectWalls = combinations(blank, 3)

    safeArea = 0
    for protectWall in protectWalls:
        tempGraph = copy.deepcopy(graph)
        tempVisited = copy.deepcopy(visited)
        for wall in protectWall:
            tempGraph[wall[0]][wall[1]] = 1

        for eachVirus in virus:
            flag = False
            for i in range(n):
                for j in range(m):
                    if tempGraph[i][j] == 0:
                        flag = True

            if flag:
                spreadVirus(tempGraph, tempVisited, eachVirus[0], eachVirus[1])

        tempSafe = 0
        for i in range(n):
            for j in range(m):
                if tempGraph[i][j] == 0:
                    tempSafe += 1

        safeArea = max(safeArea, tempSafe)

    print(safeArea)
```
-> 맞긴 맞았는데, 소요시간이 다른 사람보다 평균 3-4배 더 걸림

### 연구소 해설 답안 코드
```

