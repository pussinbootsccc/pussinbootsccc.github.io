---
layout: page
title: Heap Operations
permalink: /datastructure/heapoperations
---
Implement following functions of a min heap:  

{% highlight bash %}
update(int index, int element)   
offer(int element)  
poll()  
{% endhighlight %}



Key Ideas:
- `update(int index, int ele)`
    - base on value of original and ele, choose to `percolateUp(index)`, or `percolateDown(index)` 
- `offer(int ele)`
    - put ele in the last position, then do `percolateUp(size - 1)`
- `poll()`
    - put last value onto 0 position, then do `percolateDown(0)`

Note:
- `size` is actual size of elements in array, not `array.length` which is capacity


{% highlight java %}

    public DesignMinHeap(int[] array) { // with an input array
        if (array == null || array.length == 0) {
            throw new IllegalArgumentException("input array cannot be null or empty");
        }
        this.array = array;
        this.size = array.length;
        heapify();
    }

    public DesignMinHeap(int capacity) { // with a capacity
        if (capacity <= 0) {
            throw new IllegalArgumentException("capacity is not valid");
        }
        array = new int[capacity];
        size = 0; // also works as the pointer
    }

    public int update(int index, int element) {
        if (index < 0 || index > size - 1) {
            throw new ArrayIndexOutOfBoundsException("index is out the range");
        }
        int original = array[index]; // store old value
        array[index] = element; // update

        if (element < original) {
            percolateUp(index);
        } else if (element > original) {
            percolateDown(index);
        }
        return original;
    }

    public void offer(int element) {
        if (size == array.length) {
            // expand capacity
            array = Arrays.copyOf(array, (int)(array.length*1.5));
        }
        array[size] = element;
        size++;
        percolateUp(size - 1); // ele's index = old size = size - 1
    }

    public int poll() {
        if (size == 0) {
            throw new NoSuchElementException("heap is empty");
        }
        int result = array[0];
        array[0] = array[size - 1]; // use last element to cover up
        size--; // update size immediately
        percolateDown(0);
        return result;
    }

    private void heapify() {
        for (int i = size/2 - 1; i >= 0; i--) {
            percolateDown(i);
        }
    }

    private void percolateUp(int index) { 
        while (index > 0) { // if index = 0, only 1 element in heap, not enter while
            int parentIndex = (index - 1) / 2;
            if (array[index] < array[parentIndex]) {
                swap(array, parentIndex, index);
                index = parentIndex;
            } else {
                break;
            }
        }
    }

    private void percolateDown(int index) {
        while (index <= size / 2 - 1) {
            int leftIndex = index * 2 + 1;
            int rightIndex = index * 2 + 2;
            int swapIndex = leftIndex;
            if (rightIndex <= size - 1 && array[rightIndex] < array[leftIndex]) {
                swapIndex = rightIndex;
            }
            if (array[index] > array[swapIndex]) {
                swap(array, index, swapIndex);
                index = swapIndex;
            } else {
                break;
            }
        }
    }

    private void swap(int[] array, int i, int j) {
        int tmp = array[i];
        array[i] = array[j];
        array[j] = tmp;
    }
{% endhighlight %}