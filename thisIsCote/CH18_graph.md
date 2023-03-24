## Chapter 18 그래프 이론 문제
### 여행 계획
```
def find_parent(parent, x):
    if parent[x] != x:
        parent[x] = find_parent(parent, parent[x])
    return parent[x]

def union_parent(parent, a, b):
    a = find_parent(parent, a)
    b = find_parent(parent, b)
    if a < b:
        parent[b] = a
    else:
        parent[a] = b

n, m = map(int, input().split())
parent = [0] * (n + 1)

for i in range(n):
    data = list(map(int, input().split()))
    for j in range(n):
        if data[j] == 1:
            union_parent(parent, i + 1, j + 1)

plan = list(map(int, input().split()))

result = True
for i in range(m - 1):
    if find_parent(parent, plan[i]) != find_parent(parent, plan[i + 1]):
        result = False

if result:
    print('YES')
else:
    print('NO')
```
-> 노드끼리 연결되어 있는지 확인할 때는 서로소 집합 알고리즘을 활용
