---
layout: page
title: Counting Bloom Filter
permalink: /datastructure/countingbloomfilter
---
Implement a counting bloom filter. Support the following method:  

{% highlight txt %}
add(string). Add a string into bloom filter.
contains(string). Check a string whether exists in bloom filter.
remove(string). Remove a string from bloom filter.
{% endhighlight %} 

Key Ideas:


{% highlight java %}
class MyHash {
    private int seed;
    private int capacity;
    public MyHash(int capacity, int seed) {
        this.seed = seed;
        this.capacity = capacity; // the count of grids
    }
    public int hash(String s) {
        int res = 0;
        for (int i = 0; i < s.length(); i++) {
            res = (seed * res + s.charAt(i)) % this.capacity;
        }
        return res;
    }
}

public class CountingBloomFilter {
    private int[] counter; // count of frequence of such hash value
    private int k;
    private List<MyHash> myHashes;

    public CountingBloomFilter(int k) {
        this.k = k;
        this.myHashes = new ArrayList<>();
        for (int i = 0; i < k; i++) {
            this.myHashes.add(new MyHash(100000 + i, 2 * i + 3)); // list of hash functions
        }
        this.counter = new int[100000 + k]; // count of grids, equal to size of MyHash capacity
    }
    public void add(String word) {
        for (int i = 0; i < k; i++) {
            int pos = myHashes.get(i).hash(word);
            counter[pos] += 1;
        }
    }

    public void remove(String word) {
        for (int i = 0; i < k; i++) {
            int pos = myHashes.get(i).hash(word);
            counter[pos] -= 1;
        }
    }

    public boolean contains(String word) {
        for (int i = 0; i < k; i++) {
            int pos = myHashes.get(i).hash(word);
            if (counter[pos] <= 0) { // such hash not exist
                return false;
            }
        }
        return true;
    }
}
{% endhighlight %}