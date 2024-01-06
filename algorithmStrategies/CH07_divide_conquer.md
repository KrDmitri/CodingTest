# CH07 분할 정복
### 쿼드 트리 뒤집기
```
import sys

def reverse_quadtree(quadtree):
    global idx
    head = quadtree[idx]
    idx += 1
    if head == 'b' or head == 'w':
        return head
    # 분할 파트
    upperLeft = reverse_quadtree(quadtree)
    upperRight = reverse_quadtree(quadtree)
    lowerLeft = reverse_quadtree(quadtree)
    lowerRight = reverse_quadtree(quadtree)
    
    # 병합 파트
    return "x"+lowerLeft+lowerRight+upperLeft+upperRight


T = int(sys.stdin.readline().rstrip())
idx = 0
for _ in range(T):
    quadtree = sys.stdin.readline().rstrip()
    quadtree = reverse_quadtree(quadtree)
    idx = 0
    print(quadtree)
```
-> 분할 정복 알고리즘에 익숙하지 못한거 같은데, 문제들을 더 풀면서 해당 문제를 어떻게 분할하고 병합할 수 있는지 잘 생각해봐야겠다.

### 울타리 잘라내기
```
import sys

def divide_conquer(start, end):
    mid = (start + end) // 2
    # 기저 사례
    if start == mid:
        return heights[mid]

    low_part = divide_conquer(start, mid)
    high_part = divide_conquer(mid, end)

    lo, hi = mid - 1, mid
    width = 2
    height = min(heights[lo], heights[hi])
    temp_ans = height * width
    while start < lo or hi + 1 < end:
        if (hi + 1 < end) and (lo == start or heights[hi + 1] > heights[lo - 1]):
            hi += 1
            width += 1
            height = min(height, heights[hi])
        else:
            lo -= 1
            width += 1
            height = min(height, heights[lo])
        temp_ans = max(temp_ans, width * height)
    temp_ans = max(temp_ans, low_part, high_part)
    return temp_ans


T = int(sys.stdin.readline().rstrip())

for _ in range(T):
    N = int(sys.stdin.readline().rstrip())
    heights = list(map(int, sys.stdin.readline().rstrip().split()))

    ans = divide_conquer(0, N)

    print(ans)
```
