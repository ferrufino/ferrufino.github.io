---
layout: post
title: Sliding Window Pattern II
tags: slidingWindow, codingprep
math: false
date: 2021-10-08 22:18 +0800
---

Welcome to the next part of solving 'Sliding Window' problems part II! I'll continue at the same level as the previous post and gradually increase complexity.

## 1. Longest Substring with Same Letters after Replacement

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

To be able to identify the N letters. We can simply get the window size and substract the max most 'frequent letter'. We don't care if N letters are the same or not, we just want to be sure their count is less than or equal to 'k'.

Why don’t we need to update this count (max frequent letter) when we shrink the window? Since we have to replace all the remaining letters to get the longest substring having the same letter in any window, we can’t get a better answer from any other window even though all occurrences of the letter with frequency maxRepeatLetterCount is not in the current window.

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

Time Complexity = O(N)\
Space Complexity = O(26) They be 26 possible characters saved in the dict.

Similar problem: Longest subarray with ones after replacement.

## 2. Permutation in a string

Given a string and a pattern, find out if the string contains any permutation of the pattern.

If a string has ‘n’ distinct characters, it will have 'n!' permutations.

> Input: String="oidbcaf", Pattern="abc"\
> Output: true\
> Explanation: The string contains "bca" which is a permutation\
>  of the given pattern.

**Problem Explanation:**
Let us first break down the problem. Our string 'pattern' tell us how many times and which character must appear as a permutation in the 'string'.

If you think about it, this can be represented with a dictionary that has a frequency count per char.

Then whenever we find a valid char traversing the string (exists in dictionary), we want to substract one from the frequency count of the char in the dictionary.
  If the value happens to become 0, it means we have found the freq count for this char in the evaluated window!
  Then we can say one letter has been matched.

If our 'matched' count is equal to size of dictionary then return true.
Since based on the prev analysis, we would have found all desired chars in this window size of evaluation.

Now if our iteration count (usually represented as a var 'i') is greater or equal than pattern length - 1:\
   move one char

Why not window size, instead of 'i'?\
We will always want to keep the desired window size the length of 'pattern'. When 'r' or 'i' pointer is greater or equal to pattern length -1, this becomes true until we finishes the loop.

Then we must do the following steps:\
  1. Get left char\
  2. Move left pointer to the next position\
  3. If left char exists in map, and count == 0: Substract 1 from matched.\
  4. If left char exists in map, add one to dictionary of frequency.

Below code would look something like this:

**C++**

```
bool findPermutation(string str, string pattern) {
    int matched = 0;
    unordered_map<char, int> map;
    int left = 0;

    for(int i = 0; i < pattern.length(); i++){
      map[pattern[i]]++; //Map frequency of chars in pattern
    }

    for(int right = 0; right < str.length(); right++){
      char c = str[right]; //get right char
      if(map.find(c) != map.end()){
          map[c]--; //if found decrease of freq map

          if(map[c] == 0){
            matched++;
            // if count 0 means we have found the
            //freq count for this char in the evaluated window
          }
      }

      if(matched == (int)map.size()){
        // means chars matched irrelevant of their freq
        return true;
      }

      if(right >= pattern.length() - 1){
        // this means evertime the window is bigger
        // than pattern size move one char
        char startChar = str[left];
        left++;
        if(map.find(startChar) != map.end()){
          if(map[startChar] == 0){
            // means char we are getting rid of
            // contributes to matched count
            // (irrelevant of count), only then
            // we should decrese matched count
            matched--;
          }
         map[startChar]++;
         //add towards missing char
        }
      }
    }
    return false;
  }
```

As always, feedback is welcome, let me know your thoughts on Twitter, thank you!

## 3. String Anagrams

Given a string and a pattern, find **all anagrams of the pattern in the given string.**
Every **anagram** is a **permutation** of a string. As we know, when we are not allowed to repeat characters while finding permutations of a string, we get N! permutations (or anagrams) of a string having N characters.

> Input: String="ppqp", Pattern="pq"
> Output: [1, 2]
> Explanation: The two anagrams of the pattern in the given string are "pq" and "qp".

## Problem Explanation

This problem, similar to the one above, asks us for all starting positions of a permutation.\
We know from above how to identify a permutation of a pattern in a string.\
Also if we continue traversing the string, instead of returning 'true' on the first permutation found.\
We should be able to obtain all anagrams. As starting position == left side of window size.

```
Traverse string
	add char to right of window
	if char exists in dictionary
		reduce frequency
		if val of dict[char] == 0
			matched++

	if matched == dictionary size
		push window start to array

	if i >= pattern.length()
		removed window start
		move window start one position
		if removed char is in dictionary
			if dict[char] == 0
				matched--
		add one to frequency in dict[char]

return array with start position
```

**C++**

```
vector<int> anagrams(string str, string pattern){
        unordered_map<char,  int> map;
        int left = 0;
        vector<int> arr;
        for(int i = 0; i < pattern.length(); i++){
                map[pattern[i]]++;
        }

        for(int i = 0; i < str.length(); i++){
                char letter = str[i];
                if(map.find(letter) != map.end()){
                        map[letter]--;
                        if(map[letter] == 0){
                                matched++;
                        }
                }

                if(matched == map.size()){
                        arr.push_back(left);
                }

                if(i >= pattern.length() - 1){
                        char leftLetter = str[left];
                        left++;
                        if(map.find(leftLetter) != map.end()){
                                if(map[leftLetter] == 0){
                                        matched--;
                                }
                                map[leftLetter]++;
                        }
                }
        }

        return arr;
}

```
