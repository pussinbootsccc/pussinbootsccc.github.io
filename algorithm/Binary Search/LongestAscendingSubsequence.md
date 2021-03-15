---
layout: page
title: Longtest Ascending Subsequence
permalink: /algorithm/binarysearch/longestascendingsubsequence
---

Given an array A[0]...A[n-1] of integers, find out the length of the longest ascending subsequence.  
{% highlight bash %}

Examples  
Input: A = {5, 2, 6, 3, 4, 7, 5}   
Output: 4    
{2, 3, 4, 5} is the longest ascending subsequence. 

{% endhighlight %}

Key Ideas
- maintain a lowest ending array, `lowEndings[i]` = smallest ending value for all valid asc subsequence with (i+1) length.
    - if new num is large, just append it to the end of `lowEndings` array
        - expand the length in array
    - if new num need to be inserted, run binary search to find its right position to override old value.
        - keep the length, but update value in array
- length of lowEnings is the result.


Notes
- initialize `lowEnings[0] = array[0]`
- maintain`len` dynamically, not use `lowEndings.length` which is fixed size


{% highlight java %}
public class LongestAscendingSubsequence {
    public int longest(int[] array) {
        if (array == null || array.length == 0) return 0;
        int[] lowEndings = new int[array.length];
        lowEndings[0] = array[0]; // lowEndings[i]: the lowest ending value for all asc subsequences with (i+1) len
        int len = 1;
        for (int i = 1; i < array.length; i++) {
            int num = array[i];
            if (num > lowEndings[len - 1]) {
                lowEndings[len] = num; // append num
                len++;
            } else {
                int pos = find(lowEndings, 0, len - 1, num);
                lowEndings[pos] = num; // update old value with num
            }
        }
        return len;
    }

    private int find(int[] array, int left, int right, int target) {
        while (left + 1 < right) {
            int mid = (left + right) >> 1;
            if (array[mid] < target) {
                left = mid;
            } else {
                right = mid;
            }
        }

        if (target <= array[left]) { // 0 to insert to [1, 3], return pos = 0
            return left;
        } else {                     // 4 to insert to [3, 5], return pos = 1
            return right;
        }                            
    }
}
{% endhighlight %}
---
DP Solution
{% highlight java %}
  public int longest(int[] array) {
    if (array == null || array.length == 0) return 0;
    int[] dp = new int[array.length]; // dp[i], longest asc sequence's length ending at i index
    Arrays.fill(dp, 1); // default len = 1
    int longest = 1;
    for (int i = 1; i < array.length; i++) {
        for (int j = 0; j < i; j++) {
            if (array[i] > array[j]) {
                dp[i] = Math.max(dp[i], dp[j] + 1); 
            }
        }
        longest = Math.max(longest, dp[i]); // try every ending index
    }
    return longest;
  }
{% endhighlight %}


