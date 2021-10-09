---
layout: post
title: Sliding Window Pattern II
tags: slidingWindow, codingprep
math: false
date: 2021-10-08 22:18 +0800
---

Welcome to the next part of solving 'Sliding Window' problems! I'll continue at the same level as the previous post and gradually increase complexity.

Let me know your thoughts on Twitter, enjoy!

## Smalles Subarray with a given sum

**Problem Description:**\
Given an array of positive numbers and a positive number ‘S,’ find the length of the smallest contiguous subarray whose sum is greater than or equal to ‘S’. Return 0 if no such subarray exists.

> Input: [3, 4, 1, 1, 6], S=8 \
> Output: 3\
> Explanation: Smallest subarrays with a sum greater than or equal\
>  to '8' are [3, 4, 1] or [1, 1, 6].

> Input: [2, 1, 5, 2, 3, 2], S=7\
> Output: 2\
> Explanation: The smallest subarray with a sum greater than or equal 
> to '7' is [5, 2].

**Problem Explanation:**\
Trying to find the smallest contiguous subarray translates to reducing our window size while we have a window size sum that is **greater or equal** to S

If the sum of our subarray is not greater or equal to S, then we continue expanding our window size.

This comes down to:

```
traverse array
   add each val to currentWindowSum
   while greater or equal to S
      get window size
      compare with min window size
      remove start from window size
      move start ++
return min window size
```

Solution in C++

```
for(int i = 0; i<arr.size(); i++){
   currentWindowSum += arr[i];
   while(currentWindowSum >= S){
      windowSize = i - left + 1;
      minWindowSize = min(windowSize, minWindowSize);
      currentWindowSum -= arr[left];
      left++;
   }
}

return minWindowSize;
```

## Fruits into Baskets

**Problem Description:**\
Given an array of characters where each character represents a fruit tree, you are given **two baskets**, and your goal is to put **maximum number of fruits in each basket**. The only restriction is that **each basket can have only one type of fruit.**

> Input: Fruit=['A', 'B', 'C', 'A', 'C']\
> Output: 3\
> Explanation: We can put 2 'C' in one basket and one 'A' in the other\
> from the subarray ['C', 'A', 'C']

**Problem Explanation:**\
This might sound like an intimidating question for some as you cannot clearly infer where a window might be applied here. But if you look closely at the example, and specially at how you would solve it on paper. This translate to a problem we previously solved: "Longest Substring with no more than K distinct chars" with our K being equal to 2.

Having said the above, we add the latest char to our right side of the window, as we traverse the array.\
And remove a char from the start of our window while our 'char frequency dictionary' is greater than 2.\
Only after doing the above, can we start getting the max length of window.
