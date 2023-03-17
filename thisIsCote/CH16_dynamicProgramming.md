## Chapter 16 다이나픽 프로그래밍 문제
### 금광
```
def miningGold():
    def getMaxGold(gold, row, col, n):
        maxGold = 0
        maxGold = max(maxGold, gold[row][col - 1])
        if row > 0:
            maxGold = max(maxGold, gold[row - 1][col - 1])
        if row < n - 1:
            maxGold = max(maxGold, gold[row + 1][col - 1])
        return maxGold

    T = int(input())
    for _ in range(T):
        n, m = map(int, input().split())
        mine = [[] * m for _ in range(n)]
        gold = [[0] * m for _ in range(n)]
        tempMine = list(map(int, input().split()))
        rowIndex = 0
        for i in range(len(tempMine)):
            if i != 0 and (i % m) == 0:
                rowIndex += 1
            mine[rowIndex].append(tempMine[i])

        for i in range(m):
            if i == 0:
                for j in range(n):
                    gold[j][i] = mine[j][i]
            else:
                for j in range(n):
                    gold[j][i] = getMaxGold(gold, j, i, n) + mine[j][i]

        result = 0
        for i in range(n):
            result = max(result, gold[i][m - 1])
        print(result)
```

### 금광 해설 답안 코드
```
for tc in range(int(input())):
    n, m =map(int, input().split())
    array = list(map(int, input().split()))
    
    dp = []
    index = 0
    for i in range(n):
        dp.append(array[index:index + m])
        index += m
    
    for j in range(1, m):
        for i in range(n):
            if i == 0:
                left_up = 0
            else:
                left_up = dp[i - 1][j - 1]
            if i == n - 1:
                left_down = 0
            else:
                left_down = dp[i + 1][j - 1]
            left = dp[i][j - 1]
            dp[i][j] = dp[i][j] + max(left_up, left_down, left)
    
    result = 0
    for i in range(n):
        result = max(result, dp[i][m - 1])
    print(result)
```
-> 코드의 동작 원리는 같다. 다만, 답안 코드가 함수 사용도 하지않고 인덱싱 기법을 활용하여 속도가 더 빠를 것이다.

### 정수 삼각형
```
def integerTriangle():
    n = int(input())
    triangle = []
    for _ in range(n):
        triangle.append(list(map(int, input().split())))

    for i in range(1, n):
        for j in range(len(triangle[i])):
            if j == 0:
                triangle[i][j] += triangle[i - 1][j]
            elif j == len(triangle[i]) - 1:
                triangle[i][j] += triangle[i - 1][j - 1]
            else:
                triangle[i][j] += max(triangle[i - 1][j - 1], triangle[i - 1][j])
    result = 0
    for i in range(n):
        result = max(result, triangle[n - 1][i])

    print(result)
```

### 퇴사
```
def quitCompany():
    n = int(input())
    wageCalendar = []
    for _ in range(n):
        wageCalendar.append(list(map(int, input().split())))
    updateList = [[0] for _ in range(n)]

    for i in range(n):
        costDay = wageCalendar[i][0] - 1
        if i == 0 and costDay != 0:
            updateList[i] = 0
        elif i == 0 and costDay == 0:
            updateList[i] = wageCalendar[i][1]
        elif len(updateList[i]) == 1:
            updateList[i] = updateList[i - 1]
        else:
            updateList[i] = max(max(updateList[i]), updateList[i - 1])

        if costDay == 0 and i != 0:
            updateList[i] = max(updateList[i], updateList[i - 1] + wageCalendar[i][1])
        if costDay == 0 and i == 0:
            updateList[i] = wageCalendar[i][1]
        elif costDay != 0 and i == 0 and costDay + i < n:
            updateList[costDay + i].append(wageCalendar[i][1])
        elif costDay != 0 and costDay + i < n:
            updateList[costDay + i].append(updateList[i - 1] + wageCalendar[i][1])

    print(updateList[-1])
```
-> 맞긴 맞았는데, 코드가 여러 분기문으로 나뉘어서 가독성이 좀 떨어짐, 답지 확인

### 퇴사 해설 답안 코드
```
n = int(input())
t = []
p = []
dp = [0] * (n + 1)
max_value = 0

for _ in range(n):
    x, y = map(int, input().split())
    t.append(x)
    p.append(y)

for i in range(n - 1, -1, -1):
    time = t[i] + i
    if time <= n:
        dp[i] = max(p[i] + dp[time], max_value)
        max_value = dp[i]
    else:
        dp[i] = max_value

print(max_value)
```
-> 거꾸로 계산하는 방식이라 코드는 더 간편한데 이해하기에는 내 코드가 더 쉬운듯..ㅋㅋ

### 병사 배치하기
```
import copy
from itertools import combinations
from copy import deepcopy

# 랜덤으로 빼는 방식을 다시 다듬어보기
def arrangeSoldiers():
    def isRight(arr):
        for i in range(0, len(arr) - 1):
            if arr[i] < arr[i + 1]:
                return False
        return True

    n = int(input())
    soldiers = list(map(int, input().split()))

    minOut = 0
    maxOut = n
    answers = []

    while minOut <= maxOut:
        midOut = (minOut + maxOut) // 2
        rightFlag = False
        outCandidatesCases = combinations(soldiers, midOut)
        for outCandidatesCase in outCandidatesCases:
            tempSoldiers = copy.deepcopy(soldiers)
            for outSoldier in outCandidatesCase:
                tempSoldiers.remove(outSoldier)
            rightFlag = isRight(tempSoldiers)
            if rightFlag:
                answers.append(midOut)
                break
        if rightFlag:
            maxOut = midOut - 1
        else:
            minOut = midOut + 1

    print(min(answers))
    print(answers)
```
-> 답은 맞는거 같은데 백준에서 시간초과 뜸🥲

### 병사 배치하기 해설 답안 코드
```
n = int(input())
array = list(map(int, input().split()))
array.reverse()

dp = [1] * n

for i in range(1, n):
    for j in range(0, i):
        if array[j] < array[i]:
            dp[i] = max(dp[i], dp[j] + 1)

print(n - max(dp))
```
-> 나는 이진 탐색으로 답이 될 수 있는 범위를 좁혀가며 푼 반면에 답안 코드는 다이나믹 프로그래밍의 '가장 긴 증가하는 부분 수열' 아이디어로 문제 해결을 함

### 못생긴 수
```
def uglyNumber():
    n = int(input())
    numList = [1]
    startIndex = 0
    
    while len(numList) <= 1000:
        for i in range(startIndex, len(numList)):
            if numList[i] * 2 not in numList:
                numList.append(numList[i] * 2)
            if numList[i] * 3 not in numList:
                numList.append(numList[i] * 3)
            if numList[i] * 5 not in numList:
                numList.append(numList[i] * 5)
            startIndex += 1
            
    numList.sort()
    print(numList[n - 1])
```

### 못생긴 수 해설 답안 코드
```
n = int(input())

ugly = [0] * n
ugly[0] = 1

i2 = i3 = i5 = 0
next2, next3, next5 = 2, 3, 5

for l in range(1, n):
    ugly[l] = min(next2, next3, next5)
    if ugly[l] == next2:
        i2 += 1
        next2 = ugly[i2] * 2
    if ugly[l] == next3:
        i3 += 1
        next3 = ugly[i3] * 3
    if ugly[l] == next5:
        i5 += 1
        next5 = ugly[i5] * 5

print(ugly[n - 1])
```
-> 내 코드는 입력 최대 범위가 1000이라서 1000까지의 모든 답을 구한 후 답을 출력한 반면에 해설 답안의 코드는 작은 수부터 차례대로 계산을 해가서 구한 답을 출력하는 코드로 해설 답안의 코드가 조금 더 빠를 것이다.

### 편집 거리 해설 답안 코드
```
def edit_dis(str1, str2):
    n = len(str1)
    m = len(str2)

    dp = [[0] * (m + 1) for _ in range(n + 1)]

    for i in range(1, n + 1):
        dp[i][0] = i
    for j in range(1, m + 1):
        dp[0][j] = j

    for i in range(1, n + 1):
        for j in range(1, m + 1):
            if str1[i - 1] == str2[j - 1]:
                dp[i][j] = dp[i - 1][j - 1]
            else:
                dp[i][j] = 1 + min(dp[i][j - 1], dp[i - 1][j], dp[i - 1][j - 1])

    return dp[n][m]
```
-> 구현은 쉽지만 이 방법을 어떻게 처음부터 생각해내지?!
