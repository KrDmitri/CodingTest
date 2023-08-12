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

### 파일 합치기(모범답안)
```
t = int(input())

for _ in range(t):
    k = int(input())
    lst = [0] + list(map(int,input().split()))

    s_lst = [0 for _ in range(k+1)]

    for i in range(1,k+1):
        s_lst[i] = s_lst[i-1] + lst[i]
    # print('s_lst',s_lst)

    dp = [[0 for i in range(k+1)] for j in range(k+1)]

    for i in range(2,k+1):
        for j in range(1,k+2-i):
            dp[j][j+i-1] = min([dp[j][j+q] + dp[j+q+1][j+i-1] for q in range(i-1)]) +(s_lst[j+i-1] - s_lst[j-1])

            # print('--------------------')
            # print('test :' , [dp[j][j+q] + dp[j+q+1][j+i-1] for q in range(i-1)])
            # print('i :' ,i,'j :',j)
            # print('s_lst : ',s_lst)
            # for x in dp:
            #     print(x)

    print(dp[1][k])
```
-> min() 함수 안에 각 원소들을 위와 같이 for문으로 생성했다는 점이 흥미롭다.

### 평범한 배낭(dfs - branch and bound 풀이)
```
import sys

def calcBound(i, alreadyIn, remain):
    bound = alreadyIn
    while remain > 0:
        if i >= n:
            break
        weight = knapsack[i][0]
        if remain > weight:
            remain -= weight
            bound += knapsack[i][1]
        else:
            bound += knapsack[i][2] * remain
            remain = 0
        i += 1
    return bound

def solve(i, bound, alreadyIn, remain):
    global answer
    if i == n or answer >= bound:
        return
    if remain >= knapsack[i][0]:
        # 넣는다
        newRemain = remain - knapsack[i][0]
        newIn = alreadyIn + knapsack[i][1]
        answer = max(answer, newIn)
        solve(i + 1, bound, newIn, newRemain)
        # 넣지 않는다
        bound = calcBound(i + 1, alreadyIn, remain)
        solve(i + 1, bound, alreadyIn, remain)
    else:
        solve(i + 1, bound, alreadyIn, remain)

n, k = map(int, sys.stdin.readline().rstrip().split())
knapsack = []
for _ in range(n):
    x, y = map(int, sys.stdin.readline().rstrip().split())
    z = y / x
    knapsack.append([x, y, z])
knapsack.sort(key=lambda x:x[2], reverse=True)
bound = calcBound(0, 0, k)
answer = 0
# for a in knapsack:
#     print(a)
solve(0, bound, 0, k)
print(answer)
```
-> dp 풀이 방법이 잘 떠오르지 않아 branch and bound로 풀어보았다. 우선순위 큐를 이용한 best first search가 아니라서 그런건지 답은 잘 나오지만 시간초과가 나온다.

### 평범한 배낭(dp 풀이방법)
```
import sys

n, k = map(int, sys.stdin.readline().rstrip().split())
knapsack = []
for _ in range(n):
    x, y = map(int, sys.stdin.readline().rstrip().split())
    knapsack.append([x, y])
knapsack.sort(key=lambda x:x[0])
dp = [[0] * (k + 1) for _ in range(n + 1)]

# i는 각 물건의 인덱스, j는 무게의 한도
for i in range(1, n + 1):
    for j in range(1, k + 1):
        weight, value = knapsack[i - 1][0], knapsack[i - 1][1]
        if weight > j:
            dp[i][j] = dp[i - 1][j]
        else:
            dp[i][j] = max(dp[i - 1][j], dp[i - 1][j - weight] + value)

print(dp[n][k])
```

### 기타리스트
```
import sys

n, s, m = map(int, sys.stdin.readline().rstrip().split())
volumes = list(map(int, sys.stdin.readline().rstrip().split()))

dp = [[0] * (m + 1) for _ in range(n + 1)]

dp[0][s] = 1
for i in range(n):
    for j in range(m + 1):
        if dp[i][j] == 1:
            if j + volumes[i] <= m:
                dp[i + 1][j + volumes[i]] = 1
            if j - volumes[i] >= 0:
                dp[i + 1][j - volumes[i]] = 1

ans = -1
for i in range(m + 1):
    if dp[n][i] == 1:
        ans = i
print(ans)
```

### 뮤탈리스크
```
import sys
from itertools import permutations

n = int(sys.stdin.readline().rstrip())
numList = list(map(int, input().split()))
numList.sort(reverse=True)
for i in range(3 - len(numList)):
    numList.append(0)

dp = [[0] * 61 for _ in range(61)]

for i in range(61):
    for j in range(61):
        dp[i][j] = []
dp[0][numList[0]].append([numList[1], numList[2]])

damage = [9, 3, 1]
damageCases = list(permutations(damage, 3))

# i는 횟수, j는 숫자들 중 가장 큰 수
for i in range(60):
    for j in range(61):
        now = dp[i][j]
        if len(now) > 0:
            for elem in now:
                if j == 0 and elem[0] == 0 and elem[1] == 0:
                    print(i)
                    exit(0)
                tempNum = [j, elem[0], elem[1]]
                for case in damageCases:
                    tempList = [j - case[0], elem[0] - case[1], elem[1] - case[2]]
                    for k in range(3):
                        if tempList[k] < 0:
                            tempList[k] = 0
                    tempList.sort(reverse=True)
                    if [tempList[1], tempList[2]] not in dp[i + 1][tempList[0]]:
                        dp[i + 1][tempList[0]].append([tempList[1], tempList[2]])
```
-> 더 나은 풀이가 있나 확인해보았는데 다른 사람들은 3중 리스트를 활용해서 문제 해결을 했다.

### 동전 1
```
import sys

n, k = map(int, sys.stdin.readline().rstrip().split())
numList = []
for _ in range(n):
    numList.append(int(sys.stdin.readline().rstrip()))
dp = [0] * (k + 1)
dp[0] = 1
for coin in numList:
    for num in range(coin, k + 1):
        if num - coin >= 0:
            dp[num] += dp[num - coin]

print(dp[k])
```

### 동전 2
```
import sys

n, k = map(int, sys.stdin.readline().rstrip().split())
numList = []
for _ in range(n):
    numList.append(int(sys.stdin.readline().rstrip()))
dp = [0] * (k + 1)
for coin in numList:
    if coin <= k:
        dp[coin] = 1
for i in range(1, k + 1):
    flag = 0
    tempList = []
    for coin in numList:
        if i - coin == 0:
            dp[i] = 1
            flag = 1
            break
        if i - coin >= 0 and dp[i - coin] != 0:
            tempList.append(dp[i - coin] + 1)
    if flag:
        continue
    if len(tempList) > 0:
        dp[i] = min(tempList)

if dp[k] == 0:
    print(-1)
else:
    print(dp[k])
```

### 크리보드
```
import sys

# n은 1부터 100까지
n = int(sys.stdin.readline().rstrip())
dp = [[] for _ in range(n + 1)]
dp[0].append([0, 0, False])   # 순서별로 현재 출력 크기, 버퍼 크기, 이전 control A 눌렸는지 여부

for i in range(n):
    thisCases = dp[i]
    for j in range(len(thisCases)):
        now = thisCases[j]
        if len(now) != 3:
            print('', end='')
        strSize, bufSize, aFlag = now[0], now[1], now[2]
        dp[i + 1].append([strSize + 1, bufSize, False])
        if strSize != 0:
            dp[i + 1].append([strSize, bufSize, True])
        if aFlag:
            dp[i + 1].append([strSize, strSize, False])
        if bufSize != 0:
            dp[i + 1].append([strSize + bufSize, bufSize, False])
ans = 0
for i in range(len(dp[n])):
    ans = max(ans, dp[n][i][0])

print(ans)
```

### 크리보드(모범답안)
```
import sys

n = int(sys.stdin.readline().rstrip())
dp = [i for i in range(n + 1)]

for i in range(6, n + 1):
    dp[i] = max(dp[i - 3] * 2, dp[i - 4] * 3, dp[i - 5] * 4)

print(dp[n])
```
-> 처음 내 풀이는 각 횟수에서 할 수 있는 행동의 모든 경우의 수들을 다음 횟수로 넘기는 방식으로 하였다. 해당 방식은 시간 초과는 물론 메모리 초과도 유도하였다. 문제 특성상 수학적 귀납법을 활용하면 현재 위치에서의 최댓값은 5칸 전에서 복사 붙여넣기를 3번까지 했을때가 가장 크기 때문에 이러한 특성을 이용하면 쉽게 문제를 풀 수 있다. 하지만 이런 문제 접근법을 떠올리기가 쉽지 않다...🥲

### LCS
```
import sys

a = sys.stdin.readline().rstrip()
b = sys.stdin.readline().rstrip()
n = len(b)
m = len(a)
dp = [[0] * (m + 1) for _ in range(n + 1)]

# i는 b의 인덱스, j는 a의 인덱스
for i in range(n):
    for j in range(m):
        if a[j] == b[i]:
            dp[i][j] = dp[i - 1][j - 1] + 1
        else:
            dp[i][j] = max(dp[i - 1][j], dp[i][j - 1])

print(dp[n - 1][m - 1])
```

### LCS2
```
import sys

a = sys.stdin.readline().rstrip()
b = sys.stdin.readline().rstrip()
n = len(b)
m = len(a)
dp = [[[0, ''] for _ in range(m + 1)] for _ in range(n + 1)]

# i는 b의 인덱스, j는 a의 인덱스
for i in range(n):
    for j in range(m):
        if a[j] == b[i]:
            dp[i][j] = [dp[i - 1][j - 1][0] + 1, dp[i - 1][j - 1][1] + a[j]]
        else:
            dp[i][j] = [max(dp[i - 1][j][0], dp[i][j - 1][0]), dp[i - 1][j][1] if dp[i - 1][j][0] > dp[i][j - 1][0] else dp[i][j - 1][1]]

print(dp[n - 1][m - 1][0])
print(dp[n - 1][m - 1][1])
```

### 공통 부분 문자열
```
import sys

def memo(x, y, num):
    global dp, maxNum
    while x < n and y < m and a[y] == b[x]:
        dp[x][y] = num
        x += 1
        y += 1
        num += 1
    maxNum = max(maxNum, num - 1)

a = sys.stdin.readline().rstrip()
b = sys.stdin.readline().rstrip()

m = len(a)
n = len(b)
dp = [[0] * m for _ in range(n)]
maxNum = 0

# i는 b의 인덱스, j는 a의 인덱스
for i in range(n):
    for j in range(m):
        if a[j] == b[i] and dp[i][j] == 0:
            memo(i, j, 1)

print(maxNum)
```

### 1학년
```
import sys

n = int(sys.stdin.readline().rstrip())
numList = list(map(int, sys.stdin.readline().rstrip().split()))

dp = [[0] * (n - 1) for _ in range(21)]
dp[numList[0]][0] = 1

for j in range(n - 2):
    for i in range(21):
        if dp[i][j] == 0:
            continue
        if i + numList[j + 1] <= 20:
            dp[i + numList[j + 1]][j + 1] += dp[i][j]
        if i - numList[j + 1] >= 0:
            dp[i - numList[j + 1]][j + 1] += dp[i][j]

print(dp[numList[n - 1]][n - 2])
```
-> 다이나믹 프로그래밍 아주 어려웠는데 계속 풀면서 조금은 감각을 익힌듯 하다
