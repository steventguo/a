---
layout: post
title: Working with big numbers in a small space
categories: [snippets, java, devblog]
tags: [java, devblog]
description: Proper handling and suffixing of overly large numbers, because we don't always have the space to display 2147483647.
---

In some cases, numbers can get pretty huge - and pretty ugly and unusable. As I built out my incremenetal (read: idle/clicker) game, I ran into the problem of my numbers growing way too big, way too fast. I didn't have space for all the characters to represent numbers beyond the thousands. The obvious solution was to suffix them - 1,000 becomes 1k, 1,000,000 becomes 1M, and so on.

The snippet I used below was written in Java, but it should be easily portable to any language, providing the right math utility functions.

{% highlight java %}

public static String[] suffixArray = new String[] {"", "k", "M", "B", "T", "Qa", "Qi", "Sx", "Sp", "Oc", "No", 
"De", "UnD", "DuD", "TrD", "QaD", "QiD", "SeD", "SpD", "OcD", "NoD", "Vi", "UnV"}; 

public static String format(double n) {
        double l = (floor(Math.log(Math.abs(n)) / Math.log(10)) <= 0) ? 0 : floor(Math.log(Math.abs(n)) / Math.log(10));
        double p = (l % 3 == 0) ? 2 : (((l - 1) % 3 == 0) ? 1 : 0);
        double r = (Math.abs(n) < 1000) ? floor(n) : floor(n / (Math.pow(10, floor(l / 3) * 3 - p))) / Math.pow(10, p);
        return r + suffixArray[(int)floor(l / 3)];
}

{% endhighlight %}
  
What's nice about this function is that each element in the suffixArray can be changed quickly and on the fly. Want a simple a -> b -> c suffix progression, instead of a confusing Qa -> Qi -> Sx one? All that has to be changed is the array of strings.

In action, it looks something like this:

{% highlight java %}

System.out.println(1000 +" -> " +  format(1000));
System.out.println(-1000000 +" -> " +  format(-1000000));
System.out.println(1234567891234567891L +" -> " + format(1234567891234567891L));

{% endhighlight %}

which produces...

{% highlight make %}

I/System.out: 1000 -> 1.0k
I/System.out: -1000000 -> -1.0M
I/System.out: 1234567891234567891 -> 1.23Qi

{% endhighlight %}
