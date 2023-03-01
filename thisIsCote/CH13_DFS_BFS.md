## Chapter 13 DFS/BFS 문제
### 특정 거리의 도시 찾기
```
from collections import deque
INF = 1e9

def findCity():
    n, m, k, x = map(int, input().split())
    roads = []
    visited = [False] * (n + 1)
    distance = [INF] * (n + 1)

    for _ in range(m):
        a, b = map(int, input().split())
        roads.append((a, b))
    q = deque([])
    q.append(x)
    visited[x] = True
    distance[x] = 0
    while q:
        curCity = q.popleft()
        for road in roads:
            if road[0] == curCity:
                if not visited[road[1]]:
                    q.append(road[1])
                    visited[road[1]] = True
                    distance[road[1]] = distance[curCity] + 1

    for i in range(len(distance)):
        if distance[i] == k:
            print(i)

    if k not in distance:
        print(-1)
```
-> 값은 맞는데 백준에 코드 제출시 시간 초과로 뜸, 아마 도로의 개수 M이 1,000,000개라서 이것들을 하나하나 다 저장해서 초과 나는듯, 1 -> (2, 3) 이런 느낌으로 코드 수정해봄

```
def findCity2():
    n, m, k, x = map(int, input().split())
    roads = [[] for _ in range(n + 1)]
    visited = [False] * (n + 1)
    distance = [INF] * (n + 1)

    for _ in range(m):
        a, b = map(int, input().split())
        roads[a].append(b)

    q = deque([])
    q.append(x)
    visited[x] = True
    distance[x] = 0
    while q:
        curCity = q.popleft()
        road = roads[curCity]
        for i in road:
            if not visited[i]:
                q.append(i)
                visited[i] = True
                distance[i] = distance[curCity] + 1

    for i in range(len(distance)):
        if distance[i] == k:
            print(i)

    if k not in distance:
        print(-1)
```
-> 그래도 시간 초과남..ㅎㅎ

```
import sys
from collections import deque
input = sys.stdin.readline
INF = int(1e7)


n, m, k, x = map(int, input().split())
cities = [[] for _ in range(n+1)]
d = [INF for _ in range(n+1)]
d[x] = 0
for _ in range(m):
    i, c = map(int, input().split())
    cities[i].append(c)
q = deque([x])

while q:
    i = q.popleft()
    if d[i] >= k:
        break
    for city in cities[i]:
        if d[city] == INF:
            d[city] = d[i]+1
            q.append(city)

no = True
for i in range(1, n+1):
    if d[i] == k:
        no = False
        print(i)

if no:
    print(-1)
```
-> 답안 코드도 내 코드랑 비슷한데, 위 코드처럼 현재 탐색중인 노드가 k 이상일 때는 그 다음 노드까지의 거리가 항상 k보다 크기 때문에 탐색할 필요가 없기 때문에, 이같은 방식으로 시간을 절약할 수 있음

### 연구소
```
from itertools import combinations
import copy

def laboratory():
    def spreadVirus(tempGraph, tempVisited, vx, vy):
        if tempGraph[vx][vy] != 1:
            tempGraph[vx][vy] = 2
            tempVisited[vx][vy] = True
            if vx - 1 >= 0 and tempVisited[vx-1][vy] == False and tempGraph[vx-1][vy] != 1:
                spreadVirus(tempGraph, tempVisited, vx - 1, vy)
            if vx + 1 < n and tempVisited[vx + 1][vy] == False and tempGraph[vx+1][vy] != 1:
                spreadVirus(tempGraph, tempVisited, vx + 1, vy)
            if vy - 1 >= 0 and tempVisited[vx][vy-1] == False and tempGraph[vx][vy-1] != 1:
                spreadVirus(tempGraph, tempVisited, vx, vy - 1)
            if vy + 1 < m and tempVisited[vx][vy+1] == False and tempGraph[vx][vy+1] != 1:
                spreadVirus(tempGraph, tempVisited, vx, vy + 1)
        return


    n, m = map(int, input().split())
    graph = []
    virus = []
    blank = []
    visited = [[False] * m for _ in range(n)]

    for i in range(n):
        row = list(map(int, input().split()))
        graph.append(row)
        for j in range(len(row)):
            if row[j] == 2:
                virus.append((i, j))
            elif row[j] == 0:
                blank.append((i, j))
            elif row[j] == 1:
                visited[i][j] = True

    protectWalls = combinations(blank, 3)

    safeArea = 0
    for protectWall in protectWalls:
        tempGraph = copy.deepcopy(graph)
        tempVisited = copy.deepcopy(visited)
        for wall in protectWall:
            tempGraph[wall[0]][wall[1]] = 1

        for eachVirus in virus:
            flag = False
            for i in range(n):
                for j in range(m):
                    if tempGraph[i][j] == 0:
                        flag = True

            if flag:
                spreadVirus(tempGraph, tempVisited, eachVirus[0], eachVirus[1])

        tempSafe = 0
        for i in range(n):
            for j in range(m):
                if tempGraph[i][j] == 0:
                    tempSafe += 1

        safeArea = max(safeArea, tempSafe)

    print(safeArea)
```
-> 맞긴 맞았는데, 소요시간이 다른 사람보다 평균 3-4배 더 걸림

### 연구소 해설 답안 코드
```
def laboratory2():
    n, m = map(int, input().split())
    data = []
    temp = [[0] * m for _ in range(n)]

    for _ in range(n):
        data.append(list(map(int, input().split())))

    dx = [-1, 0, 1, 0]
    dy = [0, 1, 0, -1]

    result = 0

    def virus(x, y):
        for i in range(4):
            nx = x + dx[i]
            ny = y + dy[i]
            if nx >= 0 and nx < n and ny >= 0 and ny < m:
                if temp[nx][ny] == 0:
                    temp[nx][ny] = 2
                    virus(nx, ny)

    def get_score():
        score = 0
        for i in range(n):
            for j in range(m):
                if temp[i][j] == 0:
                    score += 1
        return score

    def dfs(count):
        global result
        if count == 3:
            for i in range(n):
                for j in range(m):
                    temp[i][j] = data[i][j]
            for i in range(n):
                for j in range(m):
                    if temp[i][j] == 2:
                        virus(i, j)
            result = max(result, get_score())
            return
        for i in range(n):
            for j in range(m):
                if data[i][j] == 0:
                    data[i][j] = 1
                    count += 1
                    dfs(count)
                    data[i][j] = 0
                    count -= 1

    dfs(0)
    print(result)
```

### 경쟁적 전염
```
def competitiveInfection():
    def oneMove(virusType, i, j):
        if i > 0 and testTube[i - 1][j] == 0:
            testTube[i - 1][j] = virusType
        if i < n - 1 and testTube[i + 1][j] == 0:
            testTube[i + 1][j] = virusType
        if j > 0 and testTube[i][j - 1] == 0:
            testTube[i][j - 1] = virusType
        if j < n - 1 and testTube[i][j + 1] == 0:
            testTube[i][j + 1] = virusType

    def moveVirus():
        for virusType in range(1, k + 1):
            tempVirusList = []
            for i in range(n):
                for j in range(n):
                    if testTube[i][j] == virusType:
                        tempVirusList.append((i, j))
            for tempVirus in tempVirusList:
                i = tempVirus[0]
                j = tempVirus[1]
                oneMove(virusType, i, j)

    n, k = map(int, input().split())   # n은 시험관 크기(n * n), k는 바이러스 종류(1 ~ k)
    testTube = []    # 시험관 내부 정보

    for _ in range(n):
        testTube.append(list(map(int, input().split())))

    s, x, y = map(int, input().split())  # s초 후에 (x, y)의 바이러스 종류 출력

    for _ in range(s):
        moveVirus()

    print(testTube[x - 1][y - 1])
```
-> 테스트 예시들은 다 맞았는데 백준 사이트에서 시간이 초과남, 위에 사용된 반복문들을 더 효율적이게 수정 요망

### 경쟁적 전염 해설 답안 코드
```
from collections import deque

def competitiveInfection2():
    n, k = map(int, input().split())

    graph = []   # 전체 보드 정보
    data = []    # 바이러스 정보

    for i in range(n):
        graph.append(list(map(int, input().split())))
        for j in range(n):
            if graph[i][j] != 0:
                data.append((graph[i][j], 0, i, j))   # 바이러스 종류, 시간, 위치 저장

    data.sort()
    q = deque(data)

    target_s, target_x, target_y = map(int, input().split())

    dx = [-1, 0, 1, 0]
    dy = [0, 1, 0, -1]

    while q:
        virus, s, x, y = q.popleft()
        if s == target_s:
            break
        for i in range(4):
            nx = x + dx[i]
            ny = y + dy[i]
            if 0 <= nx and nx < n and 0 <= ny and ny < n:
                if graph[nx][ny] == 0:
                    graph[nx][ny] = virus
                    q.append((virus, s + 1, nx, ny))

    print(graph[target_x - 1][target_y - 1])
```

### 괄호 변환
```
def changeKuohao(w):
    if len(w) == 0:
        return ''
    u = []
    v = []

    def separateString(w):
        leftNum = 0
        rightNum = 0
        for char in w:
            if leftNum == rightNum and leftNum != 0:
                v.append(char)
                continue
            if char == '(':
                leftNum += 1
                u.append(char)
            else:
                rightNum += 1
                u.append(char)

    def checkRightStr(u):
        stack = []
        for char in u:
            if char == '(':
                stack.append(char)
            else:
                if len(stack) > 0:
                    stack.pop()
                else:
                    return False
        if len(stack) == 0:
            return True

    separateString(w)
    if checkRightStr(u):
        result = changeKuohao(v)   # 결과 붙이는 코드 추가 필요
        for char in result:
            u.append(char)
        return u
    else:
        temp = []
        temp.append('(')
        result = changeKuohao(v)   # temp에 결과 붙여야 함
        for char in result:
            temp.append(char)
        temp.append(')')
        u.pop(0)
        u.pop(-1)
        for i in range(len(u)):
            if u[i] == '(':
                temp.append(')')
            else:
                temp.append('(')
        return temp

### test code ###

w = input()
result = changeKuohao(w)
for char in result:
    print(char, end='')
```

### 괄호 변환 해설 답안 코드
```
def balanced_index(p):
    count = 0
    for i in range(len(p)):
        if p[i] == '(':
            count += 1
        else:
            count -= 1
            if count == 0:
                return i

def check_proper(p):
    count = 0
    for i in p:
        if i == '(':
            count += 1
        else:
            if count == 0:
                return False
            count -= 1
    return True

def solution(p):
    answer = ''
    if p == '':
        return answer
    index = balanced_index(p)
    u = p[:index + 1]
    v = p[index + 1:]
    if check_proper(u):
        answer = u + solution(v)
    else:
        answer = '('
        answer += solution(v)
        answer += ')'
        u = list(u[1:-1])
        for i in range(len(u)):
            if u[i] == '(':
                u[i] = ')'
            else:
                u[i] = '('
        answer += "".join(u)
    return answer
```

### 연산자 끼워 넣기
```
import itertools
INF = 1e9

def insertOperand():
    def operate(char, a, b):
        if char == '+':
            return a + b
        elif char == '-':
            return a - b
        elif char == '*':
            return a * b
        elif char == '/':
            if a <= 0:
                a *= -1
                result = a // b
                return result * (-1)
            else:
                return a // b

    n = int(input())
    numList = list(map(int, input().split()))
    operandInfo = list(map(int, input().split()))
    operandStr = []
    for _ in range(operandInfo[0]):
        operandStr.append('+')
    for _ in range(operandInfo[1]):
        operandStr.append('-')
    for _ in range(operandInfo[2]):
        operandStr.append('*')
    for _ in range(operandInfo[3]):
        operandStr.append('/')

    possibleList = itertools.permutations(operandStr)
    minNum = INF
    maxNum = INF * (-1)

    for operands in possibleList:
        result = numList[0]
        for i in range(len(operands)):
            result = operate(operands[i], result, numList[i + 1])
        minNum = min(minNum, result)
        maxNum = max(maxNum, result)

    print(maxNum)
    print(minNum)
```
-> 중간의 possibleList에는 중복이 되는 값들이 많이 포함되어 있어서 해당 리스트 자료형을 집함 자료형으로 바꿔준 뒤 다시 리스트 형태로 바꿔줌으로써 백준 사이트에서 시간 초과 문제를 해결할 수 있었음

### 감시 피하기
```
import itertools
import copy

def avoidSurveillance():
    def checkLeft(x, y, tempMap):
        if tempMap[x][y] == 'S':
            return False
        elif tempMap[x][y] == 'O':
            return True
        else:
            if y - 1 >= 0:
                return checkLeft(x, y - 1, tempMap)
            else:
                return True
    def checkRight(x, y, tempMap):
        if tempMap[x][y] == 'S':
            return False
        elif tempMap[x][y] == 'O':
            return True
        else:
            if y + 1 <= n - 1:
                return checkRight(x, y + 1, tempMap)
            else:
                return True
    def checkUp(x, y, tempMap):
        if tempMap[x][y] == 'S':
            return False
        elif tempMap[x][y] == 'O':
            return True
        else:
            if x - 1 >= 0:
                return checkUp(x - 1, y, tempMap)
            else:
                return True
    def checkDown(x, y, tempMap):
        if tempMap[x][y] == 'S':
            return False
        elif tempMap[x][y] == 'O':
            return True
        else:
            if x + 1 <= n - 1:
                return checkDown(x + 1, y, tempMap)
            else:
                return True

    n = int(input())
    roadMap = []
    teachers = []
    blanks = []
    for i in range(n):
        tempList = list(input().split())
        for j in range(n):
            if tempList[j] == 'T':
                teachers.append((i, j))
            elif tempList[j] == 'X':
                blanks.append((i, j))
        roadMap.append(tempList)

    obstacleCandidates = itertools.permutations(blanks, 3)

    for obstacleCandidateList in obstacleCandidates:
        tempMap = copy.deepcopy(roadMap)
        possibleFlag = True
        for obstacleCandidate in obstacleCandidateList:
            xpos = obstacleCandidate[0]
            ypos = obstacleCandidate[1]
            tempMap[xpos][ypos] = 'O'
        for teacher in teachers:
            tx = teacher[0]
            ty = teacher[1]
            if checkLeft(tx, ty, tempMap) == False:
                possibleFlag = False
                break
            if checkRight(tx, ty, tempMap) == False:
                possibleFlag = False
                break
            if checkUp(tx, ty, tempMap) == False:
                possibleFlag = False
                break
            if checkDown(tx, ty, tempMap) == False:
                possibleFlag = False
                break
        if possibleFlag == True:
            print('YES')
            return
    print('NO')
```
