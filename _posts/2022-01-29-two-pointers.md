---
layout: post
title: Two Pointers
tags: twoPointers, codingprep
math: false
date: 2022-01-29 14:22 +0800
---

## 1. Pair with Target Sum

**Problem Description:**\
Given an array of sorted numbers and a target sum, find a pair in the array whose sum is equal to the given target.

Write a function to return the indices of the two numbers (i.e. the pair) such that they add up to the given target.

Input: [1, 2, 3, 4, 6], target=6
Output: [1, 3]
Explanation: The numbers at index 1 and 3 add up to 6: 2+4=6

**Problem Explanation:**\
Having two pointers involves in this case having to integers keeping track of two different positions.\
Now you may wonder, how does this benefit us?\
Well in this case, we can use the two pointers one on the left side (position = 0) and another at the end of the array (position = arr.size() - 1) and iterate through the array trying to see if the sum value at those two positions are equal to target.

- If the sum is equal to target, perfect we have our pair.
- If the sum is less than target, then we need to increase our left position.
- If the sum is more than the target, then means the value at the right pos is too big and we need to decrease it one position and try again.

**C++**
{% highlight js linenos %}
static pair<int, int> search(const vector<int> &arr, int targetSum) {
  int left = 0, right = arr.size() - 1;
  while (left < right) {
    int currentSum = arr[left] + arr[right];
    if (currentSum == targetSum) { // found the pair
      return make_pair(left, right);
    }
    if (targetSum > currentSum){
      left++; // we need a pair with a bigger sum
    else{
      right--; // we need a pair with a smaller sum
    }
    return make_pair(-1, -1);
}
{% endhighlight %}
