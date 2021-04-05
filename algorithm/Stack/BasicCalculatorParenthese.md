---
layout: page
title: Basic Calculator Parenthese
permalink: /algorithm/stack/basiccalculatorparenthese
---
To compute the accumulated stay duration in each loaction

{% highlight txt %} 
Input: 
input = "(1+(4+5+2)-3)+(3+2)+1";

Outpt: 
15
{% endhighlight %}

{% highlight java %}
public class BasicCalculatorParentheses {
    public int calculate(String s) {
        int prevSign = 1; // as we only have "+" "-" token, just +1 ,-1 to represent
        int num = 0;
        int res = 0;
        Deque<int[]> stack = new ArrayDeque<>();
        for (int i = 0; i < s.length(); i++) {
            char ch = s.charAt(i);
            if (Character.isDigit(ch)) {
                num = num * 10 + (ch - '0');
            }
            if (!Character.isDigit(ch)) { // carry res
                if (ch == '+') {
                    res += prevSign * num;
                    prevSign = 1; // denote curr '+'
                    num = 0;
                }
                if (ch == '-') { // carry res
                    res += prevSign * num;
                    prevSign = -1; // denote curr '-'
                    num = 0;
                }
                if (ch == '(') { // buffer res, and reset for inner
                    stack.offerFirst(new int[] {prevSign, res});
                    prevSign = 1;
                    res = 0; // reset as for inner res
                    num = 0;

                }
                if (ch == ')') { // carry res
                    res += prevSign * num; // inner computation first
                    prevSign = 1;
                    num = 0;
                    int[] old = stack.pollFirst(); // aggregate inner with prev buffered res
                    res = old[1] + old[0] * res; // res = old.res + old.sign * res
                }
            }
            if (i == s.length() - 1) { // flush the last number to result
                res += prevSign * num;
            }
        }

        return res;
    }

    public static void main(String[] args) {
        BasicCalculatorParentheses s = new BasicCalculatorParentheses();
        String input = "(1+(4+5+2)-3)+(3+2)+1";
        System.out.println(s.calculate(input));
    }
}
{% endhighlight %}
