---
layout: page
title: Max in Sliding Window
permalink: /algorithm/slidingwindow/maxinslidingwindow
---
You are given an array of integers nums, there is a sliding window of size k which is moving from the very left of the array to the very right.You can only see the k numbers in the window. Each time the sliding window moves right by one position.

{% highlight txt %}
Input: nums = [1,3,-1,-3,5,3,6,7], k = 3
Output: [3,3,5,5,6,7]
Explanation: 
Window position                Max
---------------               -----
[1  3  -1] -3  5  3  6  7       3
 1 [3  -1  -3] 5  3  6  7       3
 1  3 [-1  -3  5] 3  6  7       5
 1  3  -1 [-3  5  3] 6  7       5
 1  3  -1  -3 [5  3  6] 7       6
 1  3  -1  -3  5 [3  6  7]      7
{% endhighlight %}

Key Ideas
- `deque` holds valid large value's indexes, `deque[0]` is max's index
-  curr `nums[i]` can rule out small value's indexes, and old values's indexes from deque
- `i `index at last enters deque in each round

Notes
- need to rule out the same value indexes, because i index is more up-to-date.  

{% highlight java %}
public class MaximumInSlidingWindow {
    public List<Integer> maxWindows(int[] array, int k) {
        List<Integer> res = new ArrayList<>();
        Deque<Integer> deque = new ArrayDeque<>(); // deque[0] holds valid max's index

        for (int i = 0; i < array.length; i++) {
            while (!deque.isEmpty() && array[i] >= array[deque.peekLast()]) {
                deque.pollLast(); // rule out too small or equal values' indexes compare to i's value
            }
            while (!deque.isEmpty() && i - deque.peekFirst() >= k) {
                deque.pollFirst(); // rule out too old values indexes compare to i's position
            }
            deque.offerLast(i); // always add curr index
            if (i + 1 >= k) {
                res.add(array[deque.peekFirst()]); // only update res when already met k elements
            }
        }
        return res;
    }
}
{% endhighlight %}

