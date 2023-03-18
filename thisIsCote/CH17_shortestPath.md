## 최단 경로 문제
### 플로이드
```
INF = 1e9
def floyd():
    n = int(input())
    m = int(input())

    routes = [[INF] * (n + 1) for _ in range(n + 1)]

    for i in range(m):
        a, b, c = map(int, input().split())
        if routes[a][b] == INF:
            routes[a][b] = c
        else:
            routes[a][b] = min(routes[a][b], c)

    for k in range(1, n + 1):
        for a in range(1, n + 1):
            for b in range(1, n + 1):
                routes[a][b] = min(routes[a][b], routes[a][k] + routes[k][b])

    for a in range(1, n + 1):
        for b in range(1, n + 1):
            if a == b:
                routes[a][b] = 0
            if routes[a][b] == INF:
                routes[a][b] = 0
            if b == n:
                print(routes[a][b])
            else:
                print(routes[a][b], end=' ')
```
