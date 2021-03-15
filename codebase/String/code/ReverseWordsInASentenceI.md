---
layout: page
title: Reverse Words In A Sentence I
permalink: /algorithm/string/reversewordsinasentencei
---

Input is without trailing/duplicate/leading spaces.
"I love Google" → "Google love I"

Key Idea
1. reverse whole input
2. find start index, end index of a word
3. reverse each word

Notes
- check (i-1) to see if a space, to determine if curr is start index of a word
- check (i+1) to see if a space, to determine if curr is end index of a word


{% highlight java %}
public class ReverseWordsInASentenceI {
    public String reverseWords(String input) {
        if (input == null || input.isEmpty()) {
            return input;
        }
        char[] array = input.toCharArray();
        reverse(array, 0, array.length - 1);
        int start = -1; // to declare first
        for (int i = 0; i < array.length; i++) {
            if (array[i] != ' ' && (i == 0 || array[i - 1] == ' ')) { // check front, see if find a word start
                start = i;
            }
            if (array[i] != ' ' && (i == array.length - 1 || array[i + 1] == ' ')) { // check back, see if find an end
                reverse(array, start, i); // end = i;
            }
        }
        return new String(array);
    }

    private void reverse(char[] array, int l, int r) {
        while (l < r) {
            char tmp = array[l];
            array[l] = array[r];
            array[r] = tmp;
            l++;
            r--;
        }
    }
}

{% endhighlight %}
