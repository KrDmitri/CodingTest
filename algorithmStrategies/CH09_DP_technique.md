# CH09 동적 계획법 테크닉
### 최대 증가 부분 수열 실제로 출력하기
```
import sys

def lis(idx):
    if dp[idx] != -1:
        return dp[idx]

    ret = 1
    for j in range(idx + 1, N):
        if idx == -1 or nums[j] > nums[idx]:
            if 1 + lis(j) > ret:
                choices[idx + 1] = j
                ret = 1 + lis(j)
    dp[idx] = ret
    return ret

def best_choices(idx):
    choice = choices[idx]
    if choice == -1:
        return

    print(nums[choice], end=' ')
    best_choices(choice + 1)


T = int(sys.stdin.readline().rstrip())

for _ in range(T):
    N = int(sys.stdin.readline().rstrip())
    nums = list(map(int, sys.stdin.readline().rstrip().split()))
    dp = [-1] * N
    choices = [-1] * (N + 1)

    ans = lis(-1) - 1
    best_choices(0)
    print()
```

### 여행 짐 싸기(PACKING)
```
import sys

def packing(n, w):
    if n < 0:
        return 0
    if dp[n][w] != -1:
        return dp[n][w]

    ret = 0
    volume = items[n][1]
    score = items[n][2]
    if w >= volume:
        ret = max(ret, packing(n - 1, w - volume) + score)
    ret = max(ret, packing(n - 1, w))
    dp[n][w] = ret
    return ret

def print_items(n, w):
    if n == 0:
        if dp[n][w] == items[n][2]:
            item_list.append(items[n][0])
            return 1
        else:
            return 0
    volume = items[n][1]
    score = items[n][2]
    ans = dp[n][w]

    if ans == dp[n - 1][w]:
        return print_items(n - 1, w)
    elif ans == dp[n - 1][w - volume] + score:
        item_list.append(items[n][0])
        return 1 + print_items(n - 1, w - volume)


T = int(sys.stdin.readline().rstrip())

for _ in range(T):
    N, W = map(int, sys.stdin.readline().rstrip().split())
    items = []
    for _ in range(N):
        a, b, c = sys.stdin.readline().rstrip().split()
        b = int(b)
        c = int(c)
        items.append([a, b, c])

    dp = [[-1] * (W + 1) for _ in range(N)]
    choices = [[] for _ in range(N)]
    ans = packing(N - 1, W)
    print(dp[N - 1][W], end=' ')

    item_list = []
    nums = print_items(N - 1, W)
    print(nums)
    for elem in item_list:
        print(elem)
```

### 모스 부호 사전(MORSE)
```
import sys
INF = int(1e10)

def generate(n, m, s):
    global skip
    if skip < 0:
        return

    if n == 0 and m == 0:
        if skip == 0:
            print(s)
        skip -= 1
        return

    if skip > bino[n + m][n]:
        skip -= bino[n + m][n]
        return

    if n > 0:
        generate(n - 1, m, s + '-')
    if m > 0:
        generate(n, m - 1, s + 'o')

bino = [[0] * 201 for _ in range(201)]

for i in range(201):
    bino[i][0] = bino[i][i] = 1
    for j in range(1, i):
        bino[i][j] = min(INF, bino[i - 1][j - 1] + bino[i - 1][j])

T = int(sys.stdin.readline().rstrip())

for _ in range(T):
    n, m, k = map(int, sys.stdin.readline().rstrip().split())
    skip = k - 1
    generate(n, m, '')
```

### 모스 부호 사전(MORSE) - 두번째 풀이
```
import sys
INF = int(1e10)

def kth(n, m, skip):
    if n == 0:
        return 'o' * m

    if skip < bino[n + m - 1][n - 1]:
        return '-' + kth(n - 1, m, skip)
    else:
        return 'o' + kth(n, m - 1, skip - bino[n + m - 1][n - 1])
        

bino = [[0] * 201 for _ in range(201)]

for i in range(201):
    bino[i][0] = bino[i][i] = 1
    for j in range(1, i):
        bino[i][j] = min(INF, bino[i - 1][j - 1] + bino[i - 1][j])

T = int(sys.stdin.readline().rstrip())

for _ in range(T):
    n, m, k = map(int, sys.stdin.readline().rstrip().split())
    print(kth(n, m, k - 1))
```

### K번째 최대 증가 부분 수열(KLIS)
```
import sys

def get_dp(x):
    if dp[x] != -1:
        return dp[x]

    ret = 0
    for i in range(x + 1, N):
        if nums[i] > nums[x]:
            ret = max(ret, get_dp(i))
    dp[x] = ret + 1
    return ret + 1

def get_num_biggers(x):
    if num_biggers[x] != -1:
        return num_biggers[x]

    ret = 0
    for i in range(x + 1, N):
        if nums[i] > nums[x] and dp[x] - 1 == dp[i]:
            ret += get_num_biggers(i)
    num_biggers[x] = ret
    return ret

def klis(x, ans_nums, length_max):
    global K
    if K < 0:
        return

    if get_num_biggers(x) <= K:
        K -= get_num_biggers(x)
        return

    ans_nums.append(nums[x])

    if K == 0 and len(ans_nums) == length_max:
        print(length_max)
        for num in ans_nums:
            print(num, end=' ')
        print()

    if dp[x] == 1:
        K -= 1
        return

    nexts = []
    for i in range(x + 1, N):
        if nums[i] > nums[x] and dp[x] - 1 == dp[i]:
            nexts.append([i, nums[i]])
    nexts.sort(key=lambda x:x[1])

    for next, _ in nexts:
        klis(next, ans_nums, length_max)
    ans_nums.pop()

T = int(sys.stdin.readline().rstrip())

for _ in range(T):
    N, K = map(int, sys.stdin.readline().rstrip().split())
    nums = list(map(int, sys.stdin.readline().rstrip().split()))
    dp = [-1] * N

    for i in range(N):
        get_dp(i)

    num_biggers = [-1] * N
    for i in range(N):
        if get_dp(i) == 1:
            num_biggers[i] = 1

    for i in range(N):
        get_num_biggers(i)

    length_max = max(dp)
    K -= 1
    starts = []
    for i in range(N):
        if length_max == dp[i]:
            starts.append([i, nums[i]])
    starts.sort(key=lambda x:x[1])
    for start, _ in starts:
        klis(start, [], length_max)
```
