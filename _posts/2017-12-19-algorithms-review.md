---
layout:     post
title:      Algorithms Review
date:       2017-12-19
summary:    Data Structures and Algorithms (concepts and theories) Review
categories: Algorithms
---

This post is to keep track of some interesting but easily forgotten data structures and algorithms concepts & theories.

### NP, NP-complete, NP-Hard

### Minimum Spanning Tree (MST)

### KMP
* The max prefix matching postfix table

### Maximum Flow
* find a path from src to dst, increase the flow on each edge in the path by the minimum capacity on the path (don't forget to increase the capacity on the backward edge)
* Flow increased on backward edge is for a potential canceling out the flow on the forward edge later

### Binary Search LowerBound and UpperBound
* LowerBound(C++)/bisect_left(Python): return the index of the first element that is equal or greater than the target element
* UpperBound(C++)/bisect_right(Python): return the index of the first element that is greater than the target element

### Binary Index Tree Algorithm (BIT)
* Get the right first bit with offset for `int`: `x & -x`. Example: `12 = 0b1100`, `12 & -12 = 0b100 = 4`

### Longest Increasing Subsequence
* Time complexity should be O(NlogN):
	- method 1: binary search on an array consisting of tails of the previous increasing subsequence
	- method 2: Binary index tree, sum over a 0/1 array indexes of the sorted array? (raw thought, not confirmed yet)

### Maximum Subarray Problem (MSP)
* Kadane’s Algorithm (DP)
	- Calculate maximum ending here so far;
	- Time complexity: `O(N)`, extra space complexity: `O(1)`
* Maximum subarray sum that is less than `K`
	- Use a tree/set to store previous acc_sum, and find the smallest one among all the ones that are bigger than current_acc_sum - K;
	- Time complexity: `O(NlogN)`
* Kadane's algo can be extended to solve max rectangle in a `n` rows `m` columns matrix with time `O(m^2 * n)` (assume `n > m`)
