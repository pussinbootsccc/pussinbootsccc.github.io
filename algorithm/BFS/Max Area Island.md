---
layout: page
title: Max Area of Island
permalink: /algorithm/bfs/maxareaofarea
---
Find the island with the largest area  
the connected nodes with the same value can form a island

{% highlight bash %}
matrix = [[2, 1, 1, 1, 3],
          [2, 2, 1, 1, 1],
          [1, 1, 2, 1, 1]];

output = 8 (count of connected 1s)
{% endhighlight %}

Key Ideas
- try with each node to do bfs/dfs
- maintain a global max area to be updated

**BFS**  
{% highlight java %}
public class MaxAreaOfIsland {
    static final int[][] directions = { {1, 0}, {-1, 0}, {0, 1}, {0, -1} };
    public int maxArea(int[][] matrix) {
        int m = matrix.length;
        int n = matrix[0].length;
        int max = 0;
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (matrix[i][j] != Integer.MAX_VALUE) {
                    max = Math.max(max, bfsHelper(matrix, i, j));
                }
            }
        }
        return max;
    }

    private int bfsHelper(int[][] matrix, int x, int y) {
        Queue<Node> queue = new ArrayDeque<>(); // maintain the same value connected nodes
        queue.offer(new Node(x, y, matrix[x][y]));
        matrix[x][y] = Integer.MAX_VALUE; // denotes visited

        int count = 0;
        while (!queue.isEmpty()) {
            Node curr = queue.poll();
            count++; // a connected component found
            for (Node neighbor : findValidNeighbors(matrix, curr)) {
                queue.offer(neighbor);
                matrix[neighbor.x][neighbor.y] = Integer.MAX_VALUE; // mark as visited
            }
        }
        return count;
    }

    private List<Node> findValidNeighbors(int[][] matrix, Node curr) {
        int m = matrix.length;
        int n = matrix[0].length;
        List<Node> res = new ArrayList<>();
        for (int[] dir : directions) {
            int newX = curr.x + dir[0];
            int newY = curr.y + dir[1];
            if (newX >= 0 && newX < m && newY >= 0 && newY < n) { // within boundary
                int value = matrix[newX][newY];
                if (value != Integer.MAX_VALUE && value == curr.val) { // neighbor not visited && the same value as curr node
                    res.add(new Node(newX, newY, value));
                }
            }
        }
        return res;
    }

    static class Node {
        int x;
        int y;
        int val;
        Node(int x, int y, int val) {
            this.x = x;
            this.y = y;
            this.val = val;
        }
    }
}
{% endhighlight %}
TC: O(M * N)  
SC: O(M * N) worse case, to draw a spiral stroke that passes through all nodes in matrix, that takes up M * N size.      
    O(M + N) might be average case, the active size of queue is the sum(side length) of the boundaries. 

**DFS**   
{% highlight java %}
public class MaxAreaOfIsland {
    static final int[][] directions = { {1, 0}, {-1, 0}, {0, 1}, {0, -1} };
    public int maxArea(int[][] matrix) {
        int res = 0;
        int m = matrix.length;
        int n = matrix[0].length;
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (matrix[i][j] != Integer.MAX_VALUE) {
                    res = Math.max(res, helper(matrix, i, j));
                }
            }
        }
        return res;
    }

    private int helper(int[][] matrix, int x, int y) {
        int val = matrix[x][y];
        matrix[x][y] = Integer.MAX_VALUE; // mark as visited
        int area = 1;
        for (int[] dir : directions) {
            int newX = x + dir[0];
            int newY = y + dir[1];
            if (newX >= 0 && newX < matrix.length && newY >= 0 && newY < matrix[0].length && matrix[newX][newY] != -1) {
                if (matrix[newX][newY] == val) { // must the same value connected
                    area += helper(matrix, newX, newY);
                }
            }
        }
        return area;
    }
}
{% endhighlight %}
drawbacks: the chance of causing `StackOverflowError` due to level of recursion exceeding the stack size.  
TC: O(M * N)  
SC: O(M * N), the worst case for the level of the stack: think about a spiral alike search, the trace goes through all nodes.   
    this may exceed the max depth of the stack, (but such queue's size can fit into memory).
