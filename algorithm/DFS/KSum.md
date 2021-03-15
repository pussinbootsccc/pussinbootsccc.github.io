---
layout: page
title: KSum
permalink: /algorithm/dfs/ksum
---
Given four K lists A, B, C, D...K of integer values, compute how many tuples (a, b, c, d, ..., k)  
there are such that A[a] + B[b] + C[c] + D[d] + .. + K[k] is zero 
  
{% highlight bash %}
Input:
K rows
n elements in a row  
int[][] maxtrix = 
[[n elements], 
 [n elements], 
 [n elements], 
 ... 
 [n elements]]

{% endhighlight %}

Key Ideas
- K lists, half size `N = K / 2`
- use first half N lists to build PairSumMap
    - start level index = 0 (inclusive)
    - end level index = N (exclusive)
    - say K = 5, first half level index: [0, 1]
- use second half K - N lists, based on map info, to summarize count
    - start level index = N (inclusive)
    - end level index = K (exclusive)
    - say K = 5, second half level index: [2, 3, 4]


{% highlight java %}
public class KSum {
    public int KSumCount(int[] A, int[] B, int[] C, int[] D) {
        int[][] matrix = new int[][] {A, B ,C ,D};
        Map<Integer, Integer> map = getPairSumMap(matrix, 2);
        return dfsCount(matrix, 2, 0, 4, map);
    }

    // use first half lists to build PairSumMap
    public Map<Integer, Integer> getPairSumMap(int[][] matrix, int N) {
        Map<Integer, Integer> map = new HashMap<>();
        int n = matrix[0].length;
        helper(matrix, 0, 0, N, map);
        return map;
    }

    private void helper(int[][] matrix, int level, int acc, int N, Map<Integer, Integer>map) {
        if (level == N) {
            int freq = map.getOrDefault(acc, 0);
            map.put(acc, freq + 1);
            return;
        }

        for (int num : matrix[level]) { // use one number in this level (curr row)
            helper(matrix, level + 1, acc + num, N, map); // pass acc + sum to avoid backtrack acc explicitly
        }
    }

    // use second half lists, based on map info, to compute count that satisfies the target requirement
    // dfsCount with return type
    private int dfsCount(int[][] matrix, int level, int acc, int N, Map<Integer, Integer>map) {
        if (level == N) {
            if (map.containsKey(-acc)) {
                return map.get(-acc);
            }
            return 0;
        }

        int count = 0;
        for (int num : matrix[level]) {
            count += dfsCount(matrix, level + 1, acc + num, N, map); // pass acc + sum to avoid backtrack acc explicitly
        }
        return count;
    }
}
{% endhighlight %}
