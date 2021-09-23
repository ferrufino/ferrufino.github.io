---
layout: post
title: Sliding Window Pattern I
tags: slidingWindow, codingprep
math: false
date: 2021-09-22 13:10 +0800
---

This pattern evaluates sub-windows within an array/string/linked-list.\
We keep a sub-window of evaluation and when a condition is met, it 'slides' to the right.

It usually applies to problems that need to evaluate if find a continuous group (substring/subarray) are true to a desired condition.

Below I describe few samples of this pattern being applied in C++ and Swift.

## Maximum sum subarray of Size K

**Problem Description:**\
Given an array of positive numbers and a positive number ‘k,’ find the maximum sum of any contiguous subarray of size ‘k’.\

> Input: [2, 1, 5, 1, 3, 2], k=3\
> Output: 9\
> Explanation: Subarray with maximum sum is [5, 1, 3].

**Problem Explanation:**\
Window here is kept in size K. It adds a new entry on the right of 'arr' and subtract first entry (left entry) to always keep the sum size K.

**C++**

```
static int findMaxSumSubArray(int k, const vector<int>& arr) {
	int maxSum = 0;
	int wStart = 0;
	int currentSum = 0;
	for(int wEnd = 0; wEnd < arr.size(); wEnd++){
		currentSum += arr[wEnd];
		if(wEnd >= k-1){
			maxSum = max(maxSum, currentSum);
			currentSum -= arr[wStart];
			wStart++;
		}
	}
	return maxSum;
}
```

## Longest Substring with no more than K distinct chars

**Problem Description:**\
Find the length of the longest substring in it with no more than K distinct characters

> Input: String="cbbebi", K=3\
> Output: 5\
> Explanation: The longest substrings with no more than '3' distinct characters are "cbbeb" & "bbebi".

**Problem Explanation:**\
In this case, we keep track of unique chars in a dictionary and frequency of chars.\
If size of dict > k, then we have exceeded our k limit and need to remove from the window start chars in the dictionary:\
  while dict.size() > k from windowStart remove char from dictionary.\
Then we get max count(length of window: 'windowEnd - windowStart + 1') of chars that are equal or below k size.

**C++**

```
static int findLength(const string &str, int k) {
    int windowStart = 0, maxLength = 0;
    unordered_map<char, int> charFrequencyMap;
    for (int windowEnd = 0; windowEnd < str.length(); windowEnd++) {
        char rightChar = str[windowEnd];
        charFrequencyMap[rightChar]++;

        while ((int)charFrequencyMap.size() > k) {
            char leftChar = str[windowStart];
            charFrequencyMap[leftChar]--;
            if (charFrequencyMap[leftChar] == 0) {
                charFrequencyMap.erase(leftChar);
            }
            windowStart++; // shrink the window
        }
        maxLength = max(maxLength, windowEnd - windowStart + 1); // remember the maximum length so far
    }
    return maxLength;
}
```

## Longest Substring without repeating char

**Problem Description:**\
Find the length of the longest substring without any repeating char.

**Problem Explanation:**\
If iterating char already exists in dictionary and the position of it is higher than the window start\
  Update window start to val + 1 to skip that char\
Save pos of encounter of char\
Find max length

**Swift**

```
func lengthOfLongestSubstring(_ s: String) -> Int {
        var start: Int = 0
        var maxSum: Int = 0
        var dict = Dictionary<Character, Int>()

        for(end, char) in s.enumerated() {
            if let val = dict[char], val >= start {
                start = val + 1
            }

            dict[char] = end
            maxSum = max(maxSum, end - start + 1)
        }

        return maxSum
    }
```
