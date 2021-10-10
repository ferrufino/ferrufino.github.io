---
layout: post
title: Sliding Window Pattern III
tags: slidingWindow, codingprep
math: false
date: 2021-10-09 21:33 +0800
---

This is where the fun starts. If you are looking for easier problems checkout 'Slidding Window I' and 'Slidding Window II'.

The following problems may look hard, but if you are able to identify the key conditions that have to be met it becomes easy. Let me explain.

## Permutation in a string

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

## String Anagrams

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
