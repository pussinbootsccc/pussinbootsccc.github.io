---
layout: page
title: Max Timestamps in Window - Deque
permalink: /algorithm/slidingwindow/maxtimestampsinwindowdeque
---
Sliding window using a deque data structure

{% highlight txt %}
timestamp = [101，102，130，139，200]  
time window = 5  

那time window这个区间可能是[101, 105],[102, 106]...[196,200]
最好的区间是[101, 105]
包含了101，102两个timestamps，返回最好区间的起点值: 101
{% endhighlight %}

Key Ideas
- `deque`, served as a double linked list, offer from last, poll from first
    - use `deque`, as we need to `peekFirst` and `peekLast` to compare
- rule out old timestamps from deque, `deque.size()` keeps the count of valid timestamps

{% highlight java %}
public class MaxTimestampsInWindowII {
    public int getBestWindow(int[] timestamps, int timeFrame) {
         Arrays.sort(timestamps);

        int maxCount = 0;
        int bestStart = -1;
        // use deque, as we need to peek first and peek last
        Deque<Integer> deque = new ArrayDeque<>(); // the valid timestamps within required timeFrame
        for (int t : timestamps) {
            deque.offerLast(t);
            while (!deque.isEmpty() && deque.peekLast() - deque.peekFirst() >= timeFrame) {
                deque.pollFirst(); // keep popping out head oldest element
            }
            if (deque.size() > maxCount) {
                maxCount = deque.size();
                bestStart = deque.peekFirst();
            }
        }
        return bestStart;
    }

    public static void main(String[] args) {
        MaxTimestampsInWindowII s = new MaxTimestampsInWindowII();
        int[] input3 = {108, 115, 200, 201, 202};
        int k3 = 3;
        System.out.println(s.getBestWindow(input3, k3)); // 200
    }
}
{% endhighlight %}
