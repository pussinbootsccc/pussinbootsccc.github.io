---
layout: page
title: Thread Demo
permalink: /concurrency/threaddemo
---
Ways to create a thread

{% highlight java %}
HelloThread extends Thread {
    @Override
    public void run() {
        // do something

    }
}

HelloRunnable implements Runnable {
    @Override
    public void run() {
        // do something

    }
}
{% endhighlight %} 

**via Thread**
{% highlight java %}
public class HelloThread extends Thread{
    @Override
    public void run() {
        System.out.println("HelloThread t runs");
    }

    public static void main(String[] args) {
        Thread t = new HelloThread();
        t.start();
    }
}
{% endhighlight %}

**via Runnable**
{% highlight java %}
public class HelloRunnable implements Runnable {
    @Override
    public void run() {
        System.out.println("HelloRunnable t runs");
    }

    public static void main(String[] args) {
        Thread t = new Thread(new HelloRunnable()); // need to pass in runnable object to create a thread
        t.start();
    }
}
{% endhighlight %}