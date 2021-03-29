---
layout: page
title: Concurrency Demo
permalink: /concurrency/concurrencydemo
---
The concurrency happns when we have main thread, and t thread runs together.  

{% highlight txt %}
Hello1, Hello2 who prints out first not sure
main thread and t thread are concurrent, whom is executed first not sure
however, Hello is always the last to be printed

output:
// maybe this
Main says Hello2
Thread says Hello1
Main says Hello3

// maybe this
Thread says Hello1
Main says Hello2
Main says Hello3
{% endhighlight %} 

{% highlight java %}
public class ThreadConcurrency {
    public static void main(String[] args) throws InterruptedException {
        Thread t = new Thread() { // anonymous class extends Thread used here
            @Override
            public void run() {
                System.out.println("Thread says Hello1");
            }
        };
        t.start();
        System.out.println("Main says Hello2");
        t.join(); // wait until t thread dies

        System.out.println("Main says Hello3");
    }
}
{% endhighlight %}

**JAVA Anonymous Class**

***extend a Class***
{% highlight java %}
ParentClass name = new ParentClass(constructor arguments) {
    @Override
    public <T> method() {
        ... // do somthing specific
    }
};

Thread t = new Thread() { // anonymous class extends Thread created here
    @Override
    public void run() {
        System.out.println("AnonymousMyThread t says Hello1");
    }
};
{% endhighlight %}

***implement an Interface***
{% highlight java %}
Interface name = new InterfaceName() {
    @Override
    public <T> method() {
        ...
    }
};

Runnable action = new Runnable() {
    @Override
    public void run() {
        ... // do something specific
    }
};
{% endhighlight %}


