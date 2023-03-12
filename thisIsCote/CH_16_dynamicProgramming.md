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
