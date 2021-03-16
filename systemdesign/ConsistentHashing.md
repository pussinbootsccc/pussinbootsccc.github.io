---
layout: page
title: Consistent Hashing
permalink: /systemdesign/consistenthashing
---
一般的数据库进行horizontal shard的方法是指，把id % 服务器总数n,来得到这个id该到哪台机器上。
这种方法的缺点是，当数据继续增加，我们需要增加数据库服务器，将n变为n+1时，几乎所有的数据都要移动，这就造成了不consistent。  
为了减少这种简答取模带来的缺陷，出现了一种新的hash算法：一致性哈希的算法——Consistent Hashing。  

There are many ways to implement this algorithm. Here we implement a simple Consistent Hashing.
Suppose all the data is on one machine at the beginning. When adding to the nth machine, what is the distribution of the interval and the corresponding machine number?

Key Ideas:
- maintain a maxheap `queue` holds all machine nodes
- when adding a new machine, pop up the node with `max range size`, splits into two nodes
    - leave half of original size in this node
    - load another half into the new node
- put splited 2 nodes back into queue for next rounds

Notes:
- originally 1 node in queue
- simulate the splits processdure from 2 to N to genearte all nodes
- when #machines is odd, there is no evenly distribution

{% highlight bash %}
Input:
 n = 2,
Output:
[
  [0,179,1],
  [180,359,2]
]
Explanation:
represent 0~179 belongs to machine 1.
represent 180~359 belongs to machine 2.
{% endhighlight %}

{% highlight java %}
public class ConsistentHashing {
    static class Node { // machine node holds data within range of [start, end]
        int id;
        int start;
        int end;
        public Node (int start, int end, int id) {
            this.id = id;
            this.start = start;
            this.end = end;
        }
    }
    static class MyComparator implements Comparator<Node> {
        @Override
        public int compare(Node x, Node y) {
            if ((x.end - x.start) == (y.end - y.start)) { // size ties, low id first
                return x.id - y.id;
            }
            return (y.end - y.start) - (x.end - x.start); // larger size first
        }
    }
    public List<List<Integer>> consistentHashing(int N) {
        List<List<Integer>> res = new ArrayList<>();
        if (N == 1) {
            res.add(Arrays.asList(0, 359, 1));
            return res;
        }
        PriorityQueue<Node> queue = new PriorityQueue<>(new MyComparator());
        queue.offer(new Node(0, 359, 1)); // put the machine1 into queue

        for (int i = 2; i <= N; i++) { // simulate adding machines from 2 to N, each time poll a max-size node
            Node node = queue.poll(); // a machine has max size, going to split it
            int mid = (node.start + node.end) >> 1;
            Node nodeX = new Node(node.start, mid, node.id); // leave half of size in the original machine node
            Node nodeY = new Node(mid + 1, node.end, i); // transfer half of size into the new machine node
            queue.offer(nodeX);
            queue.offer(nodeY);
            }

        while (!queue.isEmpty()) { // fetch all nodes generated in the queue
            Node node = queue.poll();
            res.add(Arrays.asList(node.start, node.end, node.id));
        }
        return res;
    }
}
{% endhighlight %}