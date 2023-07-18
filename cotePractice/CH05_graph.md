# 그래프 알고리즘
### Two Dots
```
def nextDirection(direction):
    return (direction + 1) % 4

def searchCycle(start, now, direction):
    x, y = now[0], now[1]
    nextX = x + dx[direction]
    nextY = y + dy[direction]
    if nextX >= 0 and nextX < n and nextY >= 0 and nextY < m:
        next = [nextX, nextY]
        if board[start[0]][start[1]] == board[nextX][nextY]:
            if next == start:
                return True
            flag = searchCycle(start, next, direction)
            if flag:
                return True

    if direction != 3:
        direction = nextDirection(direction)
        nextX = x + dx[direction]
        nextY = y + dy[direction]
        if nextX >= 0 and nextX < n and nextY >= 0 and nextY < m:
            next = [nextX, nextY]
            if board[start[0]][start[1]] == board[nextX][nextY]:
                if next == start:
                    return True
                flag = searchCycle(start, next, direction)
                if flag:
                    return True
    # 아래 코드 맞나?
    return False


n, m = map(int, input().split())
board = []
for _ in range(n):
    board.append(input())

dx = [1, 0, -1, 0]
dy = [0, 1, 0, -1]
flag = 0

for i in range(n):
    for j in range(m):
        flag = searchCycle([i, j], [i, j], 0)
        if flag:
            break
    if flag:
        break

if flag:
    print('Yes')
else:
    print('No')
```
-> 위 답은 틀린 답인데, 문제를 잘못 이해했다. 나는 문제에서 말하는 싸이클이 꼭 직사각형이어야하는 줄 알았는데 그렇지 않았다..😤
