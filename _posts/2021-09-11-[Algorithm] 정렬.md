---
title: "[Algorithm] 정렬"
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
description: 정렬 알고리즘의 기초와 기본 문제들에 대해 설명한 글입니다.
---

### 정렬
- 정렬(Sorting)이란 특정한 기준에 따라 순서대로 나열하는 것을 말함
- 일반적으로 문제 상황에 따라서 적절한 정렬 알고리즘이 공식처럼 사용됨

### 선택정렬
- 처리되지 않은 데이터 중에서 가장 작은 데이터를 선택해 맨 앞에 있는 데이터와 바꾸는 것을 반복함

```python
array = [7, 5, 9, 0, 3, 1, 6, 2, 4, 8]

for i in range(len(array)):
    min_index = i
    
    for j in range(i+1, len(array)):
        if array[min_index] > array[j]:
            min_index = j
            
    array[i], array[min_index] = array[min_index], array[i]
    
print(array)
```

### 삽입 정렬
- 처리되지 않은 데이터를 하나씩 골라 적절한 위치에 삽입
- 선택 정렬에 비해 구현 난이도가 높은 편이지만, 일반적으로 더 효율적으로 동작함

```python
array = [7, 5, 9, 0, 3, 1, 6, 2, 4, 8]

for i in range(1, len(array)):
    for j in range(i, 0, -1): # 인덱스 i부터 1까지 1씩 감소하며 반복하는 문법
        if array[j] < array[j-1]:
            array[j], array[j-1] = array[j-1], array[j]
        else:
            break
            
print(array)
```

### 퀵 정렬
- 기준 데이터를 설정하고 그 기준보다 큰 데이터와 작은 데이터의 위치를 바꾸는 방법
- 일반적인 상황에서 가장 많이 사용되는 정렬 알고리즘 중 하나
- 가장 기본적인 퀵 정렬은 첫 번째 데이터를 기준 데이터(Pivot)로 설정함

```python
array = [7, 5, 9, 0, 3, 1, 6, 2, 4, 8]

def quick_sort(array, start, end):
    if start >= end:
        return
    
    pivot = start
    left = start + 1
    right = end
    
    while(left <= right):
        while(left <= end and array[left] <= array[pivot]):
            left += 1
        while(right > start and array[right] >= array[pivot]):
            right -= 1
            
        if(left > right):
            array[right], array[pivot] = array[pivot], array[right]
        else:
            array[left], array[right] = array[right], array[left]
            
    quick_sort(array, start, right -1)
    quick_sort(array, right + 1, end)
    
quick_sort(array, 0, len(array)-1)
print(array)
```

```python
array = [7, 5, 9, 0, 3, 1, 6, 2, 4, 8]

def quick_sort(array):
    
    if len(array) <= 1:
        return array
    
    pivot = array[0]
    tail = array[1:]
    
    left_side = [x for x in tail if x <= pivot]
    right_side = [x for x in tail if x > pivot]
    
    return quick_sort(left_side) + [pivot] + quick_sort(right_side)

print(quick_sort(array))
```

### 계수 정렬
- 특정한 조건이 부합할 때만 사용할 수 있지만 매우 빠르게 동작하는 정렬 알고리즘
- 계수 정렬은 데이터의 크기 범위가 제한디어 정수 형태로 표현할 수 있을 때 사용 가능
- 데이터의 개수가 N, 데이터(양수) 중 최댓값이 K일 때 최악의 경우에도 수행시간 O(N+K)를 보장

```python
array = [7, 5, 9, 0, 3, 1, 6, 2, 9, 1, 4, 8, 0, 5, 2]

count = [0] * (max(array) + 1)

for i in range(len(array)):
    count[array[i]] += 1
    
for i in range(len(count)):
    for j in range(count[i]):
        print(i, end=" ")
```

### 두 배열의 원소 교체
- 두 개의 배열 A와 B를 가지고 있음
- 두 배열은 N개의 원소로 구성되어 있으며, 배열의 원소는 모두 자연수임
- 최대 K번의 바궈치기 연산을 수행할 수 있는데, 바꿔치기 연산이란 배열 A에 있는 원소 하나와 배열 B에 있는 원소 하나를 골라 두 운소를 서로 바꾸는 것
- 배열 A의 모든 원소의 합이 최대가 되도록 하는 것
- N, K 그리고 배열 A와 B의 정보가 주어졌을 때, 최대 K번의 바꿔치기 연산을 수행하여 만들 수 있는 배열 A의 모든 원소의 합의 최댓값을 출력하는 프로그램을 작성

```python
n, k = 5, 3
a = [1, 2, 5, 4, 3]
b = [5, 5, 6, 6, 5]

a.sort()
b.sort(reverse=True)

for i in range(k):
    if a[i] < b[i]:
        a[i], b[i] = b[i], a[i]
    else: # A의 원소가 B의 원소보다 크거나 같을 때, 반목문을 탈출
        break
        
print(sum(a))
```