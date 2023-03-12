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
