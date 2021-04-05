---
layout: page
title: Basic Calculator
permalink: /algorithm/stack/basiccalculator
---
To compute the accumulated stay duration in each loaction

{% highlight txt %} 
Input: 
"one plus three time two time nine time five minus two"

Outpt: 
269
{% endhighlight %}


{% highlight java %}
public class BasicCalculator {
    static final Map<String, String> mapping = new HashMap<>() {
        put("one", "1");
        put("two", "2");
        put("three", "3");
        put("four", "4");
        put("five", "5");
        put("six", "6");
        put("seven", "7");
        put("eight", "8");
        put("nine", "9");
        put("ten", "10");
        put("plus", "+");
        put("minus", "-");
        put("time",  "*");
    };

    public int calculate(String input) {
       String s =  translate(input);
       System.out.println(s);
       return calculateHelper(s);
    }

    public int calculateHelper(String s) {
        Deque<Integer> stack = new ArrayDeque<>(); // retain digit values
        int num = 0;
        char prevSign = '+'; // previous dummy sign at front
        for (int i = 0; i < s.length(); i++) {
            char curr = s.charAt(i);

            if (Character.isDigit(curr)) { // curr x is digit
                num = num * 10 + (curr - '0');
            }

            if (!Character.isDigit(curr) || i == s.length() - 1) { // curr is a sign or curr is last digit
                if (prevSign == '-') {
                    stack.offerFirst(-num);
                }
                if (prevSign == '+') {
                    stack.offerFirst(num);
                }
                if (prevSign == '*') { // 1 + 2 + 12 * 10 * 8 + ?
                    stack.offerFirst(stack.pollFirst() * num);
                }
                prevSign = curr; // only update when curr is a sign
                num = 0; // only reset when curr is a sign
            }
        }
        int res = 0;
        for (int val : stack) {
            res += val;
        }
        return res;
    }

    private String translate(String input) {
        String[] tokens = input.split(" ");
        StringBuilder sb = new StringBuilder();
        for (String token : tokens) {
            sb.append(mapping.get(token));
        }
        return sb.toString();
    }

    public static void main(String[] args) {
        BasicCalculator b = new BasicCalculator();
//        1+3*2*9*5-2
//        269
        System.out.println(b.calculate("one plus three time two time nine time five minus two"));
    }
}
{% endhighlight %}
