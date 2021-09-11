---
title: "[Algorithm] 구현
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
description: 구현의 기초와 기본 문제들에 대해 설명한 글입니다.
---

### 구현
- 풀이를 떠올리는 것은 쉽지만 소스코드로 옮기기 어려운 문제
- 알고리즘은 간단한데 코드가 지나칠 만큼 길어지는 문제
- 실수 연산을 다루고, 특정 소수점 자리까지 출력해야 하는 문제
- 문자열을 특정한 기준에 따라서 끊어 처리해야 하는 문제
- 적절한 라이브러리를 찾아서 사용해야 하는 문제

### 상하좌우
- 여행자 A는 N x M 크기의 정사각형 공간 위에 서 있음
- 가장 왼쪽 위 좌표는 (1,1)이며, 가장 오른쪽 아래 자표는 (N,M)임
- 여행가 A는 상, 하, 좌, 우 방향으로 이동할 수 있으며, 시작 좌표는 항상 (1,1)임
- 계획서가 존재하는데
    1. L: 왼쪽으로 한 칸 이동
    2. R: 오른쪽으로 한 칸 이동
    3. U: 위로 한 칸 이동
    4. D: 아래로 한 칸 이동
- 최종 좌표를 출력

```python
n = 5
x, y = 1, 1
plans = ["R", "R", "R", "U", "D", "D"]

dx = [0,0,-1,1]
dy = [-1,1,0,0]
move_types = ["L", "R", "U", "D"]

for plan in plans:
    for i in range(len(move_types)):
        if plan == move_types[i]:
            nx = x + dx[i]
            ny = y + dy[i]
            
    if nx < 1 or ny < 1 or nx > n or ny > n:
        continue
        
    x, y = nx, ny

print(x,y)
```

### 시각
- 정수 N이 입력되면 00시 00분 00초부터 N시 59분 59초까지의 모든 시각중에서 3이 하나라도 포함되는 모든 경우의 수를 구하는 프로그램을 작성
- 모든 경우의 수 86,400초를 다 세어서 해결할 수 있는 문제
- 이러한 유형은 완전 탐색(Brute Forcing) 문제 유형이라고 불림
- 가능한 경우의 수를 모두 검사해보는 탐색 방법

```python
h = 5
count = 0

for i in range(h+1):
    for j in range(60):
        for k in range(60):
            if "3" in str(i) + str(j) + str(k):
                count+=1
                
print(count)
```

### 왕실의 나이트
- 왕실 정원의 체스판과같은 8 x 8 좌표 평면임
- 나이트는 마릉ㄹ 타고 있기 때문에 L자 형태로만 이동할 수 있으며 정원 밖으로 나갈 수 없음
- 나이트는 특정 위체에서 다음과 같은 2가지 경우로 이동 할 수 있음
    1. 수평으로 두 칸 이동한 뒤에 수직으로 한 칸 이동하기
    2. 수직으로 두 칸 이동한 뒤에 수평으로 한 칸 이동하기
- 좌표가 주어졌을 때 나이트가 이동할 수 있는 경우의 수를 출력

```python
row = 1
column = 1

steps = [(-2,-1), (-1,-2), (1,-2), (2,-1), (2,1), (1,2), (-1,2), (-2,1)]

result = 0

for step in steps:
    next_row = row + step[0]
    next_column = column + step[1]
    
    #print(next_row, next_column)
    
    if next_row >=1 and next_row <= 8 and next_column >= 1 and next_column <= 8:
        result += 1
        
print(result)
```

### 문자열 재정렬
- 알파벳 대문자 숫자(0~9)로만 구성된 문자열이 입력으로 주어짐
- 이때 모든 알파벳을 오름차순으로 정렬하여 이어서 출력한 뒤에, 그 뒤에 모든 숫자를 더한 값을 이어서 출력함

```python
data = "K1KA5CB7"

result = []
value = 0

for x in data:
    if x.isalpha():
        result.append(x)
    else:
        value+=int(x)
        
result.sort()

if value != 0:
    result.append(str(value))
    
print("".join(result))
```