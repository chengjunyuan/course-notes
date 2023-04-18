---
description: The first step in your journey with algorithms!
---

# Binary Search

Binary search is a classic searching algorithm, and we'll use this section on binary search to introduce some concepts that will be common to all subsequent algorithms we study. For a very visual demonstration of binary search, refer to [this video](https://www.youtube.com/watch?v=DSffdCT5Cx4) of CS50's David Malan ripping a book apart with his bare hands.

## Algorithm Details

Before writing our algorithms, we usually flesh them out by providing a problem they are intended to solve, as well as discussing the intended inputs, outputs, and general requirements for our code.

**Problem:** we have a sorted array of integers, and we want to check if a specific integer $$n$$ is present within the array.

**Algorithm Input:** a sorted array of integers $$A$$ and an integer $$n$$ that we are looking for.

**Algorithm Output:** an integer $$s$$, denoting the array index containing our target integer $$n$$. If the target integer $$n$$ is not present in the array, we will return $$-1$$.

### The Naive Approach

Remark: For the vast majority of our Searching and Sorting section, we will assume that we do not have access to the many common libraries and functions that allow us to solve these problems with one-liners. Our goal here is to build the functions that provide these one-line solutions, and understand how they work under the hood.

The naive approach to this sorting problem is to therefore proceed from the first index of the list, and work our way through until we find the correct one. This may result in an algorithm that looks like the following:

```python
def naivesearch(A, n):
    length = len(A)
    currIndex = 0
    while currIndex < length:
        if A[currIndex] == n:
            return currIndex
        else:
            currIndex += 1
    return -1
```

In the worst case, the number we are looking for will be the very last element of the array, and it will take us $$O(n)$$ time to find our desired number. We now turn to a _divide-and-conquer_ strategy known as binary search.

### Binary Search

Recall from the algorithm input that the array we are being given is already sorted. This is the property that we will exploit in our search algorithm. The broad strokes of the algorithm are as follows:

1. Pick the middle element of the array, and compare it to our target number.
2. If the middle element is smaller than the target number, that means the target is to the right of the middle (if it is in the array), and we continue our search there. If it is larger, we continue our search in the first half of the array, and we continue our search there.
3. Repeat until we find the target number, or do not find it.

In this algorithm, observe that we can reduce the search space by half at every step. This is clearly faster than the naive approach presented above. This algorithm is known as _binary search_, because we narrow the target range by half at every iteration of the algorithm. Our binary search algorithm may look something like this:

```python
def binary_search(A, n):
    length = len(A)
    mid = int(length / 2)
    if length == 0:
        return -1
    elif A[mid] == n:
        return mid
    elif A[mid] < n:
        binary_search(A[mid:], n)
        # in Python, this takes the second half of the array
    else:
        binary_search(A[:mid], n)
        # in Python, this takes the first half of the array
```

We claim that this binary search algorithm has a time complexity of $$O(\log n)$$.&#x20;

