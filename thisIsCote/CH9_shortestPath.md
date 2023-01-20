## Chapter 09 최단 경로
### 간단한 다익스트라 알고리즘 소스코드
```
INF = int(1e9)

# 노드, 간선 개수 입력
n, m = map(int, input().split())

start = int(input())

graph = [[] for i in range(n + 1)]
visited = [False] * (n + 1)
distance = [INF] * (n + 1)

for _ in range(m):
    a, b, c = map(int, input().split())
    graph[a].append((b, c))

def get_smallest_node():
    min_value = INF
    index = 0
    for i in range(1, n + 1):
        if distance[i] < min_value and not visited[i]:
            min_value = distance[i]
            index = i
    return index

def dijkstra(start):
    distance[start] = 0
    visited[start] = True
    # 여기서 j[0]은 노드, j[1]은 시작 노드에서 해당 노드까지의 거리
    for j in graph[start]:
        distance[j[0]] = j[1]
    for i in range(n - 1):
        now = get_smallest_node()
        visited[now] = True
        # 여기서 now는 현재 스캔 중인 노드, j는 스캔 중인 노드의 인접 노드들의 정보를 가지는 튜플
        for j in graph[now]:
            cost = distance[now] + j[1]
            if cost < distance[j[0]]:
                distance[j[0]] = cost

dijkstra(start)

for i in range(1, n + 1):
    if distance[i] == INF:
        print("INFINITY")
    else:
        print(distance[i])
```

### 개선된 다익스트라 알고리즘 소스코드(우선순위 큐 사용)
```
import heapq
INF = int(1e9)

n, m = map(int, input().split())
start = int(input())

graph = [[] for _ in range(n + 1)]
distance = [INF] * (n + 1)

for _ in range(m):
    a, b, c = map(int, input().split())
    graph[a].append((b, c))

def dijkstra(start):
    q = []
    heapq.heappush(q, (0, start))
    distance[start] = 0
    while q:
        dist, now = heapq.heappop(q)
        if distance[now] < dist:
            continue
        for i in graph[now]:
            cost = dist + i[1]
            if cost < distance[i[0]]:
                distance[i[0]] = cost
                heapq.heappush(q, (cost, i[0]))

dijkstra(start)

for i in range(1, n + 1):
    if distance[i] == INF:
        print("INFINITY")
    else:
        print(distance[i])
```

### 플로이드 워셜 알고리즘 소스 코드
```
INF = int(1e9)

n = int(input())
m = int(input())

graph = [[INF] * (n + 1) for _ in range(n + 1)]

for a in range(1, n + 1):
    for b in range(1, n + 1):
        if a == b:
            graph[a][b] = 0

for _ in range(m):
    a, b, c = map(int, input().split())
    graph[a][b] = c

# 점화식에 따라 플로이드 워셜 알고리즘 수행
for k in range(1, n + 1):
    for a in range(1, n + 1):
        for b in range(1, n + 1):
            graph[a][b] = min(graph[a][b], graph[a][k] + graph[k][b])

for a in range(1, n + 1):
    for b in range(1, n + 1):
        if graph[a][b] == INF:
            print("INFINITY", end=' ')
        else:
            print(graph[a][b], end=' ')
    print()
```

### 미래 도시
```
import collections
# 다익스트라에 필요한 것들
# 1. 방문 확인 리스트
# 2. 거리 저장 리스트
# 3. 가장 가까운 거리 찾아야 함(근데 이 문제에서는 모든 거리가 1이라서 필요 없음)
INF = int(1e9)

# 1에서 k까지 거리 + k에서 x까지 거리 구해야 함
def findRoute():
    ## 입력 받는 곳
    # n은 회사의 수, m은 연결 경로 수
    n, m = map(int, input().split())
    # 경로 입력
    paths = []
    for i in range(m):
        paths.append(list(map(int, input().split())))
    # k 거쳐서 x 가는 거임
    x, k = map(int, input().split())

    ## 본격적 다익스트라 코드
    def measureRoute(start, end):
        visited = [False] * (n + 1)
        distance = [INF] * (n + 1)
        queue = collections.deque([])

        visited[start] = True
        distance[start] = 0

        for path in paths:
            dp = path[0]  # 경로에서 출발 지점
            dt = path[1]  # 경로에서 도착 지점
            if dp == start:
                distance[dt] = 1
                visited[dt] = True
                queue.append(dt)
            if dt == start:
                distance[dp] = 1
                visited[dp] = True
                queue.append(dp)

        while queue:
            now = queue.popleft()
            for path in paths:
                dp = path[0]
                dt = path[1]
                if dp == now and visited[dt] == False:
                    distance[dt] = distance[now] + 1
                    visited[dt] = True
                    queue.append(dt)
                if dt == now and visited[dp] == False:
                    distance[dp] = distance[now] + 1
                    visited[dp] = True
                    queue.append(dp)

        return distance[end]

    distOneToK = measureRoute(1, k)
    distKToX = measureRoute(k, x)

    if distOneToK + distKToX < INF:
        print(distOneToK + distKToX)
    else:
        print(-1)
```
-> 내 코드는 1에서 k를 거쳐 x로 가는 경로의 길이를 구하는 것을 다익스트라 알고리즘 코드를 2번 사용해서 구했음, 그런데 여기서는 플로이드 워셜 알고리즘으로 모든 경로를 다 구한 다음에 더하면 더 간편

### 미래 도시 문제 해설 답안
```
def findRoute2():
    n, m = map(int, input().split())
    graph = [[INF] * (n + 1) for _ in range(n + 1)]

    for a in range(1, n + 1):
        for b in range(1, n + 1):
            if a == b:
                graph[a][b] = 0

    for _ in range(m):
        a, b = map(int, input().split())
        graph[a][b] = 1
        graph[b][a] = 1
    
    x, k = map(int, input().split())
    
    for k in range(1, n + 1):
        for a in range(1, n + 1):
            for b in range(1, n + 1):
                graph[a][b] = min(graph[a][b], graph[a][k] + graph[k][b])
    
    distance = graph[1][k] + [k][x]
    
    if distance >= INF:
        print("-1")
    else:
        print(distance)
```

### 전보
```
INF = int(1e9)
def sendTelegram():
    n, m, c = map(int, input().split())    # n: 도시 수, m: 통로 수, c: 메시지를 보내는 도시(start)
    graph = [[INF] * (n + 1) for _ in range(n + 1)]
    visited = [False] * (n + 1)
    visited[c] = True
    distance = [INF] * (n + 1)
    distance[c] = 0

    # 가장 가까운 노드 인덱스 반환 함수, INF 반환하면 프로그램 종료하면 됨
    def getNearestNode():
        minLength = INF
        minIndex = 0
        for i in range(n + 1):
            if distance[i] < minLength and visited[i] is False:
                minLength = distance[i]
                minIndex = i
        if minIndex == 0:
            return INF
        else:
            return minIndex

    # 그래프 생성 후 아래 반복문으로 경로 입력 받음
    for a in range(1, n + 1):
        for b in range(1, n + 1):
            if a == b:
                graph[a][b] = 0
    for _ in range(m):
        x, y, z = map(int, input().split())
        graph[x][y] = z

    # 시작 노드부터 처리
    for i in range(n + 1):
        if graph[c][i] < INF and visited[i] is not True:
            distance[i] = graph[c][i]

    while True:
        now = getNearestNode()
        if now == INF:
            break
        visited[now] = True
        for i in range(n + 1):
            if graph[now][i] < INF and visited[i] is not True:
                distance[i] = min(distance[i], distance[now] + graph[now][i])

    # 총 도시 수 계산
    cnt = 0
    for i in range(n + 1):
        if visited[i] == True:
            cnt += 1
    cnt -= 1

    # 걸리는 시간 계산(INF는 무시한 최댓값)
    maxTime = 0
    for i in range(n + 1):
        if distance[i] < INF and distance[i] > maxTime:
            maxTime = distance[i]

    print(cnt, maxTime)
```
-> 여기서는 우선순위 큐를 사용하지 않고 내가 최단 거리 노드를 구하는 함수를 만들어 호출해서 구현했기에 우선순위 큐를 이용하는 것보다 시간 소요가 많이 됨, 따라서 아래 해설 답안 코드 처럼 heapq 라이브러리를 활용해줘야 함

### 전보 문제 해설 답안
```
import heapq
def sendTelegram2():
    n, m, start = map(int, input().split())
    graph = [[] for i in range(n + 1)]
    distance = [INF] * (n + 1)
    
    for _ in range(m):
        x, y, z = map(int, input().split())
        graph[x].append((y, z))
    
    def dijkstra(start):
        q = []
        heapq.heappush(1, (0, start))
        distance[start] = 0
        while q:
            dist, now = heapq.heappop(q)
            if distance[now] < dist:
                continue
            for i in graph[now]:
                cost = dist + i[1]
                if cost < distance[i[0]]:
                    distance[i[0]] = cost
                    heapq.heappush(q, (cost, i[0]))
    
    dijkstra(start)
    
    count = 0
    max_distance = 0
    for d in distance:
        if d != INF:
            count += 1
            max_distance = max(max_distance, d)
    
    print(count - 1, max_distance)
```
