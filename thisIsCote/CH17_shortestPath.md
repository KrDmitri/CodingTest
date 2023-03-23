## 최단 경로 문제
### 플로이드
```
INF = 1e9
def floyd():
    n = int(input())
    m = int(input())

    routes = [[INF] * (n + 1) for _ in range(n + 1)]

    for i in range(m):
        a, b, c = map(int, input().split())
        if routes[a][b] == INF:
            routes[a][b] = c
        else:
            routes[a][b] = min(routes[a][b], c)

    for k in range(1, n + 1):
        for a in range(1, n + 1):
            for b in range(1, n + 1):
                routes[a][b] = min(routes[a][b], routes[a][k] + routes[k][b])

    for a in range(1, n + 1):
        for b in range(1, n + 1):
            if a == b:
                routes[a][b] = 0
            if routes[a][b] == INF:
                routes[a][b] = 0
            if b == n:
                print(routes[a][b])
            else:
                print(routes[a][b], end=' ')
```

### 정확한 순위
```
INF = 1e9
def preciseRank():
    n, m = map(int, input().split())
    distances = [[INF] * (n + 1) for _ in range(n + 1)]
    reverseDistances = [[INF] * (n + 1) for _ in range(n + 1)]

    for _ in range(m):
        a, b = map(int, input().split())
        distances[a][b] = 1
        reverseDistances[b][a] = 1

    for k in range(1, n + 1):
        for a in range(1, n + 1):
            for b in range(1, n + 1):
                distances[a][b] = min(distances[a][b], distances[a][k] + distances[k][b])
                reverseDistances[a][b] = min(reverseDistances[a][b], reverseDistances[a][k] + reverseDistances[k][b])

    count = 0
    for a in range(1, n + 1):
        flag = True
        for b in range(1, n + 1):
            if a == b:
                distances[a][b] = 0
                reverseDistances[a][b] = 0
            if distances[a][b] == INF and reverseDistances[a][b] == INF:
                flag = False
        if flag == True:
            count += 1

    print(count)
```
-> 플로이드를 사용하면 한 방향에 대해 연결이 되어 있는지 알 수 있어서 두 방향으로 플로이드 함수를 사용해서 두 방향 다 연결이 안되어 있는 노드의 수는 제거하는 방식으로 문제 해결함. 플로이드 워셜을 사용해서 시간 초과가 나는거 아닌가 했는데 답지에서도 동일한 방법으로 해결했다. 그런데 나처럼 굳이 reverseDistance처럼 새로운 메모리를 사용하지 않고 distance[a][b] == INF and distance[b][a] == INF 처럼 그냥 인덱스 순서만 바꿔줌

### 화성 탐사
```
import copy

# dfs로 모든 경로의 길이를 다 구한 다음 한 배열에 넣고 가장 작은 값 리턴하는 방법으로 해보기
def exploreMars():
    def goDfs(x, y, distance, visited):
        visited[x][y] = True
        distance += marsMap[x][y]
        if x == n - 1 and y == n - 1:
            distances.append(distance)
            return

        if x != 0 and not visited[x-1][y]:
            tempVisited = copy.deepcopy(visited)
            goDfs(x - 1, y, distance, visited)
            visited = copy.deepcopy(tempVisited)
        if x < n - 1 and not visited[x+1][y]:
            tempVisited = copy.deepcopy(visited)
            goDfs(x + 1, y, distance, visited)
            visited = copy.deepcopy(tempVisited)
        if y != 0 and not visited[x][y-1]:
            tempVisited = copy.deepcopy(visited)
            goDfs(x, y - 1, distance, visited)
            visited = copy.deepcopy(tempVisited)
        if y < n - 1 and not visited[x][y+1]:
            tempVisited = copy.deepcopy(visited)
            goDfs(x, y + 1, distance, visited)
            visited = copy.deepcopy(tempVisited)


    t = int(input())
    for _ in range(t):
        n = int(input())
        marsMap = []
        visitedFrame = [[False] * n for _ in range(n)]

        for _ in range(n):
            marsMap.append(list(map(int, input().split())))

        distances = []
        goDfs(0, 0, 0, visitedFrame)
        print(min(distances))
```
-> 답은 구해지는데 n이 7부터 시간초과가 남, 아마 goDfs()부분 안에 조건을 추가해서 조금 더 빠르게 할 수 있을듯

### 화성 탐사
```
import copy
INF = 1e9
# dfs로 모든 경로의 길이를 다 구한 다음 한 배열에 넣고 가장 작은 값 리턴하는 방법으로 해보기
def exploreMars():
    def goDfs(x, y, distance, visited):
        visited[x][y] = True
        distance += marsMap[x][y]
        if distance > min(distances):
            return

        if x == n - 1 and y == n - 1:
            distances.append(distance)
            return

        if x != 0 and not visited[x-1][y]:
            tempVisited = copy.deepcopy(visited)
            goDfs(x - 1, y, distance, visited)
            visited = copy.deepcopy(tempVisited)
        if x < n - 1 and not visited[x+1][y]:
            tempVisited = copy.deepcopy(visited)
            goDfs(x + 1, y, distance, visited)
            visited = copy.deepcopy(tempVisited)
        if y != 0 and not visited[x][y-1]:
            tempVisited = copy.deepcopy(visited)
            goDfs(x, y - 1, distance, visited)
            visited = copy.deepcopy(tempVisited)
        if y < n - 1 and not visited[x][y+1]:
            tempVisited = copy.deepcopy(visited)
            goDfs(x, y + 1, distance, visited)
            visited = copy.deepcopy(tempVisited)


    t = int(input())
    for _ in range(t):
        n = int(input())
        marsMap = []
        visitedFrame = [[False] * n for _ in range(n)]

        for _ in range(n):
            marsMap.append(list(map(int, input().split())))

        distances = [INF]
        goDfs(0, 0, 0, visitedFrame)
        print(min(distances))
```
-> 이렇게 고치니깐 시간 초과는 해결

### 화성 탐사 해설 답안 코드
```
import heapq
import sys
input = sys.stdin.readline
INF = int(1e9)

dx = [-1, 0, 1, 0]
dy = [0, 1, 0, -1]

for tc in range(int(input())):
    n = int(input())
    graph = []
    for i in range(n):
        graph.append(list(map(int, input().split())))

    distance = [[INF] * n for _ in range(n)]

    x, y = 0, 0
    q = [(graph[x][y], x, y)]
    distance[x][y] = graph[x][y]

    while(q):
        dist, x, y = heapq.heappop(q)
        if distance[x][y] < dist:
            continue
        for i in range(4):
            nx = x + dx[i]
            ny = y + dy[i]
            if nx < 0 or nx >= n or ny < 0 or ny >= n:
                continue
            cost = dist + graph[nx][ny]
            if cost < distance[nx][ny]:
                distance[nx][ny] = cost
                heapq.heappush(q, (cost, nx, ny))
    print(distance[n - 1][n - 1])
```
-> n의 범위가 125까지라서 내가 짠 코드는 시간 초과가 날 것임. 따라서 답안 코드와 같이 다익스트라 알고리즘을 활용하면 시간 초과 없이 해결 가능

### 숨바꼭질
```
import heapq
INF = int(1e9)

def hidingGame():
    n, m = map(int, input().split())
    routes = [[] for _ in range(n + 1)]
    distances = [INF for _ in range(n + 1)]
    visited= [False for _ in range(n + 1)]
    notVisited = []

    for _ in range(m):
        a, b = map(int, input().split())
        routes[a].append(b)
        routes[b].append(a)

    distances[1] = 0
    visited[0] = True
    visited[1] = True
    heapq.heappush(notVisited, (0, 1))

    while notVisited:
        now = heapq.heappop(notVisited)
        iterLength = len(routes[now[1]])
        for i in range(iterLength):
            nodeNum = routes[now[1]][i]
            if not visited[nodeNum]:
                distances[nodeNum] = distances[now[1]] + 1
                visited[nodeNum] = True
                heapq.heappush(notVisited, (distances[nodeNum], nodeNum))


    distances[0] = 0
    maxDistance = max(distances)
    index = 0
    for i in range(1, n + 1):
        if distances[i] == maxDistance:
            index = i
            break

    numSame = distances.count(distances[index])

    print(index, distances[index], numSame)
```
