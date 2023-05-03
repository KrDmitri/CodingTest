## 2020년 상반기 삼성전자 기출문제
### 아기 상어
```
from collections import deque
INF = int(1e9)

def babyShark():
    def findNearestDist(fish):
        sx = sharkPos[0]
        sy = sharkPos[1]
        visited = [[False] * n for _ in range(n)]
        visited[sx][sy] = True
        q = deque()
        q.append((sx, sy, 0))
        while q:
            tempPos = q.popleft()
            sx = tempPos[0]
            sy = tempPos[1]
            visited[sx][sy] = True
            dist = tempPos[2]

            if sx - 1 >= 0 and visited[sx - 1][sy] == False and graph[sx - 1][sy] <= sharkSize:
                q.append((sx - 1, sy, dist + 1))
                if fish[0] == sx - 1 and fish[1] == sy:
                    return dist + 1
            if sx + 1 < n and visited[sx + 1][sy] == False and graph[sx + 1][sy] <= sharkSize:
                q.append((sx + 1, sy, dist + 1))
                if fish[0] == sx + 1 and fish[1] == sy:
                    return dist + 1
            if sy - 1 >= 0 and visited[sx][sy - 1] == False and graph[sx][sy - 1] <= sharkSize:
                q.append((sx, sy - 1, dist + 1))
                if fish[0] == sx and fish[1] == sy - 1:
                    return dist + 1
            if sy + 1 < n and visited[sx][sy + 1] == False and graph[sx][sy + 1] <= sharkSize:
                q.append((sx, sy + 1, dist + 1))
                if fish[0] == sx and fish[1] == sy + 1:
                    return dist + 1

        if len(q) == 0:
            return 0


    # sharkPos와 가장 가까운 먹이 위치를 반환해야 함
    def findNearestFish():
        dist = INF
        tempFishList = []
        for fish in eatableFish:
            tempDist = findNearestDist(fish)
            if tempDist == 0:
                return None, None
            if tempDist < dist:
                dist = tempDist
                tempFishList = []
                tempFishList.append(fish)
            elif tempDist == dist:
                tempFishList.append(fish)
        tempFishList.sort(key=lambda x:(x[0], x[1]))
        fishPos = tempFishList[0]

        for fish in eatableFish:
            if fishPos == fish:
                eatableFish.remove(fish)
        return fishPos, dist


    n = int(input())
    graph = []
    eatableFish = []
    twoSizeFish = []
    threeSizeFish = []
    fourSizeFish = []
    fiveSizeFish = []
    sixSizeFish = []
    for i in range(n):
        tempList = list(map(int, input().split()))
        for j in range(n):
            if tempList[j] == 1:
                eatableFish.append((i, j))
            elif tempList[j] == 2:
                twoSizeFish.append((i, j))
            elif tempList[j] == 3:
                threeSizeFish.append((i, j))
            elif tempList[j] == 4:
                fourSizeFish.append((i, j))
            elif tempList[j] == 5:
                fiveSizeFish.append((i, j))
            elif tempList[j] == 6:
                sixSizeFish.append((i, j))
            elif tempList[j] == 9:
                sharkPos = (i, j)
        graph.append(tempList)

    sharkSize = 2
    sharkStomach = 0
    eatingTime = 0
    graph[sharkPos[0]][sharkPos[1]] = 0

    while len(eatableFish) > 0:
        movePos, moveTime = findNearestFish()
        if movePos == None:
            break

        sharkPos = movePos
        eatingTime += moveTime
        sharkStomach += 1
        if sharkStomach == sharkSize:
            sharkStomach = 0
            sharkSize += 1
            if sharkSize == 3:
                for fish in twoSizeFish:
                    eatableFish.append(fish)
            elif sharkSize == 4:
                for fish in threeSizeFish:
                    eatableFish.append(fish)
            elif sharkSize == 5:
                for fish in fourSizeFish:
                    eatableFish.append(fish)
            elif sharkSize == 6:
                for fish in fiveSizeFish:
                    eatableFish.append(fish)
            elif sharkSize == 7:
                for fish in sixSizeFish:
                    eatableFish.append(fish)

    print(eatingTime)
```
-> 백준의 모든 예시, 사람들이 올린 반례들 다 맞았는데 정답 처리는 안남.. 그래도 내 자랑스러운 코드니깐 업로드

### 아기 상어 수정
```
from collections import deque
INF = int(1e9)

def babyShark():
    def findNearestDist(fish):
        sx = sharkPos[0]
        sy = sharkPos[1]
        visited = [[False] * n for _ in range(n)]
        visited[sx][sy] = True
        q = deque()
        q.append((sx, sy, 0))
        while q:
            tempPos = q.popleft()
            sx = tempPos[0]
            sy = tempPos[1]
            visited[sx][sy] = True
            dist = tempPos[2]

            if sx - 1 >= 0 and visited[sx - 1][sy] == False and graph[sx - 1][sy] <= sharkSize:
                q.append((sx - 1, sy, dist + 1))
                if fish[0] == sx - 1 and fish[1] == sy:
                    return dist + 1
            if sx + 1 < n and visited[sx + 1][sy] == False and graph[sx + 1][sy] <= sharkSize:
                q.append((sx + 1, sy, dist + 1))
                if fish[0] == sx + 1 and fish[1] == sy:
                    return dist + 1
            if sy - 1 >= 0 and visited[sx][sy - 1] == False and graph[sx][sy - 1] <= sharkSize:
                q.append((sx, sy - 1, dist + 1))
                if fish[0] == sx and fish[1] == sy - 1:
                    return dist + 1
            if sy + 1 < n and visited[sx][sy + 1] == False and graph[sx][sy + 1] <= sharkSize:
                q.append((sx, sy + 1, dist + 1))
                if fish[0] == sx and fish[1] == sy + 1:
                    return dist + 1

        if len(q) == 0:
            return 0


    # sharkPos와 가장 가까운 먹이 위치를 반환해야 함
    def findNearestFish():
        dist = INF
        tempFishList = []
        for fish in eatableFish:
            tempDist = findNearestDist(fish)
            if tempDist == 0:
                eatableFish.remove(fish)
                return None, None
            if tempDist < dist:
                dist = tempDist
                tempFishList = []
                tempFishList.append(fish)
            elif tempDist == dist:
                tempFishList.append(fish)
        tempFishList.sort(key=lambda x:(x[0], x[1]))
        fishPos = tempFishList[0]

        for fish in eatableFish:
            if fishPos == fish:
                eatableFish.remove(fish)
        return fishPos, dist


    n = int(input())
    graph = []
    eatableFish = []
    twoSizeFish = []
    threeSizeFish = []
    fourSizeFish = []
    fiveSizeFish = []
    sixSizeFish = []
    for i in range(n):
        tempList = list(map(int, input().split()))
        for j in range(n):
            if tempList[j] == 1:
                eatableFish.append((i, j))
            elif tempList[j] == 2:
                twoSizeFish.append((i, j))
            elif tempList[j] == 3:
                threeSizeFish.append((i, j))
            elif tempList[j] == 4:
                fourSizeFish.append((i, j))
            elif tempList[j] == 5:
                fiveSizeFish.append((i, j))
            elif tempList[j] == 6:
                sixSizeFish.append((i, j))
            elif tempList[j] == 9:
                sharkPos = (i, j)
        graph.append(tempList)

    sharkSize = 2
    sharkStomach = 0
    eatingTime = 0
    graph[sharkPos[0]][sharkPos[1]] = 0

    while len(eatableFish) > 0:
        movePos, moveTime = findNearestFish()
        if movePos == None:
            continue

        sharkPos = movePos
        eatingTime += moveTime
        sharkStomach += 1
        if sharkStomach == sharkSize:
            sharkStomach = 0
            sharkSize += 1
            if sharkSize == 3:
                for fish in twoSizeFish:
                    eatableFish.append(fish)
            elif sharkSize == 4:
                for fish in threeSizeFish:
                    eatableFish.append(fish)
            elif sharkSize == 5:
                for fish in fourSizeFish:
                    eatableFish.append(fish)
            elif sharkSize == 6:
                for fish in fiveSizeFish:
                    eatableFish.append(fish)
            elif sharkSize == 7:
                for fish in sixSizeFish:
                    eatableFish.append(fish)

    print(eatingTime)
```
-> 처음 코드는 가장 가까운 먹이를 먹지 못할 때 바로 break를 해서 그 다음 먹이를 먹을 수 있음에도 프로그램이 종료되는 일이 있을 수 있었는데, 이번 코드에서 그 가능성을 제거해주었다. 그런데 백준에서는 시간 초과가 나서 결국 정답 판정을 못받았다.

### 아기 상어 해설 답안 코드
```
from collections import deque
INF = 1e9

n = int(input())

array = []
for i in range(n):
    array.append(list(map(int, input().split())))

now_size = 2
now_x, now_y = 0, 0

for i in range(n):
    for j in range(n):
        if array[i][j] == 9:
            now_x, now_y = i, j
            array[now_x][now_y] = 0

dx = [-1, 0, 1, 0]
dy = [0, 1, 0, -1]

def bfs():
    dist = [[-1] * n for _ in range(n)]
    q = deque([(now_x, now_y)])
    dist[now_x][now_y] = 0
    
    while q:
        x, y = q.popleft()
        for i in range(4):
            nx = x + dx[i]
            ny = y + dy[i]
            if 0 <= nx and nx < n and 0 <= ny and ny < n:
                if dist[nx][ny] == -1 and array[nx][ny] <= now_size:
                    dist[nx][ny] = dist[x][y] + 1
                    q.append((nx, ny))
    return dist

def find(dist):
    x, y = 0, 0
    min_dist = INF
    for i in range(n):
        for j in range(n):
            if dist[i][j] != -1 and 1 <= array[i][j] and array[i][j] < now_size:
                if dist[i][j] < min_dist:
                    x, y = i, j
                    min_dist = dist[i][j]
    
    if min_dist == INF:
        return None
    else:
        return x, y, min_dist

result = 0
ate = 0

while True:
    value = find(bfs())
    if value == None:
        print(result)
        break
    else:
        now_x, now_y = value[0], value[1]
        result += value[2]
        array[now_x][now_y] = 0
        ate += 1
        if ate >= now_size:
            now_size += 1
            ate = 0
```
-> 기본적인 개념은 내 코드랑 비슷한데, 다만 구현에 있어서 나보다 더 효율적으로 작동하게끔 함. 특히 인상적인 부분은 value = find(bfs())인데, 한 함수를 다른 한 함수의 인자로 주었다는 부분, 그리고 가장 가까운 거리를 구할 때마다 리스트를 새로 갱신하여 넘겨주었다는 부분

### 청소년 상어
```
import copy
def adolescentShark():
    def searchMovablePosForShark(seaMap, sharkPos, sharkDir):
        newPosX = sharkPos[0]
        newPosY = sharkPos[1]
        movableList = []
        for i in range(3):
            newPosX = newPosX + dx[sharkDir - 1]
            newPosY = newPosY + dy[sharkDir - 1]
            if newPosX < 0 or newPosX > 3 or newPosY < 0 or newPosY > 3 or seaMap[newPosX][newPosY] == [None, None]:
                continue
            else:
                movableList.append([newPosX, newPosY])
        return movableList

    def sharkMove(sharkPos, sharkDir, sharkStomach, seaMap):
        movableList = searchMovablePosForShark(seaMap, sharkPos, sharkDir)
        if len(movableList) == 0:
            stomachCandidate.append(sharkStomach)
            return
        else:
            firstSharkStomach = sharkStomach
            firstSeaMap = copy.deepcopy(seaMap)
            for movePos in movableList:
                if movePos == [0, 2]:
                    print('', end='')
                sharkPos = [movePos[0], movePos[1]]
                sharkStomach += seaMap[movePos[0]][movePos[1]][0]
                sharkDir = seaMap[movePos[0]][movePos[1]][1]
                seaMap[movePos[0]][movePos[1]] = [None, None]
                fishMove(seaMap, sharkPos)
                sharkMove(sharkPos, sharkDir, sharkStomach, seaMap)
                # 다시 원상 복구
                sharkStomach = firstSharkStomach
                seaMap = copy.deepcopy(firstSeaMap)

    # 물고기 이동하는 위치 탐색 함수
    # 이 함수는 for i in range(8): newPos = pos + dx[direction + i] 이런식으로 바꿀 수 있을듯
    def searchMovablePosForFish(pos, direction, sharkPos):
        firstDir = direction
        xPos = pos[0]
        yPos = pos[1]
        newPosX = xPos + dx[direction - 1]
        newPosY = yPos + dy[direction - 1]
        if newPosX >= 0 and newPosX < 4 and newPosY >= 0 and newPosY < 4 and not (newPosX == sharkPos[0] and newPosY == sharkPos[1]):
            return [newPosX, newPosY], direction
        else:
            for i in range(7):
                direction += 1
                direction %= 8
                newPosX = xPos + dx[direction - 1]
                newPosY = yPos + dy[direction - 1]
                if newPosX >= 0 and newPosX < 4 and newPosY >= 0 and newPosY < 4 and not (newPosX == sharkPos[0] and newPosY == sharkPos[1]):
                    return [newPosX, newPosY], direction
        return pos, firstDir

    # 물고기 위치 이동
    def fishMove(seaMap, sharkPos):
        # 1~16번까지의 물고기가 이동
        for fishNum in range(1, 17):
            if fishNum == 3:
                print('', end='')
            fishPos = [-1, -1]
            # 각 번호의 물고기 위치 탐색
            flag = 0
            for i in range(4):
                for j in range(4):
                    if seaMap[i][j][0] == fishNum:
                        fishPos = [i, j]
                        flag = 1
                        break
                if flag == 1:
                    break
            # 물고기가 없으면, 이미 상어가 먹었다는 뜻이므로 다음 물고기 탐색
            if fishPos == [-1, -1]:
                continue
            # 본격적 물고기 위치 이동 및 데이터 업데이트
            movePos, moveDir = searchMovablePosForFish(fishPos, seaMap[fishPos[0]][fishPos[1]][1], sharkPos)
            tempVal = seaMap[movePos[0]][movePos[1]]
            seaMap[movePos[0]][movePos[1]] = [fishNum, moveDir]
            seaMap[fishPos[0]][fishPos[1]] = tempVal


    seaMap = [[] * 4 for _ in range(4)]
    for i in range(4):
        tempList = list(map(int, input().split()))
        seaMap[i].append([tempList[0], tempList[1]])
        seaMap[i].append([tempList[2], tempList[3]])
        seaMap[i].append([tempList[4], tempList[5]])
        seaMap[i].append([tempList[6], tempList[7]])

    # 인덱스 순으로 물고기 및 상어의 이동 방향
    dx = [-1, -1, 0, 1, 1, 1, 0, -1]
    dy = [0, -1, -1, -1, 0, 1, 1, 1]

    # 상어 처음 위치 및 먹은 물고기 갱신
    sharkPos = [0, 0]
    sharkSize = seaMap[0][0][0]
    sharkDir = seaMap[0][0][1]
    seaMap[0][0] = [None, None]

    # 물고기 이동
    fishMove(seaMap, sharkPos)

    sharkStomach = 0
    stomachCandidate = []
    sharkMove(sharkPos, sharkDir, sharkStomach, seaMap)

    sharkSize += max(stomachCandidate)
    print(sharkSize)
```
-> 문제 해결 성공!! 디버깅 하느라 시간이 많이 걸렸는데, 원인은 dfs 재귀형으로 문제를 푸는데, 사용하는 재귀 함수들에 필요한 인자들을 제대로 전해주지 않았기 때문. 앞으로는 dfs 재귀 사용시 인자를 잘 전달해주자...

### 청소년 상어 해설 답안 코드
```
import copy

array = [[None] * 4 for _ in range(4)]

for i in range(4):
    data = list(map(int ,input().split()))
    for j in range(4):
        array[i][j] = [data[j * 2], data[j * 2 + 1] - 1]

dx = [-1, -1, 0, 1, 1, 1, 0, -1]
dy = [0, -1, -1, -1, 0, 1, 1, 1]

def turn_left(direction):
    return (direction + 1) % 8

result = 0

def find_fish(array, index):
    for i in range(4):
        for j in range(4):
            if array[i][j][0] == index:
                return (i, j)
    return None

def move_all_fishes(array, now_x, now_y):
    for i in range(1, 17):
        position = find_fish(array, i)
        if position != None:
            x, y = position[0], position[1]
            direction = array[x][y][1]
            for j in range(8):
                nx = x + dx[direction]
                ny = y + dy[direction]
                if 0 <= nx and nx < 4 and 0 <= ny and ny < 4:
                    if not (nx == now_x and ny == now_y):
                        array[x][y][1] = direction
                        array[x][y], array[nx][ny] = array[nx][ny], array[x][y]
                        break
                direction = turn_left(direction)

def get_possible_positions(array, now_x, now_y):
    positions = []
    direction = array[now_x][now_y][1]
    for i in range(4):
        now_x += dx[direction]
        now_y += dy[direction]
        if 0 <= now_x and now_x < 4 and 0 <= now_y and now_y <4:
            if array[now_x][now_y][0] != -1:
                positions.append((now_x, now_y))
    return positions

def dfs(array, now_x, now_y, total):
    global result
    array = copy.deepcopy(array)
    
    total += array[now_x][now_y][0]
    array[now_x][now_y][0] = -1
    
    move_all_fishes(array, now_x, now_y)
    
    positions = get_possible_positions(array, now_x, now_y)
    if len(positions) == 0:
        result = max(result, total)
        return 
    for next_x, next_y in positions:
        dfs(array, next_x, next_y, total)

dfs(array, 0, 0, 0)
print(result)
```
-> 전체적인 문제 풀이 방법은 내 풀이 방법이랑 비슷하다. 하지만 코드 부분을 조금 더 함수로 나누어 보다 더 효율적이고 가독성이 좋은 코드다.

### 어른 상어
```
def adultShark():
    # 각 상어들이 어느 위치로 이동할 것인지를 계산하는 함수
    def sharkMove():
        movePosition = []
        # 상어 1 번 부터 끝 번 까지
        for i in range(1, m + 1):
            if sharkAlive[i - 1] == False:
                movePosition.append(None)
                continue

            curSharkPos = sharkPos[i - 1]
            xpos = curSharkPos[0]
            ypos = curSharkPos[1]
            curSharkDir = sharkDir[i - 1]
            curSharkPriority = priorityMap[i - 1][curSharkDir - 1]
            moveFlag = True
            for dir in curSharkPriority:
                dir = dir - 1
                nxpos = xpos + dx[dir]
                nypos = ypos + dy[dir]
                # 이동할 수 있는 조건은 냄새 x, 만약 이동하는 곳에 숫자가 낮은 상어 있으면 삭제 처리
                if nxpos >= 0 and nxpos < n and nypos >= 0 and nypos < n:
                    if smellMap[nxpos][nypos][1] == 0:  # 냄새가 없어서 이동 가능한 경우
                        movePosition.append([nxpos, nypos, dir + 1])
                        moveFlag = False
                        break


            if moveFlag:  # 만약 네 방향 다 냄새가 있어서 이동을 못했을 경우 자기 냄새 쪽으로 이동
                for dir in curSharkPriority:
                    dir = dir - 1
                    nxpos = xpos + dx[dir]
                    nypos = ypos + dy[dir]
                    if nxpos >= 0 and nxpos < n and nypos >= 0 and nypos < n:
                        if smellMap[nxpos][nypos][0] == i:
                            movePosition.append([nxpos, nypos, dir + 1])
                            break

        return movePosition

    # seaMap 데이터 업데이트 코드 필요
    def updateData(movePosition, numOfShark, sharkPos, sharkDir, smellMap, seaMap):
        for i in range(len(movePosition)):
            if sharkAlive[i] == False:
                continue

            xpos = sharkPos[i][0]
            ypos = sharkPos[i][1]

            nxpos = movePosition[i][0]
            nypos = movePosition[i][1]
            newDir = movePosition[i][2]
            # 이동 가능 할 때
            if smellMap[nxpos][nypos][1] != k:
                seaMap[xpos][ypos] = 0
                seaMap[nxpos][nypos] = i + 1
                smellMap[nxpos][nypos] = [i + 1, k]
                sharkDir[i] = newDir
                sharkPos[i] = [nxpos, nypos]
            # 이동한 곳에 다른 우선 순위가 높은 상어가 있을 때
            elif smellMap[nxpos][nypos][1] == k:
                seaMap[xpos][ypos] = 0
                numOfShark -= 1
                sharkAlive[i] = False
                sharkPos[i] = None
                sharkDir[i] = None

        return numOfShark, sharkPos, sharkDir, smellMap, seaMap


    def smellDisappear():
        for i in range(n):
            for j in range(n):
                if smellMap[i][j][1] != 0:
                    smellMap[i][j][1] -= 1


    # n은 격자의 크기, m은 상어의 수, k는 상어의 냄새가 얼마나 오래 가는지
    n, m, k = map(int, input().split())
    seaMap = []
    smellMap = [[[0, 0]] * n for _ in range(n)]
    priorityMap = []
    sharkPos = []
    dx = [-1, 1, 0, 0]
    dy = [0, 0, -1, 1]
    numOfShark = m
    timeCount = 0
    sharkAlive = [True] * m

    # 상어와 바다 지도의 데이터 받음
    for _ in range(n):
        seaMap.append(list(map(int, input().split())))

    # 1 번 부터 m 번 까지 상어의 위치를 sharkPos 리스트에 담기 위한 코드(아마 몇 번째 index 에 삽입하는 함수 활용해서 시간 복잡도 줄일 수 있을듯)
    for sharkNum in range(1, m + 1):
        for i in range(n):
            flag = False
            for j in range(n):
                if seaMap[i][j] == sharkNum:
                    sharkPos.append((i, j))
                    flag = True
                    break
            if flag:
                break

    # 상어 초기 방향 데이터 입력
    sharkDir = list(map(int, input().split()))

    # 상어 이동 우선순위 기록
    for _ in range(m):
        tempMap = []
        for _ in range(4):
            tempMap.append(list(map(int, input().split())))
        priorityMap.append(tempMap)

    ### 위에까지 문제 해결에 필요한 데이터, 자료구조 생성
    # 여기서부터 본격적 문제 해결
    # 순서
    # 1. 우선 순위에 따른 상어 이동 위치 확인
    # 2. 상어 위치 이동, 상어 방향 데이터 업데이트, 겹치는 상어 있나 확인
    # 3. 냄새 데이터 업데이트

    for i in range(len(sharkPos)):
        tempPos = sharkPos[i]
        xpos = tempPos[0]
        ypos = tempPos[1]
        smellMap[xpos][ypos] = [i + 1, k]

    while numOfShark != 1:
        if timeCount > 1000:
            break
        movePosition = sharkMove()
        smellDisappear()
        numOfShark, sharkPos, sharkDir, smellMap, seaMap = updateData(movePosition, numOfShark, sharkPos, sharkDir, smellMap, seaMap)
        timeCount = timeCount + 1

    if timeCount > 1000:
        print(-1)
    else:
        print(timeCount)
```
-> 3일에 걸쳐서 푼 문제..!! 그래도 고군분투 한 만큼 보람찬 문제다. 되짚어보자면, 로직은 정확했으나 리스트의 인덱스를 잘못 사용하는 바람에 원하는 쪽으로 프로그램이 동작하지 않았고 디버깅 시 해당 오류를 찾는 것도 오래걸렸다. 또한 처음 문제를 풀 때 바로 코드로 구현하는 것만 신경을 썼는데, 잘 풀리지 않자 플로우 차트로 대강 프로그램의 흐름을 나타내고 나서 어떤 부분이 오작동했는지를 쉽게 알 수 있었다.
- 교훈 1 : 리스트의 값을 이용할 때에는 그것의 index에 주의하자
- 교훈 2 : 어떤 프로그램을 짤 때 대강이라도 플로우차트를 그리고 
