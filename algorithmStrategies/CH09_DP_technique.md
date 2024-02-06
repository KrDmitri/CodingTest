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
