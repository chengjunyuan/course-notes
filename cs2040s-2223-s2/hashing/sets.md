---
description: Fancy boolean arrays.
---

# Sets

In our previous discussions of hashing, we want to store objects and retrieve them quickly, leading to hashmaps. We now consider a slightly different problem - what if all we want to known is whether the element is present?

Although the naive solution is to use a boolean array of some form, we quickly run into trouble because the keys we are inserting are not necessarily integers, and they may not have any defined order. For example, in an attendance taking system, we will need to map strings to a boolean value.

We therefore consider the `Set` data structure, which possesses the following key operations.

| Return type | Function                |
| ----------- | ----------------------- |
| `void`      | `insert(Key k)`         |
| `boolean`   | `contains(Key k)`       |
| `void`      | `delete(Key k)`         |
| `void`      | `intersect(Set<Key> s)` |
| `void`      | `union(Set<Key> s)`     |

We want our new `Set` data structure to also possess what is basically $$O(1)$$ insertion, deletion, and retrieval time as well.

### Attempt 1: Hash Tables

Although a hash table supports all the operations required by the `Set` data structure, it unfortunately takes up more space than a list. It therefore does not satisfy the requirements of our new data structure, which is that it is small and fast. (This is precisely the point of using a `Set` instead of a hash table - we only want to check if it is present, not store the entire object.)

### Attempt 2: Fingerprint Hash Tables (FHTs)

Our second attempt also involves hashing, but with a slightly different hash function. In a fingerprint hash table (FHT), our new hash function maps our object to a non-negative integer (which will be the array index of the boolean array). To denote an object being present, we change the value of the array at that index to `true`.

<figure><img src="../.gitbook/assets/Screenshot 2023-04-19 at 10.10.19 AM.png" alt=""><figcaption><p>Taken from Lecture 16 slides.</p></figcaption></figure>

In the example above, our new hash function maps the string `www.gmail.com` to the integer `2`, and maps the string `www.apple.com` to the integer `3`.

The good news is that with FHTs, it is impossible to obtain a false negative result. The bad news is that it is _relatively_ easy to obtain a false positive result (i.e., returning `true` when the object isn't actually present, as a result of a hash collision).

In a lookup table of size m with n elements, the probability that no false positives occur is

$$
\left(1-\frac{1}{m}\right)^n\approx \left(\frac{1}{e}\right)^{\frac{n}{m}},
$$

which means that the probability of a false positive occurring is 1- that. In order to minimize our error rate (or keep it at a comfortable level), it is therefore important to pick a good table size.

To summarize, FHTs are good because we only need to use 1 bit per slot in our lookup table, saving space. However, we also need to use bigger tables to prevent hash collisions, increasing space usage anyways. We therefore turn to a slightly upgraded form of FHTs, knwon as Bloom filters.

### Attempt 3: Bloom Filters

The idea behind Bloom filters is simple: we should just apply a few hash functions. Suppose we apply $k$ hash functions - we therefore need $k$ collisions in order for a false positive to occur. By doing this, we can drive down the probability of a false positive occurring, at the cost of increased time complexity (since we are doing more hashing). Thankfully, we assume that hashing is actually O(1), so the increased number of hashes does not add to time complexity.

### Summary

In Java, we can implement this `Set` data structure through the use of the concrete `HashSet` class which implements `Set`. We see that using sets is a space-efficient way of storing data about our objects.
