---
layout: page
title: K Closest Points Heap
permalink: /algorithm/sorting/kclosestpointsheap
---
To compute the k nearest points to the origin, use score rating to break the tie.

{% highlight txt %} 
Input:
{3, 3, 4.9}, {5,-1, 5.0}, {-2 , 4, 4.88}, {4, 2, 4.7}, {-4, -2, 4.95}
k=3

Output:
Point{x=3.0, y=3.0, s=4.9}, Point{x=-4.0, y=-2.0, s=4.95}, Point{x=-2.0, y=4.0, s=4.88}
{% endhighlight %}

Key Ideas
- use `max heap`, to make min k distances remains in heap  
- write comparator for maxheap sorting rule, max distance first, low scores first

Notes
- `online algorithm`, it can process input piece-by-piece in a serial fashion, i.e., in the order that the input is fed to the algorithm, without having to know the entire input from the start.  
- better to handle streaming data  

{% highlight java %}
public class KClosestPoints {
    static class Point {
        double x;
        double y;
        double s; // score
        Point(double x, double y, double s) {
            this.x = x;
            this.y = y;
            this.s = s;
        }
    }

    static class MyComparator implements Comparator<Point> {
        public int compare(Point A, Point B) {
            double dist1 = A.x * A.x + A.y * A.y;
            double dist2 = B.x * B.x + B.y * B.y;
            if (dist1 != dist2) {
                return dist1 > dist2 ? -1 : 1; // maxheap, pop up big distance first
            } else {
                return A.s < B.s ?  -1 : 1; // maxheap, pop up low score first
            }
        }
    }

    public List<Point> findClosestK(List<Point> points, int k) {
        PriorityQueue<Point> heap = new PriorityQueue(new MyComparator()); // serve as a maxheap
        for (Point p : points) {
            heap.offer(p);
            if (heap.size() > k) {
                heap.poll();
            }
        }
        // k min dist points left in heap
        List<Point> res = new ArrayList<>();
        while (!heap.isEmpty()) {
            res.add(heap.poll());
        }
        Collections.reverse(res); // make min dist first
        return res;
    }
}
{% endhighlight %}
