# solvec.ac CLASS 5 문제 풀이
### ACM Craft(dfs 풀이)
```
import sys

def dfs(this):
    tempParent = parents[this]
    if len(tempParent) > 0:
        taskTimes = []
        for elem in tempParent:
            taskTimes.append(dfs(elem))
        return times[this] + max(taskTimes)
    else:
        return times[this]

T = int(sys.stdin.readline().rstrip())

for _ in range(T):
    N, K = map(int, sys.stdin.readline().rstrip().split())
    times = list(map(int, sys.stdin.readline().rstrip().split()))
    parents = [[] for _ in range(N)]
    childs = [[] for _ in range(N)]
    for _ in range(K):
        parent, child = map(int, sys.stdin.readline().rstrip().split())
        parents[child - 1].append(parent - 1)
        childs[parent - 1].append(child - 1)
    W = int(sys.stdin.readline().rstrip())
    isProcessed = [False] * N

    ans = dfs(W - 1)
    print(ans)
```
-> dfs로 어렵지 않게 답을 유도할 수 있었으나 시간 초과가 난다. 해당 문제는 위상 정렬로 풀어야한다고 하는데 각 노드의 값에 따라 처리를 해줘야 하므로 단순한 위상정렬로는 풀이가 힘들것 같다.

### ACM Craft(위상정렬 풀이)
```
import sys
from collections import deque

def isAllParentProcessed(child):
    for elem in parents[child]:
        if not isProcessed[elem]:
            return False
    return True

T = int(sys.stdin.readline().rstrip())

for _ in range(T):
    N, K = map(int, sys.stdin.readline().rstrip().split())
    times = list(map(int, sys.stdin.readline().rstrip().split()))
    parents = [[] for _ in range(N)]
    childs = [[] for _ in range(N)]
    for _ in range(K):
        parent, child = map(int, sys.stdin.readline().rstrip().split())
        parents[child - 1].append(parent - 1)
        childs[parent - 1].append(child - 1)
    W = int(sys.stdin.readline().rstrip())

    timeNeeds = [-1] * N
    isProcessed = [False] * N
    q = deque([])
    for elem in range(N):
        if len(parents[elem]) == 0:
            q.append(elem)
            timeNeeds[elem] = times[elem]
            isProcessed[elem] = True


    while True:
        if isAllParentProcessed(W - 1) and isProcessed[W - 1]:
            break

        now = q.popleft()
        if now == 4:
            print('', end='')
        isProcessed[now] = True
        for elem in childs[now]:
            tempChild = elem
            timeNeeds[elem] = max(timeNeeds[elem], timeNeeds[now] + times[elem])
            if not isProcessed[elem] and isAllParentProcessed(tempChild):
                q.append(elem)
    print(timeNeeds[W - 1])
```
-> 위상정렬 코드에서 조금만 더 생각하면 풀 수 있었는데, 처음에 너무 어렵게 생각한 것 같다.

### 최소 스패닝 트리
```
import sys
import heapq

def checkFinished():
    for elem in parent:
        if elem != 0:
            return False
    return True

# 서로소 함수 사용하기
def find(x):
    if parent[x] == x:
        return x
    else:
        return find(parent[x])

def union(x, y):
    xp = find(x)
    yp = find(y)
    if xp < yp:
        parent[yp] = xp
    else:
        parent[xp] = yp

V, E = map(int, sys.stdin.readline().rstrip().split())
roads = []
parent = []
for i in range(V):
    parent.append(i)

for _ in range(E):
    a, b, c = map(int, sys.stdin.readline().rstrip().split())
    heapq.heappush(roads, [c, a - 1, b - 1])

ans = 0

while roads:
    if checkFinished():
        break
    z, x, y = heapq.heappop(roads)
    px = find(x)
    py = find(y)
    if px == py:
        continue
    ans += z
    union(x, y)

print(ans)
```

### 보석 도둑(내 풀이)
```
import sys
import heapq

def calcScore(gidx, bidx):
    temp = 0
    for i in range(K - bidx):
        if gidx + i == N:
            break
        temp += gems[gidx + i][1]
    return temp * (-1)

N, K = map(int, sys.stdin.readline().rstrip().split())
gems = []
for _ in range(N):
    M, V = map(int, sys.stdin.readline().rstrip().split())
    gems.append([M, V])
gems.sort(key=lambda x:x[1], reverse=True)
bags = []
for _ in range(K):
    bags.append(int(sys.stdin.readline().rstrip()))
bags.sort(reverse=True)

ans = 0
potenScore = calcScore(0, 0)

maxHeap = []
heapq.heappush(maxHeap, [potenScore, 0, 0, 0])

while maxHeap:
    score, gidx, bidx, tempSum = heapq.heappop(maxHeap)
    if gidx == N or bidx == K:
        ans = max(ans, tempSum)
        continue
    if score * (-1) < ans:
        break
    gem = gems[gidx]
    bag = bags[bidx]
    # 넣을 수 있는 경우
    if gem[0] <= bag:
        # 넣는 경우
        heapq.heappush(maxHeap, [score, gidx + 1, bidx + 1, tempSum + gem[1]])
        # 넣지 않는 경우
        heapq.heappush(maxHeap, [(-1) * tempSum + calcScore(gidx + 1, bidx), gidx + 1, bidx, tempSum])
    # 넣을 수 없는 경우
    else:
        heapq.heappush(maxHeap, [(-1) * tempSum + calcScore(gidx + 1, bidx), gidx + 1, bidx, tempSum])

print(ans)
```
-> 처음엔 한계를 정하고 dfs로 접근을 하였는데 시간초과가 나서, 힙에 각 노드들의 정보를 넣어서 푸는 방법으로 코드를 짰는데 역시 시간초과가 난다.. 어디서 시간 소요가 많이 났는지 정확히 모르겠다.

### 보석 도둑(모범 답안)
```
import sys
import heapq

N, K = map(int, sys.stdin.readline().rstrip().split())
jew = []
for _ in range(N):
    heapq.heappush(jew, list(map(int, sys.stdin.readline().rstrip().split())))
bags = []
for _ in range(K):
    bags.append(int(sys.stdin.readline().rstrip()))
bags.sort()

answer = 0
tempjew = []
for bag in bags:
    # 모든 보석을 보면서 각 가방에 들어갈 수 있는 보석들을 tempjew에 담는다
    while jew and bag >= jew[0][0]:
        heapq.heappush(tempjew, -heapq.heappop(jew)[1])
    # 각 가방에 들어갈 수 있는 가장 비싼 보석의 가치를 더한다
    if tempjew:
        answer -= heapq.heappop(tempjew)
    # 모든 보석에 대한 탐색이 끝나면 종료
    elif not jew:
        break

print(answer)
```
-> 나는 가방을 큰 가방 부터 채워보는 방식으로 모든 가능한 문제의 영역을 힙에 넣어서 풀었다. 그런데 이 문제는 작은 가방부터 해당 가방에 들어갈 수 있는 최대의 가치를 넣으면 최종적으로 최대의 가치를 담을 수 있기 때문에 이 아이디어를 활용하면 문제의 공간을 훨씬 줄일 수 있었다.

### 수 나누기 게임
```
import sys
INF = int(1e9)

N = map(int, sys.stdin.readline().rstrip())
nums = list(map(int, sys.stdin.readline().rstrip().split()))
score = [INF] * 1000001
for num in nums:
    score[num] = 0

for i in range(1, 500001):
    if score[i] != INF:
        for x in range(2, (1000000 // i) + 1):
            j = i * x
            if score[j] != INF:
                score[j] -= 1
                score[i] += 1

for num in nums:
    print(score[num], end=' ')
```
-> EZ

### 할로윈의 양아치
```
import sys
from collections import deque

N, M, K = map(int, sys.stdin.readline().rstrip().split())
candies = list(map(int, sys.stdin.readline().rstrip().split()))
candies.insert(0, 0)
relationships = [[] for _ in range(N + 1)]
for _ in range(M):
    x, y = map(int, sys.stdin.readline().rstrip().split())
    relationships[x].append(y)
    relationships[y].append(x)

visited = [False] * (N + 1)
groups = []

# 그룹 나누기 코드
for i in range(1, N + 1):
    if not visited[i]:
        visited[i] = True
        temp = candies[i]
        q = deque()
        q.append(i)
        cnt = 1
        while q:
            now = q.popleft()
            for elem in relationships[now]:
                if not visited[elem]:
                    q.append(elem)
                    temp += candies[elem]
                    cnt += 1
                    visited[elem] = True
        groups.append([cnt, temp])

groups.sort(key=lambda x:x[0])
dp = [[0] * K for _ in range(len(groups) + 1)]

for i in range(1, len(groups) + 1):
    for j in range(1, K):
        if j >= groups[i - 1][0]:
            dp[i][j] = max(dp[i - 1][j - groups[i - 1][0]] + groups[i - 1][1], dp[i - 1][j])
        else:
            dp[i][j] = dp[i - 1][j]

print(dp[len(groups)][K - 1])
```

### 할로윈의 양아치(모범답안)
```
import sys
input = sys.stdin.readline

def find_parent(x):
    if parent[x] != x:
        parent[x] = find_parent(parent[x])
    return parent[x]

def union_parent(a, b):
    a, b = (a, b) if a>b else (b, a)
    parent[a] = b
    visited[b] += visited[a]
    arr[b] += arr[a]

N, M, K = map(int, input().split())
arr = [0] + list(map(int, input().split()))
relation = [tuple(map(int, input().split())) for _ in range(M)]
parent = [i for i in range(N+1)]
visited = [1] * (N+1)

for a, b in relation:
    a, b = find_parent(a), find_parent(b)
    if a!=b:
        union_parent(a, b)

candys = []
for i in range(1,N+1):
    if parent[i] == i:
        if visited[i] < K:
            candys.append((visited[i], arr[i]))
candys.sort(key=lambda x: (x[0], x[1]))

DP = [0] * (K+1)
for child, candy in candys:
    for i in range(K, child-1, -1):
        DP[i] = max(DP[i-child]+candy, DP[i])

print(DP[K-1])
```

### 용액
```
import sys

N = int(sys.stdin.readline().rstrip())
numList = list(map(int, sys.stdin.readline().rstrip().split()))

start = 0
end = len(numList) - 1
ans = [numList[start], numList[end]]
temp = numList[start] + numList[end]
minNum = abs(temp)

while start != end:
    if temp > 0:
        end -= 1
        if end == start:
            break
        temp = numList[start] + numList[end]
        if abs(temp) <= minNum:
            minNum = abs(temp)
            ans = [numList[start], numList[end]]
    elif temp < 0:
        start += 1
        if end == start:
            break
        temp = numList[start] + numList[end]
        if abs(temp) <= minNum:
            minNum = abs(temp)
            ans = [numList[start], numList[end]]
    else:     # 합이 0이면 바로 탈출하면될듯?
        break

print(ans[0], ans[1])
```
-> 투 포인터 알고리즘으로 쉽게 해결할 수 있다

### 세 용액
```
import sys
INF = int(1e10)

def binarySearch(first, third):
    global minNum, ans
    start = first + 1
    end = third - 1
    mid = (start + end) // 2
    sumOfTwo = numList[first] + numList[third]
    while start <= end:
        if abs(sumOfTwo + numList[mid]) < minNum:
            minNum = abs(sumOfTwo + numList[mid])
            ans = [numList[first], numList[mid], numList[third]]

        if sumOfTwo + numList[mid] < 0:
            start = mid + 1
            mid = (start + end) // 2
        elif sumOfTwo + numList[mid] > 0:
            end = mid - 1
            mid = (start + end) // 2
        else:
            minNum = 0
            ans = [numList[first], numList[mid], numList[third]]
            return True
    return False

N = int(sys.stdin.readline().rstrip())
numList = list(map(int, sys.stdin.readline().rstrip().split()))
numList.sort()
minNum = INF
ans = [numList[0], numList[1], numList[2]]

# i는 start, j는 end
for i in range(N - 2):
    for j in range(i + 2, N):
        flag = binarySearch(i, j)
        if flag:
            ans.sort()
            print(ans[0], ans[1], ans[2])
            exit(0)
ans.sort()
print(ans[0], ans[1], ans[2])
```
-> 위 풀이 방식은 최적의 3가지 용액을 찾기 위해 2가지의 용액은 완전 탐색으로 하고 나머지 하나는 완전 탐색으로 구한 2가지 용액 사이의 값들을 이진 탐색으로 찾게 해주었다. 처음 2가지 용액을 구하는 데에서 완전 탐색으로 했다는 점에서 큰 성능 저하가 있을 것으로 보인다. 투 포인터로 완전 탐색의 문제점을 해결할 수 있을것 같은데, 어떤 기준으로 각 포인터 위치 값을 바꿀 수 있을지를 정확히 모르겠다.

### 세 용액(모범 답안)
```
import sys

n = int(input())
array = list(map(int, input().split()))

array.sort()
minTake = sys.maxsize

for i in range(n-2):
    start = i + 1
    end = n - 1
    while start < end:
        take = array[i] + array[start] + array[end]
        if abs(take) < minTake:
            minTake = abs(take)
            result = [array[i], array[start], array[end]]
        if take < 0:
            start += 1
        elif take > 0:
            end -= 1
        else:
            break
            
print(result[0], result[1], result[2])
```
-> 위 풀이는 2개를 고정하고 하나를 찾는 나의 방식과 다르게 하나를 고정하고 나머지 2개를 투 포인터 알고리즘으로 찾는 코드이다. 해당 방법을 생각을 못했는데, 위 방법을 사용하면 나의 코드 대비 수행 시간이 10배 빠르다.

### 사이클 게임
```
import sys

def find(x):
    if x == parent[x]:
        return x
    else:
        return find(parent[x])

def union(x, y):
    xp = find(x)
    yp = find(y)
    if xp < yp:
        parent[yp] = xp
    else:
        parent[xp] = yp

n, m = map(int, sys.stdin.readline().rstrip().split())
lines = []
for _ in range(m):
    x, y = map(int, sys.stdin.readline().rstrip().split())
    lines.append([x, y])

parent = []
for i in range(n):
    parent.append(i)

for i in range(m):
    x, y = lines[i]
    xp = find(x)
    yp = find(y)
    if xp == yp:
        print(i + 1)
        exit(0)
    union(x, y)
print(0)
```

### 사이클 게임(모범 답안)
```
import sys

def find(x):
    if x == p[x]:
        return x
    else:
        p[x] = find(p[x])
        return p[x]

n, m = map(int, sys.stdin.readline().rstrip().split())
p = [i for i in range(n)]

for i in range(m):
    x, y = map(int, sys.stdin.readline().rstrip().split())
    x = find(x)
    y = find(y)
    if x == y:
        print(i + 1)
        exit(0)
    if x < y:
        p[y] = p[x]
    else:
        p[x] = p[y]
print(0)
```
-> 알고리즘은 같지만, 코드가 더 간결하고 성능이 좋다.

### 행성 터널
```
import sys

def find(x):
    if x == parent[x]:
        return x
    else:
        parent[x] = find(parent[x])
        return parent[x]

N = int(sys.stdin.readline().rstrip())
planets = []
for i in range(N):
    temp = list(map(int, sys.stdin.readline().rstrip().split()))
    temp.append(i)
    planets.append(temp)
parent = [i for i in range(N)]
roads = []

for j in range(3):
    planets.sort(key=lambda x:x[j])
    for i in range(N - 1):
        roads.append([planets[i][3], planets[i + 1][3], abs(planets[i][j] - planets[i + 1][j])])
roads.sort(key=lambda x:x[2])

cnt = 0
ans = 0
for i in range(len(roads)):
    road = roads[i]
    x, y, weight = road[0], road[1], road[2]
    xp = find(x)
    yp = find(y)
    if xp == yp:
        continue
    cnt += 1
    if xp < yp:
        parent[yp] = xp
    else:
        parent[xp] = yp
    ans += weight
print(ans)
```
-> 처음엔 각 구간의 길이를 확인하는 과정에서 pop(0)으로 구현하였다. 이때 시간 초과가 나고 인덱스 접근으로 고치자 정답 판정을 받았는데, pop()으로 구현할 시 파이썬 내부 메모리 관리하는 과정에서 시간이 많이 소요되는것 같다. pop()을 함부로 사용하지말자...🥲

### 도시 분할 계획
```
import sys
from heapq import heappop, heappush

def find(x):
    if parent[x] == x:
        return x
    else:
        return find(parent[x])

def union(x, y):
    xp = find(x)
    yp = find(y)
    if xp < yp:
        parent[yp] = xp
    else:
        parent[xp] = yp

N, M = map(int, sys.stdin.readline().rstrip().split())
routes = []

for _ in range(M):
    a, b, c = map(int, sys.stdin.readline().rstrip().split())
    heappush(routes, [c, a, b])

parent = [i for i in range(N + 1)]
parent[0] = 1

ans = 0
selected = []
while routes:
    if len(selected) == N - 1:
        break
    val, a, b = heappop(routes)
    if find(a) == find(b):
        continue
    union(a, b)
    ans += val
    heappush(selected, (-1) * val)

temp = heappop(selected) * (-1)
print(ans - temp)
```
