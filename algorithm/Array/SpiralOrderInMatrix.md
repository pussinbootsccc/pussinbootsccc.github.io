---
layout: page
title: Spiral Order In Matrix
permalink: /algorithm/array/spiralorderinmatrix
---
Key Idea
1. `offset + k` = absolute index, where `offset` is the base, and `k` is the expanding arm
2. `size` is the absolute side length of the focused matrix, so `size -=2` each round

Note
- `k` runs `size - 1` times, to pick size-1 elements in the sequence
- `offset + x` produces absolute index in original matrix
- special case: original size is odd, when `size = 1`, a single node will be left in the center

{% highlight java %}
public class SpiralOrderMatrixI {
    public List<Integer> spiral(int[][] matrix) {
        List<Integer> res = new ArrayList<>();
        if (matrix == null || matrix.length == 0) {
            return res;
        }
        int n = matrix.length;
        int offset = 0;
        int size = n;
        while (offset < n / 2) {
            for (int k = 0; k < size - 1; k++) { // 每串上只取包括自己的size-1个元素，指针k做size-1次循环
                res.add(matrix[offset][offset + k]);
            }

            for (int k = 0; k < size - 1; k++) {
                res.add(matrix[offset + k][offset + size - 1]);
            }

            for (int k = 0; k < size - 1; k++) {
                res.add(matrix[offset + size - 1][offset + size - 1 - k]);
            }

            for (int k = 0; k < size - 1; k++) {
                res.add(matrix[offset + size - 1 - k][offset]);
            }
            offset++;
            size -= 2;
        }

        if (size == 1) { // 边长奇数时，中心剩余一个点
            res.add(matrix[offset][offset]);
        }
        return res;
    }
}

{% endhighlight %}
