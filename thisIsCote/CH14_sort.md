## Chapter 14 정렬 문제
### 국영수
```
def GukYoungSoo():
    n = int(input())
    dataTable = []

    for i in range(n):
        dataTable.append(list(input().split()))
        for j in range(1, 4):
            dataTable[i][j] = int(dataTable[i][j])

    dataTable.sort(key = lambda x : (-x[1], x[2], -x[3], x[0]))

    for i in range(n):
        print(dataTable[i][0])
```
-> lambda함수 사용법, 오름차순은 정부호, 내림차순은 음부호 기억하기

### 안테나
```
def antenna():
    n = int(input())
    houses = list(map(int, input().split()))

    distance = [0] * n

    for i in range(n):
        for house in houses:
            distance[i] += abs(houses[i] - house)

    minNum = min(distance)

    for i in range(n):
        if minNum == distance[i]:
            print(houses[i])
            break
```
-> 답은 맞는데 시간 초과남, 첫 반복문 내 시간 절약 필요할듯

### 안테나 수정
```
def antenna():
    n = int(input())
    houses = list(map(int, input().split()))
    houses.sort()

    distance = [0] * n
    minNum = 1e9

    for i in range(n):
        flag = 0
        for house in houses:
            distance[i] += abs(houses[i] - house)
            if distance[i] > minNum:
                print(houses[i - 1])
                flag = 1
                break
        minNum = min(minNum, distance[i])
        if flag == 1:
            break
```
-> 내 생각에는 틀릴 일이 없는데 백준 사이트에서는 틀렸다고 나오고 어떤 예시에서 틀리는지 잘 모르겠음

### 안테나 해설 답안 코드
```
def antenna2():
    n = int(input())
    data = list(map(int, input().split()))
    data.sort()

    print(data[(n - 1) // 2])
```

### 실패율
```
def solution(N, stages):
    stages.sort()
    failPercent = [0] * N
    for i in range(N):
        num = 0
        for stage in stages:
            if stage == i + 1:
                num += 1
        failPercent[i] = (num / len(stages), i + 1)
        for _ in range(num):
            stages.pop(0)

    failPercent.sort(reverse=True, key=lambda x:x[0])


    answer = []
    for i in failPercent:
        answer.append(i[1])

    return answer
```
-> 답은 맞는데 몇 개 예시에서 시간 초과남

### 실패율 수정
```
def solution(N, stages):
    stages.sort()
    failPercent = [0] * N

    for i in range(N):
        num = 0
        for j in range(len(stages)):
            if stages[j] == i + 1:
                num += 1
            else:
                break
        failPercent[i] = (num / len(stages), i + 1)
        for _ in range(num):
            stages.pop(0)

    failPercent.sort(reverse=True, key=lambda x:x[0])

    answer = []
    for i in failPercent:
        answer.append(i[1])

    return answer
```
-> break 코드 추가로 시간 절약했다고 생각했는데 여전히 런타임 오류

### 실패율 해설 답안 코드
```
def solution(N, stages):
    answer = []
    length = len(stages)
    
    for i in range(1, N + 1):
        count = stages.count(i)
        if length == 0:
            fail = 0
        else:
            fail = count / length
        answer.append((i, fail))
        length -= count
    
    answer = sorted(answer, key=lambda x:x[1], reverse=True)
    
    answer = [i[0] for i in answer]
    return answer
```
