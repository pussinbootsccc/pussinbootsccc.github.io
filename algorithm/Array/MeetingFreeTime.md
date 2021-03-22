---
layout: page
title: Meeting Free Time
permalink: /algorithm/array/meetingfreetime
---
Given the bsuy schedules, to find out all free slots that satisfies required conditions.
  
{% highlight txt %}
Busy Time:
{-2, 0}, {3, 20}, {-10, -8}, {0, 2}, {16, 17}, {19, 23}, {30, 40}, {27, 33}.

startTime: -5
endTime: 30
duration: 2

Output: {-5, -2}, {23, 27}
{% endhighlight %}

Key Ideas
- maintian a on-the-fly variable `busyEnd` to track the largest end value for busy slots sofar.  
- a free slot appears when `busyEnd <= meeting.start`.  
- need to check if within the valid time zone.  


{% highlight java %}
public class MeetingFreeTime {
    static class Node {
        int start;
        int end;
        Node(int start, int end) {
            this.start = start;
            this.end = end;
        }
    }

    public List<Node> findFreeTime(List<Node> meetings, int startTime, int endTime, int duration) {
        Collections.sort(meetings, (x, y) -> x.start < y.start ? -1 : 1);

        List<Node> res = new ArrayList<>();
        int busyEnd = startTime; // maintain a global max busyEnd (largest end value among the busy nodes)
        for (Node node : meetings) {
            if (node.end <= startTime) { // check if enter valid time zone
                continue;
            }

            if (node.start - busyEnd >= duration) { // empty slot found
                Node free = new Node(busyEnd, node.start);
                res.add(free);
            }
            busyEnd = Math.max(busyEnd, node.end); // update max busyEnd for each

            if (node.end >= endTime) { // check if exit valid time zone
                break;
            }
        }
        return res;

    }
}
{% endhighlight %}

**Memroy Constrains**
- external sort: load chunk by chunk, sort a chunk in memory, write to 2 disk files, the merge 2 sorted files, write to a final result.
- bucket sort: if start, end values variance is limited.