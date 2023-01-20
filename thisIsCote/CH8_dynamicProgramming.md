## Chapter 08 다이나믹 프로그래밍
### 1로 만들기
```
# 2, 3, 5로 나누거나 1을 빼가면서 1을 만드는데 연산 몇 번 걸리는지
def makeOne():
    d = [0] * 100
    d[1] = 0

    # 5, 3, 2로 나눈거랑 1뺀거중 가장 작은거를 d에 저장
    for i in range(2, 100):
        div5 = 100
        div3 = 100
        div2 = 100
        min1 = 100
        if i // 5 > 0:
            remainder = i % 5
            if remainder > 0:
                div5 = remainder + d[i - remainder]
            else:
                div5 = 1 + d[i // 5]
        if i // 3 > 0:
            remainder = i % 3
            if remainder > 0:
                div3 = remainder + d[i - remainder]
            else:
                div3 = 1 + d[i // 3]
        if i // 2 > 0:
            remainder = i % 2
            if remainder > 0:
                div2 = remainder + d[i - remainder]
            else:
                div2 = 1 + d[i // 2]
        if i - 1 > 0:
            min1 = 1 + d[i - 1]

        d[i] = min(div2, div3, div5, min1)
```

### 1로 만들기 문제 해설 답안
```
def makeOne2():
    x = int(input())

    d = [0] * 30001

    for i in range(2, x + 1):
        d[i] = d[i - 1] + 1
        if i % 2 == 0:
            d[i] = min(d[i], d[i//2] + 1)
        if i % 3 == 0:
            d[i] = min(d[i], d[i//3] + 1)
        if i % 5 == 0:
            d[i] = min(d[i], d[i//5] + 1)

    print(d[x])
```

### 개미 전사(오답)
```
# 1. 가장 큰 수 찾는다
# 2. 가장 큰 수 양 옆 값 0으로 함
# 3. 남은 것들중에서 큰 수로 반복
def antWarrior():
    n = int(input())  # 예제에서 4임
    foodStorage = list(map(int, input().split()))  # 1, 3, 1, 5
    result = 0

    while sum(foodStorage) != 0:
        maxNum = max(foodStorage)
        maxIndex = findIndex(maxNum, foodStorage)
        result += maxNum

        foodStorage[maxIndex] = 0
        if maxIndex - 1 >= 0:
            foodStorage[maxIndex - 1] = 0
        if maxIndex + 1 < n:
            foodStorage[maxIndex + 1] = 0

    print(result)



def findIndex(elem, list):
    for i in range(len(list)):
        if list[i] == elem:
            return  i
```
-> 가장 큰 수를 찾아서 더해가는 방식이 아닌 왼쪽부터 털면서 i번째 창고를 터는게 더 큰지 아닌지만 확인 하면됨

### 개미 전사(수정 코드)
```
def antWarrior():
    n = int(input())  # 예제에서 4임
    foodStorage = list(map(int, input().split()))  # 1, 3, 1, 5

    d = [0] * 1000

    d[0] = foodStorage[0]
    d[1] = max(foodStorage[0], foodStorage[1])

    for i in range(2, n):
        d[i] = max(d[i - 1], foodStorage[i] + d[i -2])

    print(d[n - 1])
```

### 바닥 공사
```
def fittingTile():
    n = int(input())

    d = [0] * 10000
    d[0] = 1
    d[1] = 1
    for i in range(2, 10000):
        d[i] = d[i - 1] + d[i - 2] * 2

    print(d[n])
```

### 효율적인 화폐 구성
```
def combianteMoney():
    n, m = map(int, input().split())
    moneys = []
    for i in range(n):
        moneys.append(int(input()))
    moneys.sort()
    d = [999] * 1000
    d[0] = 0
    leastMoney = moneys[0]

    for i in range(1, 1000):
        # 애초에 나타낼 수 없는 돈은 -999로
        if i < leastMoney:
            d[i] = -999
        # 가장 작은 단위보다 큰 수부터
        else:
            # 나누어 떨어지는 경우
            for money in moneys:
                if i >= money and i % money == 0:
                    temp = i // money
                    d[i] = min(d[i], temp)

            # 조합하는 경우
            for bigMoney in range(i-1, 0, -1):
                if d[bigMoney] > 0 and d[i - bigMoney] > 0:
                    temp = d[bigMoney] + d[i - bigMoney]
                    d[i] = min(d[i], temp)

    if d[i] > 0:
        print(d[m])
    else:
        print(-1)
```

### 효율적인 화폐 구성 해설 답안
```
def combinateMoney2():
    n, m = map(int, input().split())
    array = []
    for i in range(n):
        array.append(int(input()))
    
    d = [10001] * (m + 1)
    d[0] = 0
    
    for i in range(n):
        for j in range(array[i], m + 1):
            if d[j - array[i]] != 10001:
                d[j] = min(d[j], d[j - array] + 1)
    
    if d[m] == 10001:
        print(-1)
    else:
        print(d[m])
```
-> 내가 작성한 코드는 i-1 번째부터 하나씩 다 내려가면서 i번째를 만들 수 있는 가장 작은 경우를 구한 코드인 반면 답안 코드는 화폐 단위 하나 하나를 보면서 i-k 번째 경우 수 + 1 (여기서 k는 화폐 단위들) 로 경우의 수를 구한 방식(해설 답안 코드가 실행 시간이 훨씬 더 적게 들 것임)
