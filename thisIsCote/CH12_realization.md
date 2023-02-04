## Chapter 12 구현 문제
### 럭키 스트레이트
```
def luckyStraight():
    n = input()

    leftNum = 0
    rightNum = 0

    for i in range(len(n)):
        if i < len(n) // 2:
            leftNum += int(n[i])
        else:
            rightNum += int(n[i])

    if leftNum == rightNum:
        print('LUCKY')
    else:
        print('READY')
```

### 문자열 재정렬
```
def sortString():
    S = input()
    charList = []
    numList = []

    for i in range(len(S)):
        if S[i].isalpha() == True:
            charList.append(S[i])
        else:
            numList.append(int(S[i]))

    charList.sort()

    for i in charList:
        print(i, end='')
    print(sum(numList))
```

### 문자열 압축
```
def compressString():
    s = input()
    stringLength = len(s)
    newString = ''

    # i는 몇 개씩 모으는지 나타냄
    for i in range(1, len(s) // 2 + 1):
        pos = 0
        cnt = 1
        while pos < len(s):
            temp = s[pos:pos+i]
            if temp == s[pos + i:pos + 2 * i]:
                pos += i
                cnt += 1
            else:
                if cnt == 1:
                    newString += temp[0]
                    pos += 1
                else:
                    newString += str(cnt) + temp
                    pos += i
                    if pos + i == len(s):
                        break
                cnt = 1

        stringLength = min(stringLength, len(newString))
        newString = ''

    print(stringLength)
```
-> 교재 입출력 예시 5 문제 있음(자르는 방식에 오해가 있었음)

### 문자열 압축 수정 코드
```
def compressString2():
    s = input()
    stringLength = len(s)
    newString = ''

    # i는 몇 개씩 모으는지 나타냄
    for i in range(1, len(s) // 2 + 1):
        pos = 0
        cnt = 1
        while pos < len(s):
            temp = s[pos:pos+i]
            if temp == s[pos + i:pos + 2 * i]:
                pos += i
                cnt += 1
            else:
                if cnt == 1:
                    newString += temp
                    pos += i
                else:
                    newString += str(cnt) + temp
                    pos += i
                    if pos + i == len(s):
                        break
                cnt = 1

        stringLength = min(stringLength, len(newString))
        newString = ''

    print(stringLength)
```
https://school.programmers.co.kr/learn/courses/30/lessons/60057
정답 판정

### 자물쇠와 열쇠(문제해결 실패)⚠️
```
import math
def unlock():
    ##### 리스트 입력 #####
    keyString = input()
    lockString = input()

    keyInt = []
    lockInt = []

    for i in keyString:
        if i.isdigit():
            keyInt.append(int(i))

    for i in lockString:
        if i.isdigit():
            lockInt.append(int(i))

    keyDimension = int(math.sqrt(len(keyInt)))
    lockDimension = int(math.sqrt(len(lockInt)))

    key = [[0] * keyDimension for _ in range(keyDimension)]
    lock = [[0] * lockDimension for _ in range(lockDimension)]

    for i in range(len(keyInt)):
        xPos = i // keyDimension
        yPos = i % keyDimension
        key[xPos][yPos] = keyInt[i]

    for i in range(len(lockInt)):
        xPos = i // lockDimension
        yPos = i % lockDimension
        lock[xPos][yPos] = lockInt[i]
    ##### 여기까지 입력받은 문자열 리스트화 완료 #####

    keyTooth = keyInt.count(1)
    lockHole = lockInt.count(0)

    # 가장 기본 조건 확인(키의 이빨 개수가 충분한지)
    if lockHole > keyTooth:
        print('false')
    else:
        lockLength = int(math.sqrt(lockHole))

        for length in range(lockLength, len(lock)):
            tempList = [[0] * length for _ in range(length)]
            xPos = 0
            for x in range(0, len(lock) - length):
                yPos = 0
                for y in range(0, len(lock) - length):
                    tempList[xPos][yPos] = lock[x][y]
                    yPos += 1
                xPos += 1
```
-> 나는 먼저 lock에서 홈이 있는 부분을 먼저 찾은 다음에 key의 각 부분을 따와서 딱 맞는 부분이 있는지 완전 탐색을 통해 문제 해결을 하려고 했는데 이렇게 하면 문제는 해결하겠지만 연산량도 더 많아져서 비효율적이다. 문제해결 실패의 이유는 1. 비효율적인 알고리즘 2. 행렬 회전 코드 작성 어려움 3. 한 행렬 부분을 따와서 새로운 행렬을 만드는 데 어려움

### 자물쇠와 열쇠 해설 답안 코드
```
def rotate_a_matrix_by_90_degree(a):
    y = len(a)
    x = len(a[0])
    result = [[0] * y for _ in range(x)] # 회전이라서 행과 열의 갯수 서로 바뀜
    for i in range(y):
        for j in range(x):
            result[j][y - i - 1] = a[i][j]
    return result

def check(new_lock):
    lock_length = len(new_lock) // 3
    for i in range(lock_length, lock_length * 2):
        for j in range(lock_length, lock_length * 2):
            if new_lock[i][j] != 1:
                return False
    return True

def solution(key, lock):
    n = len(lock)
    m = len(key)
    new_lock = [[0] * (n * 3) for _ in range(n * 3)]
    # 새로운 자물쇠의 중앙 부분에 기존의 자물쇠 넣기
    for i in range(n):
        for j in range(n):
            new_lock[i + n][j + n] = lock[i][j]
    
    # 4가지 방향에 대해서 확인
    for rotation in range(4):
        key = rotate_a_matrix_by_90_degree(key)
        # n * 2의 이유는 새로운 자물쇠에 기존 자물쇠가 있는 index가 n * 2 -1 이라서 여기까지 탐색
        for x in range(n * 2):
            for y in range(n * 2):
                # 자물쇠에 열쇠를 끼워 넣기
                for i in range(m):
                    for j in range(m):
                        new_lock[x + i][y + j] += key[i][j]
                # 새로운 자물쇠에 열쇠가 정확히 들어맞는지 검사
                if check(new_lock) == True:
                    return True
                # 자물쇠에서 열쇠 다시 빼기
                for i in range(m):
                    for j in range(m):
                        new_lock[x + i][y + j] -= key[i][j]
    return False
```

### 뱀
```
import copy
def snakeGame():
    # 뱀 이동 처리
    def moveSnake():
        tempHead = copy.deepcopy(head)
        tempBody = copy.deepcopy(body)
        # 우선 머리 옮기기
        if direction == 'L':
            head[1] -= 1
        elif direction == 'R':
            head[1] += 1
        elif direction == 'U':
            head[0] -= 1
        elif direction == 'D':
            head[0] += 1

        if head in body or head[0] > n or head[0] < 1 or head[1] > n or head[1] < 1:
            return True

        # 몸통 위치 바꿔주기
        if len(body) > 0:
            for i in range(len(body) - 1, 0, -1):
                body[i] = body[i - 1]
            body[0] = tempHead

        # 사과를 먹었을 때
        if gampeMap[head[0]][head[1]] == 1:
            # 원래 몸 길이가 머리 하나면 이전 머리 위치를 body 에 추가
            if len(tempBody) == 0:
                body.append(tempHead)
            # 원래 몸 길이가 2 이상이면 이전 몸의 끝을 body 에 추가
            else:
                body.append(tempBody[-1])
            gampeMap[head[0]][head[1]] = 0


    n = int(input())  # 보드의 크기 n * n
    # 맵 생성 (1, 1) 부터 출발이라서 n 보다 1 큰 크기로 2차원 배열 생성
    gampeMap = [[0] * (n + 1) for _ in range(n + 1)]

    k = int(input())  # 사과의 개수
    for _ in range(k):
        xPos, yPos = map(int, input().split())
        gampeMap[xPos][yPos] = 1

    l = int(input())  # 뱀 방향 전환 횟수
    moves = []
    for _ in range(l):
        moves.append(list(input().split()))

    direction = 'R'
    time = 0    # 이동 시간
    head = [1, 1]
    body = []
    conflictFlag = False  # 충돌 발생 탐지
    timeStack = 0

    # 방향 전환 데이터 기반으로 뱀 이동 처리
    for i in range(l):
        move = moves[i]
        moveTime = int(move[0]) - timeStack
        timeStack = int(move[0])
        for j in range(moveTime):  # 여기서 move[0]은 이동 시간을 가리킴
            time += 1

            # 충돌 처리
            conflictFlag = moveSnake()
            if conflictFlag:
                break

        if conflictFlag:
            break

        changeDir = move[1]
        if direction == 'L':
            if changeDir == 'D':
                direction = 'U'
            else:
                direction = 'D'
        elif direction == 'R':
            if changeDir == 'D':
                direction = 'D'
            else:
                direction = 'U'
        elif direction == 'U':
            if changeDir == 'D':
                direction = 'R'
            else:
                direction = 'L'
        elif direction == 'D':
            if changeDir == 'D':
                direction = 'L'
            else:
                direction = 'R'

    if conflictFlag == None:
        while True:
            time += 1
            # 충돌 처리
            conflictFlag = moveSnake()
            if conflictFlag:
                break

    print(time)
```

### 뱀 해설 답안 코드
```
def snakeGame2():
    n = int(input())
    k = int(input())
    data = [[0] * (n + 1) for _ in range(n + 1)]
    info = []
    
    for _ in range(k):
        a, b = map(int, input().split())
        data[a][b] = 1
    
    l = int(input())
    for _ in range(l):
        x, c = input().split()
        info.append((int(x), c))
    
    dx = [0, 1, 0, -1]
    dy = [1, 0, -1, 0]
    
    def turn(direction, c):
        if c == "L":
            direction = (direction - 1) % 4
        else:
            direction = (direction + 1) % 4
        return direction

    def simulate():
        x, y = 1, 1
        data[x][y] = 2  # 뱀 존재하는 위치는 2로 표시
        direction = 0
        time = 0
        index = 0
        q = [(x, y)]    # 뱀이 차지하고 있는 위치 정보(꼬리가 앞)
        while True:
            nx = x + dx[direction]
            ny = y + dy[direction]
            
            if 1 <= nx and nx <= n and 1 <= ny and ny <= n and data[nx][ny] != 2:
                if data[nx][ny] == 0:
                    data[nx][ny] = 2
                    q.append((nx, ny))
                    px, py = q.pop(0)
                if data[nx][ny] == 1:
                    data[nx][ny] = 2
                    q.append((nx, ny))
            else:
                time += 1
                break
            x, y = nx, ny
            time += 1
            if index < 1 and time == info[index][0]:
                direction = turn(direction, info[index][1])
                index += 1
        return time
    
    print(simulate())
```

### 기둥과 보 설치
```
def buildBeamColumn():
    # 기둥 설치 가능한지 확인 함수
    def canColumn(x, y):
        if y == 0 or columnPrint[x][y-1] == 1 or beamPrint[x-1][y] == 1 or beamPrint[x][y] == 1:
            return True
        else:
            return False

    # 보 설치 가능한지 확인 함수
    def canBeam(x, y):
        if columnPrint[x][y-1] == 1 or columnPrint[x+1][y-1] == 1 or (beamPrint[x-1][y] == 1 and beamPrint[x+1][y] == 1):
            return True
        else:
            return False

    n = int(input())       # 벽면의 크기
    columnPrint = [[0] * (n + 1) for _ in range(n + 1)]
    beamPrint = [[0] * (n + 1) for _ in range(n + 1)]
    numOfOperations = int(input())
    build_frame = []
    for _ in range(numOfOperations):
        temp = list(map(int, input().split()))
        build_frame.append(temp)

    result = []
    for i in build_frame:
        # 설치 시
        if i[3] == 1:
            # 기둥 설치
            if i[2] == 0:
                if canColumn(i[0], i[1]):
                    columnPrint[i[0]][i[1]] = 1
                    result.append([i[0], i[1], 0])
            # 보 설치
            else:
                if canBeam(i[0], i[1]):
                    beamPrint[i[0]][i[1]] = 1
                    result.append([i[0], i[1], 1])
        # 삭제 시
        else:
            # 기둥 삭제
            if i[2] == 0:
                x, y = i[0], i[1]
                # 우선 삭제
                columnPrint[x][y] = 0
                if columnPrint[x][y+1] == 1:
                    if canColumn(x, y+1):
                        pass
                    else:
                        columnPrint[x][y] = 1
                        continue
                if beamPrint[x][y+1] == 1:
                    if canBeam(x, y+1):
                        pass
                    else:
                        columnPrint[x][y] = 1
                        continue
                if beamPrint[x-1][y+1] == 1:
                    if canBeam(x-1, y+1):
                        pass
                    else:
                        columnPrint[x][y] = 1
                        continue
                result.remove([x, y, 0])
            # 보 삭제
            else:
                x, y = i[0], i[1]
                # 우선 삭제
                beamPrint[x][y] = 0
                if columnPrint[x][y] == 1:
                    if canColumn(x, y):
                        pass
                    else:
                        beamPrint[x][y] = 1
                        continue
                if columnPrint[x+1][y] == 1:
                    if canColumn(x+1, y):
                        pass
                    else:
                        beamPrint[x][y] = 1
                        continue
                if beamPrint[x-1][y] == 1:
                    if canBeam(x-1, y):
                        pass
                    else:
                        beamPrint[x][y] = 1
                        continue
                if beamPrint[x+1][y] == 1:
                    if canBeam(x+1, y):
                        pass
                    else:
                        beamPrint[x][y] = 1
                        continue
                result.remove([x, y, 1])

    result.sort(key=lambda x:(x[0], x[1], x[2]))
    print(result)
```

### 기둥과 보 설치 해설 답안 코드
```
def possible(answer):
    for x, y, stuff in answer:
        if stuff == 0:
            if y == 0 or [x - 1, y, 1] in answer or [x, y, 1] in answer or [x, y - 1, 0] in answer:
                continue
            return False
        elif stuff == 1:
            if [x, y - 1, 0] in answer or [x + 1, y - 1, 0] in answer or ([x-1, y, 1] in answer and [x + 1, y, 1] in answer):
                continue
            return False
    return True

def solution(n, build_frame):
    answer = []
    for frame in build_frame:
        x, y, stuff, operate = frame
        if operate == 0:
            answer.remove([x, y, stuff])
            if not possible(answer):
                answer.append([x, y, stuff])
        if operate == 1:
            answer.append([x, y, stuff])
            if not possible(answer):
                answer.remove([x, y, stuff])
    return sorted(answer)
```
-> 내 코드는 기둥 보 삭제 시 연관된 기둥과 보만을 확인하여 실행하는 코드인 반면 해설 코드는 현재까지의 모든 건축물들을 다 확인하여 구현한 코드, 코드 자체는 해설 코드가 간결하지만 성능은 내 코드가 더 좋지않을까 생각됨

### 치킨 배달
```
def deliverChicken():
    n, m = map(int, input().split())    # n은 지도 크기, m은 최대 치킨집 갯수
    cityMap = []
    for i in range(n):
        cityMap.append(list(map(int, input().split())))

    chickenRestaurants = []
    # 치킨집 위치 찾기
    for i in range(n):
        for j in range(n):
            if cityMap[i][j] == 2:
                chickenRestaurants.append([i, j])

    restToHome = []
    for rest in chickenRestaurants:
        x, y = rest[0], rest[1]
        dist = 0
        for i in range(n):
            for j in range(n):
                if cityMap[i][j] == 1:
                    dist += abs(x - i) + abs(y - j)
        rest.append(dist)

    chickenRestaurants.sort(key=lambda x:x[2])

    finalRest = []
    for i in range(m):
        rest = chickenRestaurants[i]
        finalRest.append([rest[0], rest[1]])

    wholeDist = 0
    for i in range(n):
        for j in range(n):
            if cityMap[i][j] == 1:
                tempDist = 1000
                for rest in finalRest:
                    tempDist = min(tempDist, abs(i - rest[0]) + abs(j - rest[1]))
                wholeDist += tempDist

    print(wholeDist)
```
-> 먼저 치킨집에서의 거리를 구해서 가장 가까운 몇 개의 치킨집을 골라서 집과의 거리를 계산한 코드, test case는 모두 정답이지만 https://www.acmicpc.net/problem/15686 여기서 정답 판정을 받지 못했는데 아마 이 방법으로는 치킨집이 2개 이상일 때 정확한 최소거리를 못구해서 그러는거 같음 그래서 해설 코드처럼 combinations 함수를 활용하여 모든 조합들을 다 비교해가며 최소거리를 구해야 함

### 치킨 배달 해설 답안 코드
```
from itertools import combinations

n, m = map(int, input().split())
chicken, house = [], []

for r in range(n):
    data = list(map(int, input().split()))
    for c in range(n):
        if data[c] == 1:
            house.append((r, c))
        elif data[c] == 2:
            chicken.append((r, c))

candidates = list(combinations(chicken, m))

def get_sum(candidate):
    result = 0
    for hx, hy in house:
        temp = 1e9
        for cx, cy in candidate:
            temp = min(temp, abs(hx - cx) + abs(hy - cy))
        result += temp
    return result

result = 1e9
for candidate in candidates:
    result = min(result, get_sum(candidate))

print(result)
```
