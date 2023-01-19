## Chapter 10 그래프 이론
### 크루스칼 알고리즘 소스 코드(서로소 집합 알고리즘을 통해 cycle을 판별함)
```
def find_parent(parent, x):
    if parent[x] != x:
        parent[x] = find_parent(parent, parent[x])
    return parent[x]

def union_parent(parent, a, b):
    a = find_parent(parent, a)
    b = find_parent(parent, b)
    if a < b:
        parent[b] = a
    else:
        parent[a] = b

v, e = map(int, input().split())
parent = [0] * (v + 1)

edges = []
result = 0

for i in range(1, v + 1):
    parent[i] = i

for _ in range(e):
    a, b, cost = map(int, input().split())
    edges.append((cost, a, b))

edges.sort()

for edge in edges:
    cost, a, b = edge
    if find_parent(parent, a) != find_parent(parent, b):
        union_parent(parent, a, b)
        result += cost

print(result)
```

### 위상 정렬 알고리즘 소스코드
```
from collections import deque

v, e = map(int, input().split())

# 진입차수
indegree = [0] * (v + 1)
graph = [[] for i in range(v + 1)]

for _ in range(e):
    a, b = map(int, input().split())
    graph[a].append(b)
    indegree[b] += 1

# 위상 정렬 함수
def topology_sort():
    result = []
    q = deque()
    for i in range(1, v + 1):
        if indegree[i] == 0:
            q.append(i)
    
    while q:
        now = q.popleft()
        result.append(now)
        for i in graph[now]:
            indegree[i] -= 1
            if indegree[i] == 0:
                q.append(i)
    
    for i in result:
        print(i, end=' ')

topology_sort()
```

### 팀 결성
```
def combineTeam():
    def findParent(parent, x):
        if parent[x] != x:
            parent[x] = findParent(parent, parent[x])
        return parent[x]

    def unionTeam(parent, x, y):
        x = findParent(parent, x)
        y = findParent(parent, y)
        if x < y:
            parent[y] = x
        else:
            parent[x] = y

    def checkTeam(parent, x, y):
        x = findParent(parent, x)
        y = findParent(parent, y)
        if x == y:
            print('YES')
        else:
            print('NO')

    n, m = map(int, input().split())
    operations = []
    parent = [0] * (n + 1)

    for i in range(1, n + 1):
        parent[i] = i

    for _ in range(m):
        operations.append(list(map(int, input().split())))

    for operation in operations:
        sort = operation[0]
        a = operation[1]
        b = operation[2]

        if sort == 0:
            unionTeam(parent, a, b)
        elif sort == 1:
            checkTeam(parent, a, b)
```

### 도시 분할 계획
```
def divideCity():
    def findParent(parent, x):
        if parent[x] != x:
            parent[x] = findParent(parent, parent[x])
        return parent[x]

    def unionCity(parent, a, b):
        x = findParent(parent, a)
        y = findParent(parent, b)
        if x < y:
            parent[y] = x
        else:
            parent[x] = y

    n, m = map(int, input().split())
    parent = [0] * (n + 1)
    for i in range(1, n + 1):
        parent[i] = i

    # 간선 정보 입력
    edges = []
    for _ in range(m):
        edges.append(list(map(int, input().split())))

    edges.sort(key=lambda e:e[2])

    # 총 길이 result, 각 도로 길이 저장 confirmedRoad
    result = 0
    confirmedRoad = []

    for edge in edges:
        a = edge[0]
        b = edge[1]
        cost = edge[2]

        if findParent(parent, a) != findParent(parent, b):
            unionCity(parent, a, b)
            result += cost
            confirmedRoad.append(cost)

    confirmedRoad.sort()
    max = confirmedRoad[-1]
    print(result - max)
```
