---
layout: page
title: Max Timestamps in Window - Bucket
permalink: /algorithm/slidingwindow/maxtimestampsinwindowbucket
---
Bucket sort, sliding window

{% highlight txt %}
timestamp = [101，102，130，139，200]  
time window = 5  

那time window这个区间可能是[101, 105],[102, 106]...[196,200]
最好的区间是[101, 105]
包含了101，102两个timestamps，返回最好区间的起点值: 101
{% endhighlight %}

Key Ideas

{% highlight java %}
public class MaxTimestampsInWindowII {
    public int getBestWindow(int[] timestamps, int k) {
        int[] pair = getMinMax(timestamps);
        int min = pair[0]; // a offset to convert to original timestamp
        int max = pair[1];
        int offset = min;

        int[] bucket = new int[max - min + 1];

        for (int t : timestamps) {
            bucket[t - offset] += 1;
        }

        int count = 0;
        int maxCount = 0;
        int bestStart = -1;
        for (int i = 0; i < bucket.length; i++) {
            count += bucket[i];
            if (i >= k) {
                count -= bucket[i - k];
            }
            if (count > maxCount && i >= k - 1) { // update condition when size full && curr count is large
                maxCount = count;
                bestStart = i - k + 1; // i is endIndex of k-size window, startIndex = i - (k - 1)
                System.out.println(bestStart);
            }
        }
        return bestStart + offset;
    }

    private int[] getMinMax(int[] timestamps) {
        int min = Integer.MAX_VALUE;
        int max = Integer.MIN_VALUE;
        for (int t : timestamps) {
            min = Math.min(min, t);
            max = Math.max(max, t);
        }
        return new int[] {min, max};
    }

    public static void main(String[] args) {
        MaxTimestampsInWindowII s = new MaxTimestampsInWindowII();
        int[] input3 = {108, 115, 200, 201, 202};
        int k3 = 3;
        System.out.println(s.getBestWindow(input3, k3)); // 198. [198, 202] sections contains 3 timestamps
    }
}
{% endhighlight %}
