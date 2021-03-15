---
layout: page
title: Heap Heapify
permalink: /datastructure/heapheapify
---
Heapify an unsorted array to min heap.



{% highlight bash %}
Input: 
{10, 11, 7, 2, 8, 4, 6, 13, 3}   =>   {2, 3, 4, 10, 8, 7, 6, 13, 11}  

           2  
       3        4  
    10   8    7   6  
 13   11  

{% endhighlight %} 


Key Ideas:
- heapify() requires left & right subtrees are already heapified, based on that
    - we only need to look at 3 values: (parent, left child, right child), 
    - do percolateDown upon parent node to make current tree become heapified as a whole. 
    - scan the input array from right to left guarantees this property (build bottom foundation, and percolate down from top)
- parent node with `index`
    - its leftChild: `leftIndex = 2 * index + 1` (must have)
    - its rightChild: `rightIndex = 2 * index + 2` (may not have)
    - pick `min(leftChild, rightChild)`, then compete for `min(parent, child)`

{% highlight java %}

public class Heapify {
  public int[] heapify(int[] array) {
    if (array == null || array.length == 0) {
      throw new IllegalArgumentException("input array cannot be null or empty");
    }
    for (int i = array.length - 1; i >= 0 ; i--) { // scan from bottom to top
      percolateDown(i, array);
    }
    return array;
  }

  private void percolateDown(int index, int[] array) {
    while (true) {
      int leftIndex = 2 * index + 1;
      if (leftIndex > array.length - 1) { // such index node has no child
        break;
      } 
      int rightIndex = 2 * index + 2;
      int swapIndex = leftIndex; // leftIndex is always valid
      if (rightIndex < array.length && array[rightIndex] < array[leftIndex]) {
        swapIndex = rightIndex; // if rightIndex valid and truely small
      }

      if (array[index] > array[swapIndex]) { // parent node is large, percolate down
        swap(array, index, swapIndex);
        index = swapIndex; // continue to go down
      } else {
        break; // parent node is small enough to stay as where it is
      }
    }
  }

  private void swap(int[] array, int i, int j) {
    int tmp = array[i];
    array[i] = array[j];
    array[j] = tmp;
  }
}
{% endhighlight %}