---
layout: post
title: Sliding Window Pattern I
tags: slidingWindow, codingprep
math: false
date: 2021-09-22 13:10 +0800
---

The following pattern evaluates slidding windows within an array/string/linked-list.\
We keep a sub-window of evaluation and when a condition is met, it 'slides' to the right.

Below I describe 5 problems of this pattern being applied in C++ or Swift.

## 1. Find averages of all subarrays of size K

**Problem Description:**\
Given an array, find the average of all subarrays of ‘K’ contiguous elements in it.

Array: [1, 3, 2, 6, -1, 4, 1, 8, 2], K=5
Output: [2.2, 2.8, 2.4, 3.6, 2.8]

**Problem Explanation:**\
Window size here is kept in size 'K'.\
DS to keep track of averages, vector of doubles \
double for windowSum\
int for windowStart

traverse with windowEnd
   add curr int to windowSum\
   if windowEnd more than or equal to 'K'\
      get windowSize - windowSize can be replaced with 'K'\
      get average = windowSum / windowSize\
      push to vector of doubles\
      move windowStart one position to the right

**C++**

{% highlight js linenos %}
static vector<double> findAverages(int K, const vector<int>& arr) {
vector<double> ans;
int windowStart = 0;
double windowSum = 0;

    for(int i = 0; i < arr.size(); i++){
        // i is our  windowEnd
      windowSum += arr[i];

      if(i >= K - 1){ //due to positions starting from 0
        //double windowSize = i - windowStart + 1;
        //no need as we have K
        double average = windowSum / K;
        ans.push_back(average);
        windowSum -= arr[windowStart];
        windowStart++;
      }
    }

    return ans;

}
{% endhighlight %}
Time Complexity = O(N)

## 2. Maximum sum subarray of Size K

**Problem Description:**\
Given an array of positive numbers and a positive number ‘k,’ find the maximum sum of any contiguous subarray of size ‘k’.

Input: [2, 1, 5, 1, 3, 2], k=3\
Output: 9\
Explanation: Subarray with maximum sum is [5, 1, 3].

**Problem Explanation:**\
Window here is kept in size 'k'. It adds a new entry on the right of 'arr' and subtracts left entry to always keep the sum size 'k'.

**C++**

{% highlight js linenos %}
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
{% endhighlight %}
Time Complexity = O(N)\
Space Complexity = O(1)

## 3. Smallest Subarray with an equal or greater sum than 'S'

**Problem Description:**\
Given an array of positive numbers and a positive number ‘S’\
find the length of the smallest contiguous subarray\
whose sum is greater than or equal to ‘S’. Return 0 if no such subarray exists.

Input: [2, 1, 5, 2, 3, 2], S=7\
Output: 2\
Explanation: The smallest subarray with a sum greater than or equal to '7' is [5, 2].\

**Problem Explanation:**\
Here we don't have a fixed sized window and our goal is instead to create the smallest possible window.\
Now to keep track of when to evaluate the current window, we first need to reach a sum equal or greater to 'S'\
Then compare the current window size with our smallest window at that moment and replace if necessary.\
Also we can continue this evaluation, shifting our window to the right,\
while the sum is equal or greater to 'S'

**C++**

{% highlight js linenos %}
static int findSmallesSubArray(vector<int> arr, string S) {
 int currentWindowSum = 0;
 int minWindowSize = INT_MAX;
 int windowSize = 0;
 int left = 0;
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
}
{% endhighlight %}
Time Complexity = O(N)\
Space Complexity = O(1)

## 4. Longest Substring with no more than K distinct chars

**Problem Description:**\
Find the length of the longest substring in it with no more than 'k' distinct characters

Input: String="cbbebi", k=3\
Output: 5\
Explanation: The longest substrings with no more than '3' distinct characters are "cbbeb" & "bbebi".

**Problem Explanation:**\
In this case, we keep track of unique chars in a dictionary and frequency of chars.\
If size of dict > k, then we have exceeded our k limit and need to remove from the window start chars in the dictionary:\
  while dict.size() > k from windowStart remove char from dictionary.\
Then we get max count(length of window: 'windowEnd - windowStart + 1') of chars that are equal or below k size.

**C++**

{% highlight js linenos %}
static int findLength(const string &str, int k) {
   int windowStart = 0, maxLength = 0;
   unordered_map<char, int> charFrequencyMap;
   //i equals windowEnd
   for(int i = 0; i < str.length(); i++) {
      char rightChar = str[i];
      charFrequencyMap[rightChar]++;
      while ((int)charFrequencyMap.size() > k) {
            char leftChar = str[windowStart];
            charFrequencyMap[leftChar]--;
            if (charFrequencyMap[leftChar] == 0) {
             charFrequencyMap.erase(leftChar);
            }
            windowStart++; // shrink the window
      }
      // remember the maximum length so far
      maxLength = max(maxLength, i - windowStart + 1);
   }
   return maxLength;
}
{% endhighlight %}

Time Complexity = O(N)\
Space Complexity = O(K)

## 5. Longest Substring with Distinct Characters

**Problem Description:**\
Find the length of the longest substring without any repeating char.

Input: String="aabccbb"\
Output: 3\
Explanation: The longest substring with distinct characters is "abc".

**Problem Explanation:**\
If the map already contains the 'rightChar', shrink the window from the beginning so that
we have only one occurrence of 'rightChar'.

The question here though is whether to assign to WindowStart 'rightChar' or WindowStart itself.
This is tricky; in the current window, we will not have any 'rightChar' after its
      previous index and if 'windowStart' is already ahead of the last index of 'rightChar',
      we'll keep 'windowStart'\
Then just pick the max value between the two, when 'rightChar' already exists.

**Swift**

{% highlight js linenos %}
func lengthOfLongestSubstring(\_ s: String) -> Int {
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

{% endhighlight %}

**C++**

{% highlight js linenos %}
static int findLength(const string& str){
   int maxLength = 0;
   int currentLength = 0;
   unordered_map<char, int> map;
   int left = 0;
   // my right side is i
   for(int i = 0; i < str.length(); i++){
      char c = str[i];
      if(map.find(c) != map.end()){
         left = max(left, map[c]+ 1);
      }
      map[c] = i;
      currentLength = i - left + 1;
      maxLength = max(maxLength, currentLength);
   }

return maxLength;
}
{% endhighlight %}

Time Complexity = O(N)\
Space Complexity = O(K) K = the number of distinct chars in the string.
