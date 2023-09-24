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
