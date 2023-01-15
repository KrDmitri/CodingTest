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
