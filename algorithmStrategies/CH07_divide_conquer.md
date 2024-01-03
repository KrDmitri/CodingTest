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
