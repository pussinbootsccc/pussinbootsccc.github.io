---
layout: page
title: Max Bombs
permalink: /algorithm/bfs/maxbombs
---
  
{% highlight bash %}


{% endhighlight %}

Key Ideas



{% highlight java %}
public class Bomb {
    static class Bomb{
        double x;
        double y;
        double r;

        @Override
        public String toString() {
            return "Bomb{" +
                    "x=" + x +
                    ", y=" + y +
                    ", r=" + r +
                    '}';
        }

        public Bomb(double x, double y, double r) {
            this.x = x;
            this.y = y;
            this.r = r;
        }

    }


    public Bomb findBest(List<Bomb> bombs) {
        int max = 0;
        Bomb best = null;
        for (Bomb thisBomb : bombs) {
            int count = bfsHelper(thisBomb, bombs);
            if (count > max) {
                max = count;
                best = thisBomb;
            }
        }
        return best;
    }

    private int bfsHelper(Bomb bomb, List<Bomb> bombs) {
        int count = 0;
        Set<Bomb> visited = new HashSet<>();  // not adding the same bomb to queue twice
        Queue<Bomb> queue = new ArrayDeque<>(); // retains reachable bombs
        queue.offer(bomb);
        visited.add(bomb);
        while (!queue.isEmpty()) {
            Bomb curr = queue.poll();
            count++;
            for (Bomb neighbor : findNeighbors(curr, bombs)) {
                if (!visited.contains(neighbor)) {
                    visited.add(neighbor);
                    queue.offer(neighbor);
                }
            }
        }
        return count;
    }

    private List<Bomb> findNeighbors(Bomb curr, List<Bomb> bombs) {
        List<Bomb> res = new ArrayList<>();
        for (Bomb tmp : bombs) {
            if (tmp != curr && distanceSquare(curr, tmp) <= curr.r * curr.r) {
                res.add(tmp);
            }
        }
        return res;
    }

    private double distanceSquare(Bomb a, Bomb b) {
        return (a.x - b.x) * (a.x - b.x) + (a.y - b.y) * (a.y - b.y);
    }

    public static void main(String[] args) {
        Bomb c = new Bomb(1, 1, 0.9);
        Bomb b = new Bomb(0, 0, 2.2);
        Bomb a = new Bomb(-0.5, 0.5, 0.1);
        Bomb d = new Bomb(3, 3, 0.1);
        List<Bomb> bombs = new ArrayList<>();
        bombs.add(a);
        bombs.add(b);
        bombs.add(c);

        Bomb s = new Bomb();
        Bomb best = s.findBest(bombs);
    }
}
{% endhighlight %}
