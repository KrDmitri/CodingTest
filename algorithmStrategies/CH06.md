# CH06 무식하게 풀기
### 소풍
```
import sys

T = int(sys.stdin.readline().rstrip())

def dfs(depth, used, start):
    if depth == n // 2:
        return 1
    ans = 0

    for i in range(start, n):
        if not used[i]:
            used[i] = True
            for j in range(i + 1, n):
                if isFriend[i][j] and not used[j]:
                    used[j] = True
                    ans += dfs(depth + 1, used, i)
                    used[j] = False
            used[i] = False
    return ans

for _ in range(T):
    # n은 학생 수, m은 친구 쌍 수
    n, m = map(int, sys.stdin.readline().rstrip().split())
    friendList = list(map(int, sys.stdin.readline().rstrip().split()))
    isFriend = [[False] * n for _ in range(n)]
    for i in range(0, m * 2, 2):
        isFriend[friendList[i]][friendList[i + 1]] = True
        isFriend[friendList[i + 1]][friendList[i]] = True
    used = [False] * n
    ans = dfs(0, used, 0)
    print(ans)
```
-> 이제까지 위와 같은 브루트포스 문제를 풀 때 중복으로 답을 세는 것을 방지하기 위해 visited 등과 같은 list를 사용하였는데, 위의 경우와 같이 visited를 사용하기 어려운 경우에는 특정한 답의 모양을 강제(ex. 순서 강제)해서 재귀 함수 내부에서 답의 수를 구하는 것으로 해결할 수 있다는 것을 알게되었다.

### 게임판 덮기(시간 초과)
```
import sys

# 현재 탐색하는 칸에서 채울 수도 있지만, 채우지 않을 수도 있다.
def fillBlank(graph, x, y):
    if x == n and y == 0:
        for i in range(n):
            for j in range(m):
                if graph[i][j] == '.':
                    return 0
        return 1

    ans = 0
    if graph[x][y] == '.':
        for i in range(4):
            nx1 = x + dx[i]
            ny1 = y + dy[i]
            if nx1 < 0 or nx1 >= n or ny1 < 0 or ny1 >= m or graph[nx1][ny1] == '#':
                continue
            ni = (i + 1) % 4
            nx2 = x + dx[ni]
            ny2 = y + dy[ni]
            if nx2 < 0 or nx2 >= n or ny2 < 0 or ny2 >= m or graph[nx2][ny2] == '#':
                continue
            graph[x][y] = '#'
            graph[nx1][ny1] = '#'
            graph[nx2][ny2] = '#'
            nx = x
            ny = y + 1
            if ny == m:
                ny = 0
                nx += 1
            ans += fillBlank(graph, nx, ny)
            graph[x][y] = '.'
            graph[nx1][ny1] = '.'
            graph[nx2][ny2] = '.'

    nx = x
    ny = y + 1
    if ny == m:
        ny = 0
        nx += 1
    ans += fillBlank(graph, nx, ny)
    return ans

T = int(sys.stdin.readline().rstrip())

dx = [-1, 0, 1, 0]
dy = [0, 1, 0, -1]

for _ in range(T):
    n, m = map(int, sys.stdin.readline().rstrip().split())
    graph = []
    for _ in range(n):
        graph.append(list(sys.stdin.readline().rstrip()))

    white = 0
    for i in range(n):
        for j in range(m):
            if graph[i][j] == '.':
                white += 1
    if white % 3 != 0:
        print(0)
        continue

    flag = False
    for i in range(n):
        for j in range(m):
            if graph[i][j] == '.':
                x = i
                y = j
                flag = True
                break
        if flag:
            break

    ans = fillBlank(graph, x, y)
    print(ans)
```
-> 하드 코딩 느낌이 있지만 정확도는 높다고 생각하는데 큰 테스트 케이스에서 시간 초과가 난다..

### 게임판 덮기
```
import sys

def dfs(graph):
    # 기저 사례인지 확인 + 현재 가장 빈 칸이 어딘지 확인
    flag = True
    for i in range(n):
        for j in range(m):
            if graph[i][j] == '.':
                x, y = i, j
                flag = False
                break
        if not flag:
            break
    if flag:
        return 1

    ans = 0
    for i in range(4):
        dx, dy = dxList[i], dyList[i]
        nx1 = x + dx[0]
        ny1 = y + dy[0]
        nx2 = x + dx[1]
        ny2 = y + dy[1]
        if nx1 < 0 or nx1 >= n or ny1 < 0 or ny1 >= m or graph[nx1][ny1] == '#':
            continue
        if nx2 < 0 or nx2 >= n or ny2 < 0 or ny2 >= m or graph[nx2][ny2] == '#':
            continue
        graph[x][y], graph[nx1][ny1], graph[nx2][ny2] = '#', '#', '#'
        ans += dfs(graph)
        graph[x][y], graph[nx1][ny1], graph[nx2][ny2] = '.', '.', '.'

    return ans


dxList = [[1, 0], [1, 1], [0, 1], [1, 1]]
dyList = [[0, 1], [0, 1], [1, 1], [-1, 0]]

T = int(sys.stdin.readline().rstrip())

for _ in range(T):
    n, m = map(int, sys.stdin.readline().rstrip().split())
    graph = []
    for _ in range(n):
        graph.append(list(sys.stdin.readline().rstrip()))
    ans = 0
    ans += dfs(graph)

    print(ans)
```
-> 처음 접근 법은 현재 위치를 중심으로 해서 게임판을 덮는 방식이었다. 현재 칸을 채우지 않는 수도 있기 때문에 탐색의 범위가 굉장히 넓었는데, 위와 같은 풀이는 현재 칸을 반드시 채우는 방식이라서 탐색 범위가 줄어들었다.

### 시계 맞추기
```
import sys
INF = int(1e9)

def isAllTwelve(nums):
    for num in nums:
        if num != 12:
            return False
    return True

def switchOn(nums, switchNum):
    for clock in switches[switchNum]:
        nums[clock] += 3
        if nums[clock] == 15:
            nums[clock] = 3
    return nums

def backTracking(nums, index, temp):
    global ans
    if temp >= ans:
        return
    if index == 10:
        if isAllTwelve(nums):
            ans = min(ans, temp)
        return

    backTracking(nums, index + 1, temp)
    newNums = [num for num in nums]
    for i in range(3):
        newNums = switchOn(newNums, index)
        backTracking(newNums, index + 1, temp + i + 1)


switches = [[0, 1, 2], [3, 7, 9, 11], [4, 10, 14, 15], [0, 4, 5, 6, 7], [6, 7, 8, 10, 12], [0, 2, 14, 15], [3, 14, 15], [4, 5, 7, 14, 15], [1, 2, 3, 4, 5], [3, 4, 5, 9, 13]]

T = int(sys.stdin.readline().rstrip())

for _ in range(T):
    nums = list(map(int, sys.stdin.readline().rstrip().split()))
    ans = INF

    backTracking(nums, 0, 0)
    print(ans)
```
-> 처음 해당 문제를 어떻게 풀지 감이 오지 않았는데 책에서 설명한 문제의 특성을 이용해서 풀 수 있었다. 앞으로 어려운 문제를 만났을 때는 문제의 특성을 먼저 더 생각해봐야겠다.
