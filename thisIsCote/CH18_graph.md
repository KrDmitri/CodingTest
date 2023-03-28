## Chapter 18 그래프 이론 문제
### 여행 계획
```
def planTravel():
    def findParent(elem):
        if elem == parent[elem]:
            return elem
        else:
            return findParent(parent[elem])

    def unionParent(a, b):
        a = findParent(a)
        b = findParent(b)

        if a <= b:
            parent[b] = a
        else:
            parent[a] = b

    n, m = map(int, input().split())
    routes = []
    for _ in range(n):
        routes.append(list(map(int, input().split())))
    plan = list(map(int, input().split()))

    parent = [0] * (n + 1)
    for i in range(1, n + 1):
        parent[i] = i

    for i in range(n):
        for j in range(n):
            if routes[i][j] == 1:
                unionParent(i + 1, j + 1)

    flag = True
    for i in range(m - 1):
        if findParent(plan[i]) != findParent(plan[i + 1]):
            flag = False
            break

    if flag:
        print('YES')
    else:
        print('NO')
```

### 여행 계획 해설 답안 코드
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

n, m = map(int, input().split())
parent = [0] * (n + 1)

for i in range(n):
    data = list(map(int, input().split()))
    for j in range(n):
        if data[j] == 1:
            union_parent(parent, i + 1, j + 1)

plan = list(map(int, input().split()))

result = True
for i in range(m - 1):
    if find_parent(parent, plan[i]) != find_parent(parent, plan[i + 1]):
        result = False

if result:
    print('YES')
else:
    print('NO')
```
-> 노드끼리 연결되어 있는지 확인할 때는 서로소 집합 알고리즘을 활용

### 탑승구
```
def boardingGate():
    def findMaxGateNum(size):
        for i in range(size, -1, - 1):
            if gateAvailable[i] == True:
                return i

    numGates = int(input())
    numPlanes = int(input())
    gateInfo = []
    for _ in range(numPlanes):
        gateInfo.append(int(input()) - 1)
    gateAvailable = [True] * numGates

    result = 0

    for i in range(numPlanes):
        gateSize = gateInfo[i]
        if True not in gateAvailable[0:gateSize + 1]:
            break
        else:
            tempGate = findMaxGateNum(gateSize)
            gateAvailable[tempGate] = False
            result += 1

    print(result)
```

### 탑승구2
```
def boardingGate2():
    def findParent(elem):
        if elem == parent[elem]:
            return elem
        else:
            return findParent(parent[elem])

    def unionParent(a, b):
        a = findParent(a)
        b = findParent(b)
        if a <= b:
            parent[b] = a
        else:
            parent[a] = b

    g = int(input())
    p = int(input())
    planeInfo = []
    for i in range(p):
        planeInfo.append(int(input()))

    parent = []
    for i in range(g + 1):
        parent.append(i)

    result = 0

    for i in range(p):
        tempGate = findParent(planeInfo[i])
        if tempGate == 0:
            break
        else:
            unionParent(tempGate, tempGate - 1)
            result += 1

    print(result)
```
-> 서로소 집합 알고리즘을 활용한 문제 해결

### 어두운 길
```
import heapq
def darkRoad():
    def findParent(a):
        if a == parent[a]:
            return a
        else:
            return findParent(parent[a])

    def unionParent(a, b):
        a = findParent(a)
        b = findParent(b)
        if a <= b:
            parent[b] = a
        else:
            parent[a] = b

    n, m = map(int, input().split())
    roads = []
    wholeCost = 0
    parent = []
    selectedRoads = []
    resultCost = 0
    for _ in range(m):
        x, y, z = map(int, input().split())
        heapq.heappush(roads, (z, x, y))
        wholeCost += z

    for i in range(n):
        parent.append(i)

    while len(selectedRoads) < n - 1:
        tempCost, x, y = heapq.heappop(roads)
        if findParent(x) != findParent(y):
            unionParent(x, y)
            resultCost += tempCost
            selectedRoads.append((x, y, tempCost))

    print(wholeCost - resultCost)
```
-> 최소 신장 트리 알고리즘 역시 서로소 집합 알고리즘을 활용하여 구현

### 행성 터널
```
import heapq
def planetTunnel():
    def calculateDistance(aPlanet, bPlanet):
        ax, ay, az = aPlanet[0], aPlanet[1], aPlanet[2]
        bx, by, bz = bPlanet[0], bPlanet[1], bPlanet[2]
        dist = min(abs(ax - bx), abs(ay - by), abs(az - bz))

        return dist

    def findParent(a):
        if a == parents[a]:
            return a
        else:
            return findParent(parents[a])

    def unionParent(a, b):
        a = findParent(a)
        b = findParent(b)
        if a <= b:
            parents[b] = a
        else:
            parents[a] = b


    n = int(input())
    planets = []
    paths = []
    parents = []

    for i in range(n):
        parents.append(i)

    for _ in range(n):
        x, y, z = map(int, input().split())
        planets.append((x, y, z))

    for i in range(len(planets)):
        for j in range(i, len(planets)):
            tempDist = calculateDistance(planets[i], planets[j])
            heapq.heappush(paths, (tempDist, i, j))

    pathCount = 0
    cost = 0
    while pathCount < n - 1:
        tempDist, aPlanet, bPlanet = heapq.heappop(paths)
        if findParent(aPlanet) != findParent(bPlanet):
            unionParent(aPlanet, bPlanet)
            pathCount += 1
            cost += tempDist

    print(cost)
```
