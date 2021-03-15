---
layout: page
title: Web Log Limiter
permalink: /algorithm/systemdesign/webloglimiter
---
Implement a web logger, which provide two methods:  
`hit(timestamp)`, record a hit at given timestamp.   
`get_hit_count_in_last_5_minutes(timestamp)`, get hit count in last 5 minutes.   

{% highlight bash %}
Input:  
  hit(1);  
  hit(2);  
  get_hit_count_in_last_5_minutes(3);  
  hit(300);  
  get_hit_count_in_last_5_minutes(300);  
  get_hit_count_in_last_5_minutes(301);  
Output:  
  2  
  3  
  2 
{% endhighlight %} 


Key Ideas:
- queue only maintains valid nodes
- when query get_hit(), then do lazy deletion on queue


{% highlight java %}
public class WebLogger {
    private Queue<Integer> queue;
    private static final int window = 300;

    public WebLogger() {
        this.queue = new ArrayDeque<>();
    }

    public void hit(int timestamp) {
        queue.add(timestamp);
    }

    public int get_hit_count_in_last_5_minutes(int timestamp) {
        while (!queue.isEmpty() && queue.peek() <= timestamp - window) { // 1 = 301 - 300, the 1 is still invalid node
            queue.poll();
        }
        return queue.size();
    }
}
{% endhighlight %}