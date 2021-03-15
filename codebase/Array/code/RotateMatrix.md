---
layout: page
title: Rotate Matrix
permalink: /algorithm/array/rotatematrix
---
Rotate a matrix close-wisely by 90 degrees

Key Ideas:
- `k` is absolute index, and it already carries offset info, as it starts as left
- `n - 1 - k` denotes absolute index of the last element in a side
- rotate a node, swapping 4 values, in a round; runs `count of elements = right - left + 1` rounds, to rotate all nodes in a side.

{% highlight java %}
// Method 1:
public class RotateMatrix {
    public void rotate(int[][] matrix) {
        int n = matrix.length;
        if (n <= 0) return;
        for (int offset = 0; offset <= n / 2; offset++) { // offset: 0, 1, 2..
            int left = offset; // means offset
            int right = n - 2 - offset;
            for (int k = left; k <= right; k++) { // k is absolute index
                int tmp = matrix[left][k]; // 1st
                matrix[left][k] = matrix[n - 1 - k][left]; // 1st <= 4th
                matrix[n - 1 - k][left] = matrix[n - 1 - left][n - 1 - k]; // 4th <= 3rd
                matrix[n - 1 - left][n - 1 - k] = matrix[k][n - 1 - left]; // 3rd <= 2nd
                matrix[k][n - 1 - left] = tmp; // 2nd <= 1st
            }
        }
    }
}
{% endhighlight %}


{% highlight java %}
// Method 2:
public class RotateMatrixII {
    public void rotate(int[][] matrix) {
        int n = matrix.length;
        if (n <= 0) return;
        int left = 0;
        int right = n - 2;
        while (left <= right) {
            for (int k = left; k <= right; k++) { // left, right, k is absolute index
                int tmp = matrix[left][k]; // 1st, left value fixed, k changes to larger
                matrix[left][k] = matrix[n - 1 - k][left]; // 1st <= 4th, 循环处理一竖列，行号(最下行起)变小，列号固定
                matrix[n - 1 - k][left] = matrix[n - 1 - left][n - 1 - k]; // 4th <= 3rd，循环处理一横行，行号固定，列号(最右列起)变小
                matrix[n - 1 - left][n - 1 - k] = matrix[k][n - 1 - left]; // 3rd <= 2nd，循环处理一竖列，行号变大，列号固定
                matrix[k][n - 1 - left] = tmp; // 2nd <= 1st, 循环用tmp值补满最右侧竖列
            }
            left++; // 往里压缩
            right--; // 往里压缩
        }
    }
}
{% endhighlight %}