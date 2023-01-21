## Chapter 11 그리디 문제
### 모험가 길드
```
def organizeGuild():
    n = int(input())
    horrorIndex = list(map(int, input().split()))
    horrorIndex.sort()

    cnt = 0

    while len(horrorIndex) != 0:
        maxNum = horrorIndex.pop()
        if len(horrorIndex) > maxNum:
            for i in range(maxNum):
                horrorIndex.pop()
        elif maxNum > len(horrorIndex):
            if maxNum == 1:
                cnt += 1
            break
        cnt += 1

    print(cnt)
```
-> 내가 짠 코드는 최소한의 그룹을 만드는 코드로 반대로 해주면 최대한의 그룹을 만드는 코드로 바꿀 수 있음(접근법은 맞다)

### 곱하기 혹은 더하기
```
def checkPlusMultiple():
    n = input()
    result = int(n[0])

    for i in range(1, len(n)):
        result = max(result + int(n[i]), result * int(n[i]))

    print(result)
```

### 문자열 뒤집기
```
def invertStr():
    S = input()
    mark = '0'

    if S[0] == '0':
        mark = '0'
    else:
        mark = '1'

    zeroCount = 0
    oneCount = 0

    for i in range(len(S)):
        if S[i] == mark:
            continue
        else:
            if mark == '0':
                zeroCount += 1
                mark = '1'
            else:
                oneCount += 1
                mark = '0'

    if mark == '0':
        zeroCount += 1
    else:
        oneCount += 1

    print(min(zeroCount, oneCount))
```

### 만들 수 없는 금액
```
def findImpossibleNum():
    n = int(input())
    coins = list(map(int, input().split()))

    coins.sort()
    coinSum = sum(coins)
    flag = True

    for i in range(1, coinSum + 1):
        if i in coins:
            continue
        else:
            temp = []
            for coin in coins:
                if coin < i:
                    temp.append(coin)
            if sum(temp) >= i:
                continue
            else:
                print(i)
                flag = False
                break

    if flag:
        print(coinSum + 1)
```

### 만들 수 없는 금액 문제 해설 답안
```
def findImpossibleNum2():
    n = int(input())
    data = list(map(int, input().split()))
    data.sort()

    target = 1
    for x in data:
        if target < x:
            break
        target += x

    print(target)
```

### 볼링공 고르기
```
def chooseBall():
    n, m = map(int, input().split())
    ballWeight = list(map(int, input().split()))

    cnt = 0

    for i in range(n - 1):
        for j in range(i + 1, n):
            if ballWeight[i] != ballWeight[j]:
                cnt += 1

    print(cnt)
```

### 무지의 먹방 라이브
```
def moojiMukbang():
    def idxPlus(idx):
        idx += 1
        idx %= len(food_times)
        if food_times[idx] == 0:
            idx = idxPlus(idx)
        return idx

    food_times = list(map(int, input().split()))
    k = int(input())

    idx = 0
    totalTime = sum(food_times)

    if totalTime <= k:
        idx = -2
    else:
        while k != 0:
            food_times[idx] -= 1
            idx = idxPlus(idx)
            k -= 1

    answer = idx + 1
    print(answer)
```
-> 효율성은 좋지 못한 코드, 내가 생각하는 효율을 떨어뜨리는 요인은 idxPlus() 함수 재귀 호출이랑, sum() 함수이다. 다시 수정 요망⚠️
