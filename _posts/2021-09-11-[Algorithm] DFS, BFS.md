---
title: "[Algorithm] DFS, BFS"
date: 2021-09-10 21:30:00 -0000

tagline: "by baeksy"
header:
  overlay_image: /assets/images/splash/programer.jpeg
  overlay_filter: 0.5
  #show_overlay_excerpt : false

toc: true
toc_label: "Algorithm"
toc_sticky: true

categories: 
  - Algorithm
tags: 
  - Python
description: DFS, BFS 알고리즘의 기초와 기본 문제들에 대해 설명한 글입니다.
---

### 재귀 팩토리얼 구현

```python
def factorial_recursive(n):
    if n<=1:
        return 1
    return n * factorial_recursive(n-1)
print(factorial_recursive(5))
```

### 최대공약수 계산(유클리드 호제법)
- 두 자연수 A,b에 대하여 (A>B) A를 B로 나눈 나머지를 R이라고 할때
- 이때 A와 B의 최대공약수는 B와 R의 최대공약수와 같다.

```python
def gcd(a,b):
    if a%b == 0:
        return b
    else:
        return gcd(b, a%b)
    
print(gcd(192, 162))
```

### DFS(Depth-First Search)
- DFS는 깊이 우선 탐색이라고도 부르며, 그래프에서 깊은 부분을 우선적으로 탐색하는 알고리즘임
- DFS는 스택 자료구조(혹은 재귀 함수)를 이용하며, 구체적인 동작 과정은 다음과 같음.
    1. 탐색 시작 노드를 스택에 삽입하고 방문 처리를 함
    2. 스택의 최상단 노드에 방문하지 않은 인접한 노드가 하나라도 있으면 그 노드를 스택에 넣고 방문 처리함, 방문하지 않은 인접 노드가 없으면 스택에서 최상단 노드를 꺼냄
    3. 더 이상 2번 과정을 수행할 수 없을 때까지 반복

```python
graph = [
    [],
    [2, 3, 8],
    [1, 7],
    [1, 4, 5],
    [3, 5],
    [3, 4],
    [7],
    [2, 6, 8],
    [1, 7]
]

def dfs(graph, v, visited):
    visited[v] = True
    print(v, end=" ")
    
    for i in graph[v]:
        if not visited[i]:
            dfs(graph, i, visited)

visited = [False] * 9

dfs(graph, 1, visited)
```

### BFS(Breadth-First Search)
- BFS는 너비 우선 탐색이라고도 부르며, 그래프에서 가까운 노드부터 우선적으로 탐색하는 알고리즘임
- BFS는 큐 자료구조를 이용하며, 구체적인 동작 과정은 다음과 같음
    1. 탐색 시작 노드를 큐에 삽입하고 방문 처리를 함
    2. 큐에서 노드를 꺼낸 뒤에 해당 노드의 인접 노드 중에서 방문하지 않은 노드를 모두 큐에 삽입하고 방문처리함
    3. 더 이상 2번의 과정을 수행할 수 없을 때까지 반복함

```python
from collections import deque

def bfs(graph, start, visited):
    queue = deque([start])
    
    visited[start] = True
    
    while queue:
        v = queue.popleft()
        print(v, end= " ")
        
        for i in graph[v]:
            if not visited[i]:
                queue.append(i)
                visited[i]=True

visited = [False] * 9

bfs(graph, 1, visited)
```

### 음료수 얼려 먹기
- N x M 크기의 얼음 틀이 있음
- 구멍이 뚤려 있는 부분은 0, 칸막이가 존재하는 부분은 1로 표시됨
- 얼음 틀의 모양이 주어졌을 때 생성되는 총 아이스크림의 개수를 구하는 프로그램을 작성

```python
n,m = 4, 5
graph = [
    [0,0,1,1,0],
    [0,0,0,1,1],
    [1,1,1,1,1],
    [0,0,0,0,0]
]

def dfs(x,y):
    if x <= -1 or x >=n or y <= -1 or y >= m:
        return False
    
    if graph[x][y] == 0:
        graph[x][y] = 1
        
        dfs(x-1,y)
        dfs(x,y-1)
        dfs(x+1,y)
        dfs(x,y+1)
        return True
    return False

result = 0

for i in range(n):
    for j in range(m):
        if dfs(i,j) == True:
            result+=1
            
print(result)
```
### 미로 탈출
- N x M 크기의 직사각형 형태의 미로에 갇혔음
- 출발 위치는 (1,1)이며 미로의 출구는 (N,M)의 위치에 존재하며 한 번에 한칸씩 이동할 수 있음
- 이떄 괴물이 있는 부분은 0으로, 괴물이 없는 부분은 1로 표시되어 있음
- 탈출을 위해서 움직여야하는 최소 칸의 개수를 구하시오

```python
n, m = 5, 6
graph = [
    [1,0,1,0,1,0],
    [1,1,1,1,1,1],
    [0,0,0,0,0,1],
    [1,1,1,1,1,1],
    [1,1,1,1,1,1]
]

from collections import deque

def bfs(x, y):
    queue = deque()
    queue.append((x,y))
    
    while queue:
        x, y = queue.popleft()
        
        for i in range(4):
            nx = x + dx[i]
            ny = y + dy[i]
            
            if nx < 0 or nx >= n or ny <0 or ny >= m:
                continue
                
            if graph[nx][ny] == 0:
                continue
                
            if graph[nx][ny] == 1:
                graph[nx][ny] = graph[x][y] + 1
                queue.append((nx, ny))
    return graph[n-1][m-1]

dx = [-1, 1, 0, 0]
dy = [0, 0, -1, 1]

print(bfs(0,0))
```




