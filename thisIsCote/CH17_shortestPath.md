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

### 정확한 순위
```
INF = 1e9
def preciseRank():
    n, m = map(int, input().split())
    distances = [[INF] * (n + 1) for _ in range(n + 1)]
    reverseDistances = [[INF] * (n + 1) for _ in range(n + 1)]

    for _ in range(m):
        a, b = map(int, input().split())
        distances[a][b] = 1
        reverseDistances[b][a] = 1

    for k in range(1, n + 1):
        for a in range(1, n + 1):
            for b in range(1, n + 1):
                distances[a][b] = min(distances[a][b], distances[a][k] + distances[k][b])
                reverseDistances[a][b] = min(reverseDistances[a][b], reverseDistances[a][k] + reverseDistances[k][b])

    count = 0
    for a in range(1, n + 1):
        flag = True
        for b in range(1, n + 1):
            if a == b:
                distances[a][b] = 0
                reverseDistances[a][b] = 0
            if distances[a][b] == INF and reverseDistances[a][b] == INF:
                flag = False
        if flag == True:
            count += 1

    print(count)
```
-> 플로이드를 사용하면 한 방향에 대해 연결이 되어 있는지 알 수 있어서 두 방향으로 플로이드 함수를 사용해서 두 방향 다 연결이 안되어 있는 노드의 수는 제거하는 방식으로 문제 해결함. 플로이드 워셜을 사용해서 시간 초과가 나는거 아닌가 했는데 답지에서도 동일한 방법으로 해결했다. 그런데 나처럼 굳이 reverseDistance처럼 새로운 메모리를 사용하지 않고 distance[a][b] == INF and distance[b][a] == INF 처럼 그냥 인덱스 순서만 바꿔줌