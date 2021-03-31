---
layout: page
title: High Frequency Actions
permalink: /algorithm/slidingwindow/highfrequencyactions
---
Given a string array, to find the most frequent pattern with 3 continous actions.

{% highlight txt %}
Input
Pickup, Select, Run
Get, Pickup, Select, Run
Get, Pickup
Deliver, Pickup, Select, Run

Output
Pick,Select,Run
{% endhighlight %}

Key Ideas
- maintain `map` to record `pattern : frequency`
- sliding window to compute the content in the `window` list
    - concat the window content as the key
    - or define your own `hash()` to compute the key = hash(window)

Notes
- sliding window, with a concrete window list, without the index on original array
- adjust window size to the required size, then do logic  
- a way to update freq map `map.put(key, map.getOrDefault(key, 0) + 1);`

{% highlight java %}
public class HighFrequencyActions {
    private static final int requiredSize = 3;
    public String findActions(String[] input) {
        Map<String, Integer> map = new HashMap<>(); // pattern : frequency

        for (String line : input) {
            String[] actions = line.split(", ");
            if (actions.length >= requiredSize) {
                helper(actions, map);
            }
        }
        return findBestPattern(map);
    }

    private String findBestPattern(Map<String, Integer> map) {
        String best = "";
        int maxCount = 0;
        for (Map.Entry<String, Integer> entry : map.entrySet()) {
            if (entry.getValue() > maxCount) {
                maxCount = entry.getValue();
                best = entry.getKey();
            }
        }
        return best;
    }

    private void helper(String[] array, Map<String, Integer> map) {
        List<String> window = new LinkedList<>();
        for (String ele : array) {
            window.add(ele);
            // too small
            if (window.size() < requiredSize)
                continue;
            // too large
            if (window.size() > requiredSize) {
                window.remove(0);
            }
            // right size
            String key = String.join(",", window);
            map.put(key, map.getOrDefault(key, 0) + 1);
        }
    }

    public static void main(String[] args) {
        HighFrequencyActions s = new HighFrequencyActions();
        class Test {
            void equals(String expected, String[] input) {
                String actual = s.findActions(input);
                if (actual.equals(expected)) {
                    System.out.println("PASSED");
                } else {
                    System.out.println("FAILED");
                    System.out.println("expected: " + expected + " , actual: " + actual);
                }
            }
        }

        String expected1 = "Pickup,Select,Run";
        String[] input1 = {"Pickup, Select, Run",
                        "Get, Pickup, Select, Run",
                        "Get, Pickup",
                        "Deliver, Pickup, Select, Run"};

        String expected2 = "Get,Select,Pickup";
        String[] input2 = {
                "Get, Select, Run",
                "Get, Select, Pickup, Run",
                "Get, Select, Run",
                "Get, Select, Pickup, Run",
                "Get, Select, Pickup, Run"};

        String expected3 = "";
        String[] input3 = {
                "Get, Select",
                "Get, Select"};

        String expected4 = "Get,Select,Run";
        String[] input4 = {
                "Get, Select",
                "Get, Select",
                "Get, Select, Run"};

        String expected5 = "Get,Get,Get";
        String[] input5 = {
                "Get, Get, Get, Get, Get",
                "Select, Select, Select, Select"};

        String expected6 = "X,X,X";
        String[] input6 = {
                "Y, Y, Y, Y, Y",
                "X, X, X, X, X, X, X"};

        String expected7 = "B,C,C";
        String[] input7 = {
                "B, C, C, C, C",
                "B, C, C, B, C, C"};

        Test tester = new Test();
        tester.equals(expected1, input1);
        tester.equals(expected2, input2);
        tester.equals(expected3, input3);
        tester.equals(expected4, input4);
        tester.equals(expected5, input5);
        tester.equals(expected6, input6);
        tester.equals(expected7, input7);
    }
}
{% endhighlight %}

**Define Your Own Hash Function**      
I want have a self-define hash function, that can compute a unique hashcode for a unique window arraylist object.    
`unique_code = hash(a window object)`

{% highlight java %}
{
...
// when generate key for this unique window
String key = hash(window); // replacement for String key = String.join(",", window);
lookup.put(key, String.join(",", window)); // addition reverse mapping from key to original window content
map.put(key, map.getOrDefault(key, 0) + 1);
...
}

private String hash(List<String> window) {
    long res = 17;
    for (String s : window) {
        res = 37 * res + s.hashCode();
    }
    return String.valueOf(res);
}
{% endhighlight %}

