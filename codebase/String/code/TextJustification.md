---
layout: page
title: Text Justification
permalink: /algorithm/string/testjustification
---

Key Idea
1. greedy find rightmost index that can fit into a line width
2. call `justifyLine()` given `left`, `right` index
3. update `left = right + 1` to loop over the word list

Notes

- normal lines: distribute spaces in gaps
    - default spaces: `totalSpaces / # of gaps` in a line of words
    - remainder spaces: to be distributed evenly among gaps, until used up
    - `sb.append(defaultSpaces).append(" " if remainder-- > 0)`

- last line: single space in gaps
    - align with left, add a single space between words
    - padding spaces at right


{% highlight java %}
public class TextJustification {
    public List<String> fullJustify(String[] words, int maxWidth) {
        int left = 0;
        List<String> res = new ArrayList<>();
        while (left < words.length) {
            int right = findRight(left, words, maxWidth);
            res.add(justifyLine(left, right, words, maxWidth)); // add a justified line
            left = right + 1; // points to new starter
        }
        return res;
    }

    private String justifyLine(int left, int right, String[] words, int max) {
        if (left == right) return padding(words[left], max); // only 1 word in a line, padding right side

        boolean isLastLine = (right == words.length - 1);
        int gaps = right - left;
        int totalSpaces = max - wordsLength(left, right, words);
        String defaultSpace = isLastLine ? " " : blank(totalSpaces / gaps);
        int remainder = isLastLine ? 0 : totalSpaces % gaps; // distribute remainder space evenly among gaps

        StringBuilder sb = new StringBuilder();
        for (int i = left; i <= right - 1; i++) { // [left, right-1] words in line need adding spaces logic
            sb.append(words[i]).append(defaultSpace).append(remainder-- > 0 ? " " :  "");
        }
        sb.append(words[right]); // last word in the line should not adding space, 因为它右对齐
        return !isLastLine ? sb.toString() : padding(sb.toString(), max); // last line need padding on right
    }

    private int findRight(int left, String[] words, int max) { // greedy to go right as possible if can fit in this line
        int i = left;
        int charLen = words[i++].length();
        while (i < words.length && charLen + 1 + words[i].length() <= max) {
            charLen += 1 + words[i].length(); // a space + a word length
            i++;
        }
        return i - 1; // word index inclusive
    }

    private int wordsLength(int left, int right, String[] words) {
        int len = 0;
        for (int i = left; i <= right; i++) {
            len += words[i].length();
        }
        return len;
    }

    private String padding(String input, int max) { // add blanks at right side
        return input + blank(max - input.length());
    }

    private String blank(int n) { // n length of blank spaces
        char[] array = new char[n];
        Arrays.fill(array,' ');
        return new String(array);
    }
}
{% endhighlight %}
