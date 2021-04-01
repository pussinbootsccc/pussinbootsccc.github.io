---
layout: page
title: Subarrays With Bounded Maximum
permalink: /algorithm/dp1d/subarrayswithboundedmaximum
---
We are given an array A of positive integers, and two positive integers L and R (L <= R).

Return the number of contiguous subarrays such that the value of the maximum array element in that subarray is at least L and at most R.

{% highlight txt %}
Input: 
A = [2, 1, 4, 3]
L = 2
R = 3

Output: 3

Explanation: 
There are three subarrays that meet the requirements: [2], [2, 1], [3].
{% endhighlight %}

{% highlight java %}
public int numSubarrayBoundedMax(int[] A, int L, int R) {
    int n = A.length;
    int[] dp = new int[n];
    // dp[i] means the count of satisfying subarrays ending at index i
    int largeNumberIndex = -1;
    for (int i = 0; i < n; i++) {
        if (A[i] < L) {
            if (i > 0) {
                dp[i] = dp[i - 1]; // // count of must ending at i, the same as count of must ending at i-1
            } // else dp[0] = 0, no need to write it out
        }
        else if (A[i] > R) {
            largeNumberIndex = i;
            dp[i] = 0; // cannot find a valid subarray ending at i index
        }
        else {
            dp[i] = i - largeNumberIndex; // everything after largeIndex to curr i can form a new valid subarray
        }

    }
    // sum up dp for using every ending position
    int count = 0;
    for (int i = 0; i < n; i++) {
        count += dp[i];
    }
    return count;
}
{% endhighlight %}

**Space Efficient Version**
{% highlight java %}
public int numSubarrayBoundedMax(int[] A, int L, int R) {
    int n = A.length;
    int count = 0; // total count
    int curr = 0; // count for dp[i], this round

    int largeNumberIndex = -1;
    for (int i = 0; i < n; i++) {
        if (A[i] < L) {
            // pass, curr is inherited from previous round
        } 
        else if (A[i] > R) {
            lastLargerIndex = i;
            curr = 0;
        }
        else {
            curr = i - largeNumberIndex;
        }
        count += curr; 
    }
    
    return count;
}
{% endhighlight %}

