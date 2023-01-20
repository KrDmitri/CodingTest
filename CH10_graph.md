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

### 커리큘럼
```
def makeCurriculum2():
    def findIndex(indegree):
        for i in range(len(indegree)):
            if indegree[i] == 0:
                return i

    n = int(input())
    curriculum = [0]
    studyTime = [0] * (n + 1)
    indegree = [-1] * (n + 1)
    specialNode = [False] * (n + 1)
    edges = []

    # 데이터 입력
    for i in range(1, n + 1):
        curriculum.append(list(map(int, input().split())))

    # 이 루프에선 간선 정보 리스트를 생성해줘야함(내용 수정 필요)
    for i in range(1, n + 1):
        # 각각의 리스트 첫번째 원소는 자신의 공부 시간, indegree는 차수
        eachList = curriculum[i]
        indegree[i] = len(eachList) - 2
        ## 차수가 맨 처음 1인 경우
        if indegree[i] == 1:
            specialNode[i] = True
        # 마지막은 -1이라서 count 안하므로 전체 길이 - 1만큼 loop 실행(해당 루프에 작업해야 할 데이터 존재)
        for j in range(1, len(eachList) - 1):
            edges.append((eachList[j], i))

    while 0 in indegree:
        now = findIndex(indegree)
        studyTime[now] += curriculum[now][0]
        indegree[now] -= 1
        for edge in edges:
            fromNode = edge[0]
            toNode = edge[1]
            if fromNode == now:
                indegree[toNode] -= 1
                studyTime[toNode] += curriculum[fromNode][0]

    # 맨 처음 차수가 1인 경우 처리
    for i in range(1, n + 1):
        if specialNode[i]:
            for edge in edges:
                fromNode = edge[0]
                toNode = edge[1]
                if toNode == i:
                    studyTime[toNode] = studyTime[fromNode] + curriculum[toNode][0]

    for i in range(1, n + 1):
        print(studyTime[i])
```
-> 이번 문제는 좀 까다로웠는데, 부모노드가 다른 부모노드를 가질 경우 일반적인 위상 정렬 개념으로 접근하면 정확한 답이 나오기 어려웠음, 그래서 이를 맨 처음 차수가 1인 경우를 처리해주면서 해결할 수 있었는데 내가 짠 코드가 좀 길어짐

### 커리큘럼 문제 해설 답안
```
from collections import deque
import copy
def makeCurriculum3():
    v = int(input())
    indegree = [0] * (v + 1)
    graph = [[] for i in range(v + 1)]
    time = [0] * (v + 1)
    
    for i in range(1, v + 1):
        data = list(map(int, input().split()))
        time[i] = data[0]
        for x in data[1:-1]:
            indegree[i] += 1
            graph[x].append(i)
    
    def topology_sort():
        result = copy.deepcopy(time)
        q = deque()
        
        for i in range(1, v + 1):
            if indegree[i] == 0:
                q.append(i)
        
        while q:
            now = q.popleft()
            for i in graph[now]:
                result[i] = max(result[i], result[now] + time[i])
                indegree[i] -= 1
                if indegree[i] == 0:
                    q.append(i)
        
        for i in range(1, v + 1):
            print(result[i])
    
    topology_sort()
```
-> 알고보니 동시에 여러 강의 들을 수 있다고 가정이었음..(문제 잘 읽기) 그래서 생각보다 더 쉬운 문제, 각 간선을 확인하면서 선수과목 시간 + 본 강의 시간 한 것들 max()함수로 큰 것을 갱신해주면 됨
