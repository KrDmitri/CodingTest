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
