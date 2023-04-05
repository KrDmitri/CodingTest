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
