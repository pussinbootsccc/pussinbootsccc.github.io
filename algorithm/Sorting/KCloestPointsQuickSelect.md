---
layout: page
title: K Closest Points Quick Select
permalink: /algorithm/sorting/kclosestpointsquickselect
---
To compute the k nearest points to the origin, use score rating to break the tie.

{% highlight txt %}
Input
{3, 3, 4.9}, {5,-1, 5.0}, {-2 , 4, 4.88}, {4, 2, 4.7}, {-4, -2, 4.95}
k=3

Output
Point{x=3.0, y=3.0, s=4.9}, Point{x=-4.0, y=-2.0, s=4.95}, Point{x=-2.0, y=4.0, s=4.88}
{% endhighlight %}

Key Ideas
- quick select to partition list, as long as `partition index` falls into `target index`, the elements in `[0, k-1]` is the answer  
- writer `compare()` method to define priorty between two points  

Notes
- `offline algorithm`, need to know the size and take in the whole input at the start
- sorting is unstable, expected O(n), worst O(n^2)
- quickSort need to make recursion on both halfs, while quickSelect only do on the one half (range size reduced each round)

{% highlight java %}
public class KClosestPointsQuickSelect {
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

    public List<Point> findClosestK(List<Point> points, int k) {
        int n = points.size();
        quickSelect(points, 0, n - 1, k - 1);
        return points.subList(0, k);
    }

    private void quickSelect(List<Point> points, int left, int right, int index) {
        int mid = partition(points, left, right); // mid is pivot node's absolute index
        if (mid == index) {
            return;
        } else if (mid < index) {
            quickSelect(points, mid + 1, right, index);
        } else {
            quickSelect(points, left, mid - 1, index);
        }
    }

    // elements before i has high priority than pivot
    // elements after j has equal or low priority than pivot
    // return the partition index i
    private int partition(List<Point> points, int left, int right) {
        Point pivot = points.get(right); // use last node as pivot node
        int i = left;
        int j = right - 1;
        while (i <= j) {
            if (compare(points.get(i), pivot) < 0) { // curr I point has high priority than pivot , do nothing, move pointer
                i++;
            } else if (compare(points.get(j),pivot) >= 0) { // curr J point has equal or lower priority than pivot, do nothing, move pointer
                j--;
            } else { // need swap I, J points
                Point I = points.get(i);
                Point J = points.get(j);
                points.set(i, J);
                points.set(j, I);
                i++;
                j--;
            }
        }
        // put pivot to i index, and i index ele to the rightest position
        Point ele = points.get(i);
        points.set(i, pivot);
        points.set(right, ele);
        return i;
    }

    private int compare(Point p1, Point p2) {
        double dist1 = p1.x * p1.x + p1.y * p1.y;
        double dist2 = p2.x * p2.x + p2.y * p2.y;
        if (dist1 != dist2) {
            return dist1 < dist2 ? -1 : 1; // min distance first/lefter
        } else {
            return p1.s > p2.s ? -1 : 1; // high score first/lefter
        }
    }
}
{% endhighlight %}
