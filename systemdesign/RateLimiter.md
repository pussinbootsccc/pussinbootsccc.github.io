---
layout: page
title: Rate Limiter
permalink: /systemdesign/ratelimiter
---
Implement a rate limiter, provide one method: `is_ratelimited(timestamp, event, rate, increment)`.  

`timestamp`: the current timestamp, which is an integer and in second unit.  
`event`: the string to distinct different event. for example, "login" or "signup".  
`rate`: the rate of the limit.    
1/s (1 time per second),    
2/m (2 times per minute),   
10/h (10 times per hour),   
100/d (100 times per day).
The format is `[integer]/[s/m/h/d]`.  
`increment`: whether we should increase the counter. (or take this call as a hit of the given event)

{% highlight bash %}
Input: 
is_ratelimited(1, "login", "3/m", true)   
is_ratelimited(11, "login", "3/m", true)   
is_ratelimited(21, "login", "3/m", true)  
is_ratelimited(30, "login", "3/m", true)   
is_ratelimited(65, "login", "3/m", true)  
is_ratelimited(300, "login", "3/m", true)  

Output:  
false   
false   
false  
true   
false  
false  
{% endhighlight %} 


Key Ideas:
- maintain a map with info of `event:[list of its happened timestamps]`
- given a curr timestamp, compute the startTimeStamp of the active window
  - use binary search to find how many events happened within active window, if `timestamp >= startTime`
  - determine if curr event need to be limited, if `count >= limit`


{% highlight java %}
public class RateLimiter {
    private Map<String, List<Integer>> map;
    private Map<String, Integer> window;

    public RateLimiter() {
        this.map = new HashMap<>();
        this.window = new HashMap<>();
        this.window.put("s", 1);
        this.window.put("m", 60);
        this.window.put("h", 60 * 60);
        this.window.put("d", 24 * 60 * 60);
    }

    public boolean isRateLimited(int timestamp, String event, String rate, boolean increment) {
        if (rate == null || rate.length() == 0 || timestamp <= 0) return false;
        String[] array = rate.split("/");
        int limit = Integer.parseInt(array[0]); // max freq allowed
        int window = this.window.get(array[1]); // active window size

        map.putIfAbsent(event, new ArrayList<>()); // event : listOfTimestamps
        int startTime = timestamp - window + 1; // start timestamp of the active window given curr time
        boolean shouldLimit = count(map.get(event), startTime) >= limit;
        if (increment && !shouldLimit) {
            map.get(event).add(timestamp); // when satisfied conditions to append new timestamp for this event
        }
        return shouldLimit;
    }

    private int count(List<Integer> times, int startTime) { // how many timestamps exists equal&after startTime
        if (times.size() == 0 || startTime > times.get(times.size() - 1)) return 0;
        int n = times.size();
        int left = 0;
        int right = n - 1;
        while (left + 1 < right) {
            int mid = left + (right - left) / 2;
            if (times.get(mid) < startTime) {
                left = mid;
            } else {
                right = mid;
            }
        }
        if (startTime <= times.get(left)) { // [1, 2, 5, 6, 7] startTime = 2, count = 5 - 1 (2's index) = 4
            return n - left;
        } else {                            // [1, 2, 5, 6, 7] startTime = 3, count = 5 - 2 (5's index) = 3
            return n - right;
        }
    }
}
{% endhighlight %}