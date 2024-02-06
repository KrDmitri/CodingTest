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
