# 다이나믹 프로그래밍
### 이동하기
```
import sys

n, m = map(int, sys.stdin.readline().rstrip().split())
graph = []
for _ in range(n):
    graph.append(list(map(int, sys.stdin.readline().rstrip().split())))
dp = [[0] * m for _ in range(n)]
dp[0][0] = graph[0][0]
for i in range(1, m):
    dp[0][i] = graph[0][i] + dp[0][i - 1]
for i in range(1, n):
    dp[i][0] = graph[i][0] + dp[i - 1][0]

for i in range(1, n):
    for j in range(1, m):
        dp[i][j] = graph[i][j] + max(dp[i - 1][j - 1], dp[i - 1][j], dp[i][j - 1])

print(dp[n - 1][m - 1])
```

### 점프점프
```
import sys
INF = int(1e9)

n = int(sys.stdin.readline().rstrip())
a = list(map(int, sys.stdin.readline().rstrip().split()))

dp = [INF] * n
dp[0] = 0
for i in range(n):
    jump = a[i]
    for j in range(1, jump + 1):
        if i + j < n:
            dp[i + j] = min(dp[i] + 1, dp[i + j])

print(dp[n - 1] if dp[n - 1] != INF else -1)
```

### 팰린드롬?
```
import sys

n = int(sys.stdin.readline().rstrip())
numList = list(map(int, sys.stdin.readline().rstrip().split()))

dp = [[0] * n for _ in range(n)]

for numLen in range(n):
    for start in range(n - numLen):
        end = start + numLen
        if numLen == 0:
            dp[start][end] = 1
        elif numList[start] == numList[end]:
            if numLen == 1:
                dp[start][end] = 1
            else:
                if dp[start + 1][end - 1] == 1:
                    dp[start][end] = 1

m = int(sys.stdin.readline().rstrip())
for _ in range(m):
    x, y = map(int, sys.stdin.readline().rstrip().split())
    print(dp[x - 1][y - 1])
```
-> 위 다이나믹 프로그래밍 문제는 왼쪽에서 오른쪽으로 쌓아가는 다른 다이나믹 프로그래밍 문제와 다르게 양 끝의 문자를 하나씩 빼 보아서 회문인지 확인하는 방법으로 풀 수 있다.

### 1,2,3 더하기 4
```
import sys

n = int(sys.stdin.readline().rstrip())
dp = [1] * 10001

for i in range(2, 10001):
    dp[i] += dp[i - 2]

for i in range(3, 10001):
    dp[i] += dp[i - 3]

for _ in range(n):
    x = int(sys.stdin.readline().rstrip())
    print(dp[x])
```
-> 다이나믹 프로그래밍 문제는 항상 점화식, 아니면 패턴을 꼭 먼저 찾고난 후에 풀도록 하자

### 파일 합치기
```
import sys

t = int(sys.stdin.readline().rstrip())

for _ in range(t):
    k = int(sys.stdin.readline().rstrip())
    numList = list(map(int, sys.stdin.readline().rstrip().split()))
    subSum = [0]
    for i in range(k):
        subSum.append(sum(numList[:i + 1]))

    dp = [[0] * (k + 1) for _ in range(k + 1)]
    for numLen in range(2, k + 1):
        for i in range(1, k + 1):
            j = i + numLen - 1
            if numLen == 2:
                if i == k:
                    continue
                dp[i][j] = subSum[j] - subSum[i - 1]
            else:
                if i == k - numLen + 2:
                    break
                candidates = []
                # x랑 y는 각 부분합 원소 길이
                for x in range(1, numLen):
                    y = numLen - x
                    left, right = 0, 0
                    if x >= 2:
                        left = dp[i][i + x - 1]
                    if y >= 2:
                        right = dp[i + x][i + numLen - 1]
                    candidates.append(left + right)
                dp[i][j] = (subSum[j] - subSum[i - 1]) + min(candidates)

    print(dp[1][k])
```
-> 코드는 내가 작성했는데 아이디어는 다른 사람의 블로그 내 설명에서 얻었다. dp.. 너 정말 어려웠구나..
