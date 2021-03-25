---
layout: page
title: Standard Bloom Filter
permalink: /datastructure/standardbloomfilter
---
Implement a standard bloom filter. Support the following method:  

{% highlight txt %}
StandardBloomFilter(k) The constructor and you need to create k hash functions.
add(string) Add a string into bloom filter.
contains(string) Check a string whether exists in bloom filter
{% endhighlight %} 

Key Ideas:
- MyHash class provides hash function
- `BitSet bits = new BitSet(size of grids)` tracks if such hash value exists, if `bits.get(hash) = 1`  
- BloomFilter keeps a list of k MyHash instances
  - use each hash function to hash given value
  - to update the associated `pos`in bits

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

public class StandardBloomFilter {
    private List<MyHash> myHashes;
    private BitSet bits; // just work as a boolean[] but with less space
    private int k;

    public StandardBloomFilter(int k) {
        this.k = k;

        this.myHashes = new ArrayList<>();
        for (int i = 0; i < k; i++) {
            this.myHashes.add(new MyHash(100000 + i, 2 * i + 3)); // list of hash functions
        }

        this.bits = new BitSet(100000 + k); // number of grids, equal to MyHash capacity size
    }

    public void add(String word) {
        for (int i = 0; i < k; i++) { // use each hash function to hash the word
            int pos = myHashes.get(i).hash(word);
            bits.set(pos);
        }
    }

    public boolean contains(String word) {
        for (int i = 0; i < k; i++) { // use each hash function to check
            int pos = myHashes.get(i).hash(word);
            if (!bits.get(pos)) {
                return false;
            }
        }
        return true;
    }
}
{% endhighlight %}