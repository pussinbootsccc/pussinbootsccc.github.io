---
layout: page
title: 4Sum II
permalink: /algorithm/array/4sumii
---
Given four lists A, B, C, D of integer values, compute how many tuples (i, j, k, l)  
there are such that A[i] + B[j] + C[k] + D[l] is zero 
  
{% highlight bash %}
Input:
A = [ 1, 2]
B = [-2,-1]
C = [-1, 2]
D = [ 0, 2]

Output:
2

Explanation:
The two tuples are:
(0, 0, 0, 1) -> A[0] + B[0] + C[0] + D[1] = 1 + (-2) + (-1) + 2 = 0
(1, 1, 0, 0) -> A[1] + B[1] + C[0] + D[0] = 2 + (-1) + (-1) + 0 = 0
{% endhighlight %}

Key Ideas
- create PairSum frequency map using pairs in A, B
- by map info, scan pairs in C, D to compute final count


{% highlight java %}

public int fourSumCount(int[] A, int[] B, int[] C, int[] D) {
    Map<Integer, Integer> map = new HashMap<>();
    // build pairSum to freq map
    for (int a : A) {
        for (int b : B) {
            int count = map.getOrDefault(a + b, 0);
            map.put(a + b, count + 1);
        }
    }
    // summarize count
    int count = 0;
    for (int c : C) {
        for (int d : D) {
            int target = -(c + d);
            if (map.containsKey(target)) {
                count += map.get(target);
            }
        }
    }
    return count;  
}

{% endhighlight %}
