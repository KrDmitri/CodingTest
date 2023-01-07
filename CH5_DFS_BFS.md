## Chapter 05 DFS/BFS
### 음료수 얼려 먹기 문제
```
from collections import deque

def countIce():
    # 필요 정보 저장
    n, m = map(int, input().split())
    iceFrame = []
    for i in range(n):
        iceFrame.append(list(map(int, input().split())))

    # 방문 리스트
    visited = [[False] * m for _ in range(n)]

    # 2차원 배열 그래프처럼 쓰려고 인접 노드 정보 저장
    adjacent = [[] * m for _ in range(n)]
    steps = [(-1, 0), (0, -1), (0, 1), (1, 0)]
    for i in range(n):
        for j in range(m):
            tempList = []
            for step in steps:
                newI = i + step[0]
                newJ = j + step[1]
                if newI >= 0 and newI < n and newJ >= 0 and newJ < m:
                    tempList.append((newI, newJ))
            adjacent[i].append(tempList)

    count = 0

    # 본격적 탐색
    for i in range(n):
        for j in range(m):
            # 새로운 얼음의 첫 칸 발견 시
            if iceFrame[i][j] == 0 and visited[i][j] == False:
                visited[i][j] = True
                queue = deque([(i, j)])
                while queue:
                    v = queue.popleft()
                    for k in adjacent[v[0]][v[1]]:
                        if not visited[k[0]][k[1]] and iceFrame[k[0]][k[1]] == 0:
                            queue.append(k)
                            visited[k[0]][k[1]] = True
                count += 1
    print(count)
```
### 음료수 얼려 먹기 해설 답안
```
def countIce2():
    n, m = map(int, input().split())

    graph = []
    for i in range(n):
        graph.append(list(map(int, input().split())))

    def dfs(x, y):
        if x <= -1 or x >= n or y <= -1 or y >= m:
            return False
        if graph[x][y] == 0:
            graph[x][y] = 1
            dfs(x - 1, y)
            dfs(x, y - 1)
            dfs(x + 1, y)
            dfs(x, y + 1)
            return True
        return False

    result = 0
    for i in range(n):
        for j in range(m):
            if dfs(i, j) == True:
                result += 1
    print(result)
```

### 미로 탈출 문제
```
from collections import deque

def escapeMaze():
    n, m = map(int, input().split())
    maze = []
    for i in range(n):
        maze.append(list(map(int, input().split())))

    visited = [[False] * m for _ in range(n)]
    visited[0][0] = True

    cnt = 1

    # 2차원 배열 그래프처럼 쓰려고 인접 노드 정보 저장
    adjacent = [[] * m for _ in range(n)]
    steps = [(-1, 0), (0, -1), (0, 1), (1, 0)]
    for i in range(n):
        for j in range(m):
            tempList = []
            for step in steps:
                newI = i + step[0]
                newJ = j + step[1]
                if newI >= 0 and newI < n and newJ >= 0 and newJ < m:
                    tempList.append((newI, newJ))
            adjacent[i].append(tempList)

    for i in range(n):
        for j in range(m):
            queue = deque([(i, j)])
            while queue:
                node = queue.popleft()
                # 이 flag는 가는 길이 막혔을 때 나타냄(False면 막혔다는 뜻)
                flag = False
                for adjacentNode in adjacent[node[0]][node[1]]:
                    if maze[adjacentNode[0]][adjacentNode[1]] == 1 and not visited[adjacentNode[0]][adjacentNode[1]]:
                        queue.append((adjacentNode[0], adjacentNode[1]))
                        visited[adjacentNode[0]][adjacentNode[1]] = True
                        flag = True
                    if adjacentNode[0] == n-1 and adjacentNode[1] == m-1:
                        cnt += 1
                        print(cnt)
                        return
                if flag == True:
                    cnt += 1
```

### 미로 탈출 해설 답안
```
def escapeMaze2():
    n, m = map(int, input().split())
    graph = []
    for i in range(n):
        graph.append(list(map(int, input().split())))

    dx = [-1, 1, 0, 0]
    dy = [0, 0, -1, 1]

    def bfs(x, y):
        queue = deque()
        queue.append((x, y))
        while queue:
            x, y = queue.popleft()
            for i in range(4):
                nx = x + dx[i]
                ny = y + dy[i]
                if nx < 0 or ny < 0 or nx >= n or ny >= m:
                    continue
                if graph[nx][ny] == 0:
                    continue
                if graph[nx][ny] == 1:
                    graph[nx][ny] = graph[x][y] + 1
                    queue.append((nx, ny))
        return graph[n-1][m-1]

    print(bfs(0, 0))
```
-> 내가 작성한 코드는 미로가 2갈래 길로 나뉘고 깊이가 깊어지면 정확한 최단 경로를 구하지 못함 해설 처럼 큐에 들어갈때마다 노드에 값을 올려주는 식으로 바꿔야함

### 수정 코드
```
def escapeMaze3():
    n, m = map(int, input().split())
    maze = []
    for i in range(n):
        maze.append(list(map(int, input().split())))

    visited = [[False] * m for _ in range(n)]
    visited[0][0] = True

    cnt = 1

    # 2차원 배열 그래프처럼 쓰려고 인접 노드 정보 저장
    adjacent = [[] * m for _ in range(n)]
    steps = [(-1, 0), (0, -1), (0, 1), (1, 0)]
    for i in range(n):
        for j in range(m):
            tempList = []
            for step in steps:
                newI = i + step[0]
                newJ = j + step[1]
                if newI >= 0 and newI < n and newJ >= 0 and newJ < m:
                    tempList.append((newI, newJ))
            adjacent[i].append(tempList)

    for i in range(n):
        for j in range(m):
            queue = deque([(i, j)])
            while queue:
                node = queue.popleft()
                flag = False
                for adjacentNode in adjacent[node[0]][node[1]]:
                    if maze[adjacentNode[0]][adjacentNode[1]] == 1 and not visited[adjacentNode[0]][adjacentNode[1]]:
                        queue.append((adjacentNode[0], adjacentNode[1]))
                        visited[adjacentNode[0]][adjacentNode[1]] = True
                        maze[adjacentNode[0]][adjacentNode[1]] = maze[node[0]][node[1]] + 1
                        flag = True
                    if adjacentNode[0] == n-1 and adjacentNode[1] == m-1:
                        cnt += 1
                        # print(cnt)
                        # return
                if flag == True:
                    cnt += 1
    print(maze[n-1][m-1])
````
-> 그런데 내 코드는 인접 노드 정보를 담는 리스트까지 사용해서 메모리도 그만큼 쓰고 이 리스트 접근하려고 루프를 중첩해서 실행하기때문에 해설처럼 굳이 인접노드 정보 저장하지 않고 step으로 확인하는 느낌으로 하는 것이 더 효율적일듯
