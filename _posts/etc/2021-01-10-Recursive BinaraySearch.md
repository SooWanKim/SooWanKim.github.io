---
layout: post
title: Recursive BinarySearch
categories: [ETC, Python]
---

## 기능

이진탐색을 기본, 재귀로 구현

### BinarySearch
```python
def binary_search(sorted_list, item):
    low = 0
    high = len(sorted_list) - 1
    while low <= high:
        mid = int((low + high) / 2)
        guess = sorted_list[mid]

        if guess == item:
            return mid
        elif guess < item:
            low = mid
        else:
            high = mid

    return None
```

***

### Recursive BinarySearch
```python
def recursive_binary_search(low, high, sorted_list, item):
    mid = int((low + high) / 2)
    guess = sorted_list[mid]

    if low > high:
        return None

    if guess == item:
        return mid
    elif guess < item:
        low = mid + 1
    else:
        high = mid - 1

    return recursive_binary_search(low, high, sorted_list, item)

```

***

```python
my_list = [i for i in range(1, 10)]

low = 0
high = len(my_list) - 1

answer = recursive_binary_search(low, high, my_list, 4)
print(answer)
answer = binary_search(my_list, 3)
print(answer)
```

