---
layout: page
title: Sort Transformed Array
permalink: /algorithm/math/sorttransformedarray
---
Given a sorted integer array nums and three integers a, b and c, apply a quadratic function:    
`f(x) = ax2 + bx + c` to each element nums[i] in the array, and return the array in a sorted order.

{% highlight txt %}
Input: nums = [-4,-2,2,4], a = 1, b = 3, c = 5
Output: [3,9,15,33]

Input: nums = [-4,-2,2,4], a = -1, b = 3, c = 5
Output: [-23,-5,1,7]
{% endhighlight %}

Key Ideas
- based on `a` value, positive or negative
- extreme values appear at two ends, so maintain `two pointers`: `l`, `r`.
- `a >= 0`, 谁大移谁, 从后往前写入结果; `a < 0`, 谁小移谁, 从前往后写入结果

{% highlight java %}
class Solution {
    public int[] sortTransformedArray(int[] nums, int a, int b, int c) {
        int n = nums.length;
        int l = 0;
        int r = n - 1;
        
        int[] res = new int[nums.length];
        int i = a >= 0 ?  n - 1 : 0; // write pointer in result
        
        while (l <= r) {
            int left = func(nums[l], a, b, c); // extreme value appears at two ends
            int right = func(nums[r], a, b, c); // extreme value appears at two ends
            if (a >= 0) { // 谁大移谁，写入结果
                if (left >= right) {
                    res[i--] = left;
                    l++;
                } else {
                    res[i--] = right;
                    r--;
                }
                
            } else { // 谁小移谁，写入结果
                if (left <= right) {
                    res[i++] = left;
                    l++;
                } else {
                    res[i++] = right;
                    r--;
                }
            }
        }
        return res;
        
    }
    
    private int func(int x, int a, int b, int c) {
        return a * x * x + b * x + c;
    }
}
{% endhighlight %}

