---
layout: page
title: Longest Line of One in Matrix
permalink: /algorithm/dpmulti/longestlineofoneinmatrix
---
Given a 01 matrix M, find the longest line of consecutive one in the matrix.  The line could be horizontal, vertical, diagonal or anti-diagonal.  

{% highlight txt %}
Input:
[[0,1,1,0],
 [0,1,1,0],
 [0,0,0,1]]

Output: 3
Explanation: three 1s in diagonal
{% endhighlight %}

Key Ideas:
- 3 dp arrays needed to maintain count of continous 1s given curr i, j position
- update global max count for each position

Note:
- `i = m - 1, j = 0`, 最左下角位置, 最小负数为`1 - m`, 所以令`offset = m - 1`
- `diagonal`, (m + n - 1)个, represent by `j - i`, `d[j - i + offset]`保证非负数index
- `anti-diagonal`,(m + n - 1)个, represent by `j + i`, `ad[j + i]`都是非负数index

{% highlight java %}
class Solution {
    public int longestLine(int[][] M) {
        if (M == null || M.length == 0 || M[0].length == 0) return 0;
        int m = M.length;
        int n = M[0].length;
        int offset = m - 1;
        
        int[] col = new int[n]; // n个列
        int[] d = new int[m + n - 1]; // m+n-1个对角线
        int[] ad = new int[m + n - 1]; // m+n-1个反对角线
        
        int max = 0;
        for (int i = 0; i < m; i++) {
            int curr = 0; // how many consecutive 1 in curr row
            for (int j = 0; j < n; j++) {
                if (M[i][j] == 1) {
                    curr++;
                    col[j]++;
                    d[j - i + offset]++;
                    ad[j + i]++;
                    max = Math.max(max, curr);
                    max = Math.max(max, col[j]);
                    max = Math.max(max, d[j - i + offset]);
                    max = Math.max(max, ad[j + i]); 
                } else {
                    curr = 0;
                    col[j] = 0;
                    d[j - i + offset] = 0;
                    ad[j + i] = 0;
                }  
            }
        }
        return max;
    }
}
{% endhighlight %}


