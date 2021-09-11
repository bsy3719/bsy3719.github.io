---
title: "[Algorithm] 최단경로 알고리즘"
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
description: 최단경로 알고리즘의 기초와 기본 문제들에 대해 설명한 글입니다.
---

### 최단 경로 문제
- 가장 짧은 경로를 찾는 알고리즘
- 다양한 문제 상황
    - 한 지점에서 다른 한 지점까지의 최단 경로
    - 한 지점에서 다른 모든 지점까지의 최단 경로
    - 모든 지점에서 다른 모든 지점까지의 최단 경로
- 각 지점은 그래프에서 노드로 표현
- 지점 간 연결된 도로는 그래프에서 간선으로 표현

### 다익스트라 최단 경로 알고리즘
- 특정한 노드에서 출발하여 다른 모든 노드로 가는 최단 경로를 계산
- 음의 간선이 없을 때 정상적으로 동작
- 그리디 알고리즘으로 분류 : 매 상황에서 가장 비용이 적은 노드를 선택해 임의의 과정을 반복

### 동작과정
1. 출반 노드를 설정
2. 최단 거리 테이블을 초기화
3. 방문하지 않은 노드 중에서 최단 거리가 가장 짧은 노드를 선택
4. 해당 노드를 거쳐 다른 노드로 가는 비용을 계산하여 최단 거리 테이블을 갱신
5. 위 과정에서 3번과 4번을 반복

- 알고리즘 동작 과정에서 최단 거리 테이블은 각 노드에 대한 현재까지의 최단 거리 정보를 가지고 있음
- 처리 과정에서 더 짧은 경로를 찾으면 '이제부터는 이 경로가 제일 짧은 경로애"라고 갱신함

### 다익스트라 알고리즘의 특징
- 그리디 알고리즘 : 매 상황에서 방문하지 않은 가장 비용이 적은 노드를 선택해 임의의 과정을 반복
- 단계를 거치며 한 번 처리된 노드의 최단 거리는 고정되어 더 이상 바뀌지 않음
    - 한 단계당 하나의 노드에 대한 최단 거리를 확실히 찾는 것으로 이해할 수 있음
- 다익스트라 알고리즘을 수행한 뒤에 테이블에 각 노드까지의 최단 거리 정보가 저장됨
    - 완벽한 형태의 최단 경로를 구하려면 소스코드에 추가적인 기능을 더 넣어야 함

### 간단한 구현 방법

```python
import sys

INF = int(1e9)

n, m = 6, 11
start = 1

graph = [
    [],
    [(2,2), (3,5), (4,1)],
    [(3,3), (4,2)],
    [(2,3), (6,5)],
    [(3,3), (5,1)],
    [(3,1), (6,2)],
    []
]

visited = [False] * (n+1)
distance = [INF] *(n+1)

# 방문하지 않은 노드 중에서, 가장 최단 거리가 깗은 노드의 번호를 반환
def get_smallest_node():
    min_value = INF
    index = 0
    for i in range(1, n+1):
        if distance[i] < min_value and not visited[i]:
            min_value = distance[i]
            index = i
            
    return index

def dijkstra(start):
    distance[start] = 0
    visited[start] = True
    
    # 출발 노드로 부터 연결된 노드들의 거리를 갱신
    for j in graph[start]:
        distance[j[0]] = j[1]
    
    for i in range(n-1):
        now = get_smallest_node()
        visited[now] = True
        
        for j in graph[now]:
            cost = distance[now]  + j[1]
            
            # 현재 노드를 거쳐서 다른 노드로 이동하는 거리가 더 짧은 경우
            if cost < distance[j[0]]:
                distance[j[0]] = cost
                
dijkstra(start)

for i in range(1, n+1):
    if distance[i] == INF:
        print("INFINITY")
    else:
        print(distance[i])
```

- 총 O(V)번에 걸쳐서 최단 거리가 가장 짧은 노드르 매번 선형 탐색해야함
- V : 노드의 개수

### 우선순위 큐(Priority Queue)
- 우선순위가 가장 높은 데이터를 가장 먼저 삭제하는 자료구조
- 여러 개의 물건 데이터를 자료구조에 넣었다가 가치가 높은 물건 데이터부터 꺼내서 확인해야 하는 경우에 우선순위 큐를 이용

### 힙(Heap)
- 우선순위 큐를 구현하기 위해 사용하는 자료구조 중 하나임
- 최소 힙(Min Heap)과 최대 힙(Max Heap)이 있음
- 삽입, 삭제 시간 복잡도가 O(logN)임, 리스트로 구현시 삽입은 O(1), 삭제는 O(N)임

```python
import heapq

# 오름차순 힙 정렬
def heapsort(iterable):
    h = []
    result = []
    
    # 모든 원소를 차례대로 힙에 삽입
    for value in iterable:
        heapq.heappush(h, value)
        
    # 힙에 삽입된 모든 원소를 차례대로 꺼내어 담기
    for _ in range(len(h)):
        result.append(heapq.heappop(h))
    return result

result = heapsort([1, 3, 5, 7, 9, 2, 4, 6, 8, 0])
print(result)
```

```python
import heapq

# 내립차순 힙 정렬
# 넣은때 부호를 바꾸어 넣고 꺼낼 때 부호를 바꾸어서 꺼냄
def heapsort(iterable):
    h = []
    result = []
    
    # 모든 원소를 차례대로 힙에 삽입
    for value in iterable:
        heapq.heappush(h, -value)
        
    # 힙에 삽입된 모든 원소를 차례대로 꺼내어 담기
    for _ in range(len(h)):
        result.append(-heapq.heappop(h))
    return result

result = heapsort([1, 3, 5, 7, 9, 2, 4, 6, 8, 0])
print(result)
```

### 다익스트라 알고리즘 : 개선된 구현 방법
- 단계마다 방문하지 않은 노드 중에서 최단 거리가 가장 짧은 노드를 선택하기 위해 힙(Heap) 자료구조를 이용
- 기본 동작 원리는 동일
    - 현재 가장 가까운 노드르 저장해 놓기 위해서 힙 자료구조를 추가적으로 이용한다는 점이 다름
    - 현재의 최단 거리가 가장 짧은 노드를 선택해야 하므로 최소 힙을 사용

```python
import sys

# 방문처리를 기록하는 visited가 필요 없음
# 최소 거리를 찾는 함수를 힙으로 대체
INF = int(1e9)

n, m = 6, 11
start = 1

graph = [
    [],
    [(2,2), (3,5), (4,1)],
    [(3,3), (4,2)],
    [(2,3), (6,5)],
    [(3,3), (5,1)],
    [(3,1), (6,2)],
    []
]

distance = [INF] *(n+1)

def dijkstra(start):
    q = []
    
    heapq.heappush(q, (0, start))
    distance[start] = 0
    
    while q:
        dist, now = heapq.heappop(q)
        
        # 현재 노드가 이미 처리된 적이 있는 노드라면 무시
        if distance[now] < dist:
            continue
            
        # 현재 노드와 연결된 다른 인접한 노드들을 확인
        for v in graph[now]:
            cost = dist + v[1]
            
            if cost < distance[v[0]]:
                distance[v[0]] = cost
                heapq.heappush(q, (cost, v[0]))
                
dijkstra(start)

for i in range(1, n+1):
    if distance[i] == INF:
        print("INFINITY")
    else:
        print(distance[i])
```

### 다익스트라 알고리즘 : 개선된 구현 방법 성능 분석
- 힙 자료구조를 이용하는 다익스트라 알고리즘의 시간 복잡도는 O(ElogV)임
    - E : 간선의 개수
    - V : 노드의 개수

### 플로이드 워셜 알고리즘 개요
- 모든 노드애서 다른 모든 노드까지의 최단 경로를 모두 계산
- 플로이드 워셜(Floyd-Warshall) 알고리즘은 다익스트라 알고리즘과 마찬가지로 단계별 거쳐가는 노드를 기준으로 알고리즘을 수행
- 다만 매 단계마다 방문하지 않은 노드 중 최단 거리를 갖는 노드를 차즌ㄴ 과정이 필요하지 않음
- 플로이드 워셜은 2차원 테이블에 최단 거리 정보를 저장
- 플로이드 워셜 알고리즘은 다이나믹 프로그래밍 유형에 속함

### 플로이드 워셜 알고리즘
- 각 단계마다 특정한 노드 k를 거쳐가는 경우를 확인
- a에서 b로 가는 최단 거리보다 a에서 k를 거쳐 b로 가는 거리가 더 짧은지 검사 합

```python
INF = int(1e9)

n = 4

graph =[
    [],
    [INF, 0, 4, INF, 6],
    [INF, 3, 0, 7, INF],
    [INF, 5, INF, 0, 4],
    [INF, INF, INF, 2, 0]
]

for k in range(1, n+1):
    for a in range(1, n+1):
        for b in range(1, n+1):
            graph[a][b] = min(graph[a][b], graph[a][k] + graph[k][b])
        
for a in range(1, n+1):
    for b in range(1, n+1):
        if graph[a][b] == INF:
            print("INFINITY", end=" ")
        else:
            print(graph[a][b], end=" ")
    print()
```

### 플로이드 워셜 알고리즘 성능 분석
- 노드의 개수가 N개일 때 알고리즘 상으로 N번의 단계를 수행합니다.
- 각 단계마다 O(N^2)의 연산을 통해 현재 노드를 거쳐 가는 모든 경로를 고려
- 따라서 프롤이드 워셜 알고리즘의 총 시간 복잡도는 O(N^3)입니다.
