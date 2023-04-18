---
description: A quick introduction to time and space complexity.
---

# Algorithmic Analysis

When writing programs, we usually think about the time and space complexity of the program. The complexity of a program tells us how the time/space usage of the program varies according to the input size.

## Big-O Notation

There are a great many programs in the world, and it would be impossible to classify all of them. Therefore, when we discuss the complexity of a particular program, we usually do it with reference to a known "standard" program which acts as a benchmark. We describe the relationship between the program we're analyzing and one of the "standard" benchmark programs using what is known as big-O notation. Big-O notation provides us with three terms to describe the program - an upper bound, lower bound, and tight bound.

Throughout the following sections, the term $$T(n)$$refers to the running time of the algorithm, i.e., the number of operations performed when given an input of size $$n$$.

### Definitions

**Definition (Upper Bound):** We say that a program has $$O(f(n))$$ complexity if $$T(n)=O(f(n))$$, i.e., $$T$$ grows no faster than $$f$$ and $$T(n)\leq cf(n)$$ for some constant $$c$$.

**Definition (Lower Bound):** We say that a program has $$\Omega(f(n))$$ complexity if $$T(n)=\Omega(f(n))$$, i.e., $$T$$ grows no slower than $$f$$ and $$T(n)\geq cf(n)$$ for some constant $$c$$.

**Definition (Tight Bound):** We say that a program has $$\Theta(f(n))$$ complexity if $$T(n)=O(f(n))$$ and $$T(n)=\Omega(f(n))$$.

### Simple Examples

We illustrate the concepts using a few examples. All examples in this section are written in Python.

```python
def sum(n):
    ans = 0
    for i in range(n):
        ans += 1
    return ans
```

The `sum` function has a time complexity of $$\Omega(n)$$. Observe that the number of operations $$n$$ increases, so does the number of addition operations performed in the `for` loop. Our next example shows a function with a time complexity of $$\Omega(n^2)$$

```python
def square(n):
    ans = 0
    for i in range(n):
        for j in range(n):
            ans += 1
    return ans
```

Observe that the outer `for` loop is run $$n$$ times, and for each iteration of the outer `for` loop, the inner `for` loop is executed, with `n` operations being performed. This yields a total of $$n\times n=n^2$$ operations being performed.

Further exposition on the topic of time and space complexity will be provided when we start to encounter more algorithms.

## General Remarks

* Programmers are generally quite concerned about the time and space complexity of a program - we want to write code that runs quickly and doesn't take up a lot of memory space!
* Although the two examples here both have a tight bound $$\Omega(f(n))$$, programmers usually take a more pessimistic viewpoint and are only concerned by the upper bound $$O(f(n))$$, as we want to build systems that can tolerate worst case scenarios.

