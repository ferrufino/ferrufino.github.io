---
layout: post
title: Sliding Window Pattern II
tags: slidingWindow, codingprep
math: false
date: 2021-10-08 22:18 +0800
---

Welcome to the next part of solving 'Sliding Window' problems! I'll continue at the same level as the previous post and gradually increase complexity.

Let me know your thoughts on Twitter, enjoy!

## Smallest Subarray with a given sum

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
This might sound like an intimidating question for some as you cannot clearly infer where a window might be applied here. But if you look closely at the example, and specially at how you would solve it on paper. This translate to a problem we previously solved: **"Longest Substring with no more than K distinct chars"** with our K being equal to 2.

Having said the above, we add the latest char to our right side of the window, as we traverse the array.\
And remove a char from the start of our window while our 'char frequency dictionary' is greater than 2.\
Only after doing the above, can we start getting the max length of window.

**C++**

```
static int fruitsIntoBaskets(const string &str) {
    int windowStart = 0, maxLength = 0;
    unordered_map<char, int> charFrequencyMap;
    for (int windowEnd = 0; windowEnd < str.length(); windowEnd++) {
        char rightChar = str[windowEnd];
        charFrequencyMap[rightChar]++;

        while ((int)charFrequencyMap.size() > 2) {
            char leftChar = str[windowStart];
            charFrequencyMap[leftChar]--;
            if (charFrequencyMap[leftChar] == 0) {
                charFrequencyMap.erase(leftChar);
            }
            windowStart++; // shrink the window
        }
         // remember the maximum length so far
        maxLength = max(maxLength, windowEnd - windowStart + 1);
    }
    return maxLength;
}
```

## Longest Substring with k letters replaced

**Problem Description:**\
Given a string with lowercase letters only, if you are allowed to replace no more than k letters with any letter, find the length of the longest substring having the same letters after replacement.

> Input: String="aabccbb", k=2
> Output: 5
> Explanation: Replace the two 'c' with 'b' to have the longest repeating substring "bbbbb".

> Input: String="abbcbbac", k=2
> Output: 6
> Explanation: Replace the 'a' and 'c' with 'b' OR the 'c' and the last 'a' with 'b' to have the longest repeating substring "bbbbbb".

**Problem Explanation:**\
We know our substring (or ideal window) will contain the 'longest frequency letter', from the example above that is 'b', and N letters to be replaced. That N needs to be less than or equal to 'k'.

To be able to identify the N letters. We can simply get the window size and substract the most 'frequent letter'. We don't care if N letters are the same or not, we just want to be sure their count is less than or equal to 'k'.

So then:

```
We traverse the string
  Add a new char to a map of frequency letters
  We get the max frequency letters between the added letter vs our count
  
  We then check if 'window size' minus 'max frequency of letters'
  (N count) is greater than 'k'
    true: then reduce window size and frequency for char removed.

  Get max window size
```

**C++**

```
  static int findLength(const string& str, int k) {
    int maxLength = 0;
    int maxFrequencyLetter = 0;
    unordered_map<char, int> frequencyCount = unordered_map<char, int>();
    int windowStart = 0;

    for(int i = 0; i < str.length(); i++){
      char letter = str[i];
      frequencyCount[letter]++;
      maxFrequencyLetter = max(maxFrequencyLetter, frequencyCount[letter]);

      if(i - windowStart + 1 - maxFrequencyLetter > k){
        //reduce window size
        char startingLetter = str[windowStart];
        frequencyCount[startingLetter]--;
        windowStart++;
      }

      maxLength = max(i - windowStart + 1, maxLength);
    }

    return maxLength;
  }
```
