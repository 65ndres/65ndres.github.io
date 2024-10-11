---
title: Big O Notation
date: 2024-01-03 12:00:00 -500
categories: [Back to Basics]
tags: [Basics, Computer Science, Big O Notation, Python ]
---


I'm gonna start this series called Back to Basics with one of the most important topics: Big O. This topic 
intimidated alot the first time I saw since its crucil to undertand the impact you code will have when the size if the input n increases.

## Game Plan

* What it is.
* Tiers to solve for.
* Major complexities.
* Code examples.


### What is it

Big O defines the runtime needed to execute an algorithm by identifying how the performance of your algorithm will change as the input size grows. Big O measures the efficiency and performance of your code using time and space complexity.

### Tiers to solve for

When talking about Big O notation it's important to understand the concept of Time and Space complexity.
Tipically there are three tiers to solve for (Best case, average case and worst case) which are known as assymtomatic notations.

- Best case - Big Omega
- Average case - Big Theta
- Worst case - Big O

Developers typically solve for the worse case scenerio, Big O, because it allows you to make analytical statements such as: "in the worst case scenario, my algo will scale quickly".

### Major Complexities

in Big O, there are six mejor type of complexities (time and space) sorted from best to worst:

1. Contant: `O(1)`
2. Logarithmic time: `O(n log n)`
3. Linear time:`O(n)`
4. Quadratic time:`O(n^2)`
5. Exponential time: `O(2^n)`
6. Factorial time: `O(n!)`

#### How to know which algo has which complexity

1. When your calculation is not dependent on the input size, it is a constant time complexity `O(1)`.
2. When the input size is reduced by half, maybe when iterating, handling recursion, or whatsover, it is algorithmic time complexity `O(n log n)`.
3. when you have a single loop whithin your algo, it is linear time complexity `O(n)`.
4. when you have nested loops whithin your algo, meaning a loop in a loop, it is quadratic tieme complexity `O(n^2)`.
5. when the growth rate dubles within each addition to the input, it is exponential time complexity `O(2^n)`.


### Code Examples

#### Constant Time: `O(1)`

When your algo is not dependent on the input size n, it is said to have a constant time complexity with order `O(1)`. 

In the case bellow the algorithm is to return the first element of an array. Even if the array has 1 million elements.

```python
# Define the array
array = [1..1000000]

def get_first_element(array)
  first_element = array[0]
  print("First element:", first_element)

get_first_element(array)
```
#### Linear Time: `O(n)`
You get linear time complexity when the running time of an algorithm increases linearly with the size of the input.

```python
def factorial(n):
    result = 1
    for i in range(1, n + 1):
        result *= i

    return result

n = 5
print(f"Factorial of {n} is {factorial(n)}")
```

#### Logarithm Time: `O(nlog n)`
This one is similar to linear time complexity, expect that the runtime does not depend on the input size but rather on half the input size. When the input size decreases on each iteration or step. A great example is binary search function, which devide your sorted array based on the target value.

```python
def binary_search(arr, target):
    low = 0
    high = len(arr) - 1
    while low <= high:
        mid = (low + high) // 2
        if arr[mid] == target:
            return mid  # Return the index of the target
        elif arr[mid] > target:
            high = mid - 1
        else:
            low = mid + 1
    # If target not found, return -1
    return -1

# Example usage:
arr = [1, 3, 5, 7, 9, 11, 13, 15]
target = 7

result = binary_search(arr, target)

if result != -1:
    print(f"Element {target} found at index {result}")
else:
    print(f"Element {target} not found in the array")

```


#### Quadratic time `O(n^2)`
When you perform nested iterations, meaning having a loop in a loop, the time complexity becomes horrible.
A good example would be having your algo iterate over an array of n elements and an inner loop that will run n time for each iteration of the outer loop.

```python
data = { 'first': [1..100], 'second': [1..100], 'third': [1..100]}

def read_dataset(data)
    for key in data.keys():
        for value in data[key]:
            print(value)

read_dataset(data)
```

#### Exponential Time: `O(2^n)`

Occurs when the growth rate doubles with each addition to the input (n), ofter iterating through all subsets of the input element

The recursive Fibonnacci sequence is a good example because bc each number is the sum of the last two prceding numbers, where 0 and 1 are the first two numbers. The third number of the sequence is 1, the fourth is 2, the fifth is 3 and so on.

```python
def fibonacci(n):
    if n <= 1:
        return n
    else:
        return fibonacci(n - 1) + fibonacci(n - 2)
n = 7
print(f"Fibonacci of {n} is {fibonacci(n)}")

```
