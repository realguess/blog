title: Obtain Total Number of Notes in Google Keep
date: 2013-12-20 16:03:47
tags: [google-keep]
---

[Google Keep][keep] is a quick note taking service, having a lot less features than others like [Evernote][]. Therefore, you cannot even get the total number of notes. But you can quickly fire up a browser console, and type in the following script to get the note count:

    document.getElementsByClassName('IZ65Hb-n0tgWb').length

No jQuery needed, and problem solved.

[keep]: https://drive.google.com/keep
[evernote]: https://evernote.com/
