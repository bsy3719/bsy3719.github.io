---
title: "[Algorithm] 이진탐색"
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
description: 이진탐색 알고리즘의 기초와 기본 문제들에 대해 설명한 글입니다.
---

### 이진 탐색 알고리즘
- 순차탐색 : 리스트 안에 특정한 데이터를 찾기 위해 앞에서부터 데이터를 하나씩 확인하는 방법
- 이진탐색 : 정렬되어 있는 리스트에서 탐색 범위를 절반씩 좁혀가며 데이터를 탐색하는 방법

```python
n, target = 10, 7
array = [1, 3, 5, 7, 9, 11, 13, 15, 17, 19]

def binary_search(array, target, start, end):
    if start > end:
        return None
    
    mid = (start + end)//2
    
    if array[mid] == target:
        return mid
    elif array[mid] > target:
        return binary_search(array, target, start, mid-1)
    else:
        return binary_search(array, target, mid+1, end)
    
result = binary_search(array, target, 0, n-1)

if result == None:
    print("원소가 존재하지 않습니다.")
else:
    print(result+1) # 몇번쨰 원소인지 알기위해 인덱스 값에 1을 더해서 출력
```

### 파이썬 이진 탐색 라이브러리
- bisect_left(a, x): 정렬된 순서를 유지하면서 배열 a에 x를 삽입할 가장 왼쪽 인덱스를 반환
- bisect_right(a, x): 정렬된 순서를 유지하면서 배열 a에 x를 삽입할 가장 오른쪽 인덱스를 반환

```python
from bisect import bisect_left, bisect_right

a = [1, 2, 4, 4, 8]
x = 4

print(bisect_left(a, x))
print(bisect_right(a, x))
```

### 값이 특정 범위에 속하는 데이터 개수 구하기

```python
from bisect import bisect_left, bisect_right

def count_by_range(a, left_value, right_value):
    right_index = bisect_right(a, right_value)
    left_index = bisect_left(a, left_value)
    return right_index - left_index

a = [1, 2, 3, 3, 3, 3, 4, 4, 8, 9]

print(count_by_range(a, 4, 4))

print(count_by_range(a, -1, 3))
```

### 파라메트릭 서치(Parametric Search)
- 파라메트릭 서치란 최적화 문제를 결정 문제로 바꾸어 해결하는 기법
- 특정한 조건을 만족하는 가장 알맞는 값을 빠르게 찾는 최적화 문제
- 일반적으로 코딩 테스트에서 파라메트릭 서치 문제는 이진 탐색을 이용하여 해결할 수 있음

### 떡볶이 떡 만들기
- 떡볶이 떡의 길이는 일정하지 않음
- 대신에 한 봉지 안에 들어가는 떡의 총 길이는 절단기로 잘라서 맞춰줌
- 절단기 높이(H)를 지정하면 줄이저진 떡을 한 번에 절단함
- 높이가 H보다 긴 떡은 H 위의 부분이 잘릴 것이고, 낮은 떡은 잘리지 않음
- 손님이 왔을 때 요청한 총 길이가 M일 때 적어도 M만큼의 떡을 얻기 위해 절단기에 설정할 수 있는 옾이의 최댓값을 구하는 프로그램을 작성하시오

```python
n, m = 4, 6
array = [19, 15, 10, 17]

start = 0
end = max(array)

result = 0

while(start<=end):
    total = 0
    mid = (start + end) // 2
    for x in array:
        if x > mid:
            total += x - mid
            
    if total < m:
        end = mid - 1
    else:
        result = mid
        start = mid + 1
        
print(result)
```
