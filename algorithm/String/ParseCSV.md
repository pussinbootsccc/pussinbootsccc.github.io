---
layout: page
title: Parse CSV
permalink: /algorithm/string/parsecsv
---

{% highlight java %}
public class CsvParser {
    public String parse(String str) {
        int n = str.length();
        boolean inQuote = false;
        StringBuilder sb = new StringBuilder();
        List<String> res = new ArrayList<>();
        for (int i = 0; i < n; i++) {
            char ch = str.charAt(i);
            if (!inQuote) {
                if (ch == '\"') {
                    inQuote = true;
                } else if (ch == ',') { // true delimiter, flush sb
                    res.add(sb.toString());
                    sb.setLength(0);
                } else {
                    if (ch == ' ') { // skip un-meaningful spaces
                        continue;
                    }
                    sb.append(ch);
                }
            } else {
                if (ch == '\"') {
                    if (i + 1 < n && str.charAt(i + 1) == '\"') { // the double quotes to a quote
                        sb.append("\"");
                        i++;
                    } else { // the end quote
                        inQuote = false;
                    }
                } else {
                    sb.append(ch);
                }
            }
        }
        if (sb.length() > 0) {
            res.add(sb.toString());
        }
        return String.join("|", res);
    }

    public static void main(String[] args) {
        String csv = "Jane, Roberts,   janer@msn.com,  \"San Francisco,  CA\", 0.98";
        String expected = "Jane|Roberts|janer@msn.com|San Francisco,  CA|0.98";

        CsvParser s = new CsvParser();
        String actual = s.parse(csv);
        System.out.println(actual);
        System.out.println(expected.equals(actual));
    }
}
{% endhighlight %}
