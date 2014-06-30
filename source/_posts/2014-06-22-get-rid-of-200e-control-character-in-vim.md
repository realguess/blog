title: Get Rid of '<200e>' Control Characters in VIM
date: 2014-06-22 13:48:50
tags: [left-to-right-mark, control-character, unicode, vim]
---

The [left-to-right mark] character is an invisible control character. If you open up a text file in VIM containing such a character, you will see it as `<200e>` in Unicode.

If you want to get rid of all occurrences of this control character, you can do:

    :%s/<ctrl>+<shift>+u200e<enter>//g

That's `<ctrl>+<shift>+u` to enable entering the character by Unicode code points, followed by the actual character code and `<enter>` key. It will loke like this:

    :%s/<200e>//g

However, not all terminals are supported.

[left-to-right mark]: http://en.wikipedia.org/wiki/Left-to-right_mark
