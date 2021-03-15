---
layout: page
title: Spiral Order In Matrix II
permalink: /algorithm/array/spiralorderinmatrixii
---
The input matrix is M * N, not necessarily a square   

Key Idea
- main 4 indexes as boundaries
    - `left`, `right`, horizontally scan, inclusive, range in `[left, right]`
    - `up`, `down`, vertically scan, exclusive, range in `[up + 1, down - 1]`

Note
- first handle purely no overlaps cases, `left < right && up < down`
- then handle overlaps & crossover cases

{% highlight java %}
public class SpiralOrderMatrixII {
    public List<Integer> spiral(int[][] matrix) {
        List<Integer> res = new ArrayList<>();
        if (matrix.length == 0 || matrix[0].length == 0) {
            return res;
        }
        int left = 0; // inclusive
        int right = matrix[0].length - 1; // inclusive
        int up = 0; // exclusive
        int down = matrix.length - 1; // exclusive
        
        while (left < right && up < down) {
            for (int i = left; i <= right; i++) {
                res.add(matrix[up][i]);
            }
            for (int i = up + 1; i <= down - 1; i++) {
                res.add(matrix[i][right]);
            }
            for (int i = right; i >= left; i--) {
                res.add(matrix[down][i]);
            }
            for (int i = down - 1; i >= up + 1; i--) {
                res.add(matrix[i][left]);
            }
            left++;
            right--;
            up++;
            down--;
        }

        if (left > right || up > right) { // index crossover occur, cannot form a matrix, finish
            return res;
        }
        if (left == right) { // single column, or a single dot
            for (int i = up; i <= down; i++) {
                res.add(matrix[i][left]);
            }
        } else if (up == down) { // single row, a single dot covered by if clause
            for (int i = left; i <= right; i++) {
                res.add(matrix[up][i]);
            }
        }
        return res;
    }
}

{% endhighlight %}
