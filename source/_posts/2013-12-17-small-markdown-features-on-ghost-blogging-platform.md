title: Small Markdown Features on Ghost Blogging Platform
date: 2013-12-17 18:05:38
tags: [ghost, markdown, automatic-link, line-divider]
---

I have known about [Ghost][] since its [KickStarter campaign][campaign]. And recently they just launched the hosted platform for Ghost. I like to set up and run it myself, but the hosted platform is 100% customized to run Ghost blogs. So it could be more efficient and reliable than tinkering on my own. While I was testing it, I have noticed two small [Markdown][] features supported by Ghost:

**Automatic links**

You do not need to enclose the fully qualified URL in angular brackets, URL like `http://ghost.org` will be automatically linked up. But this works well without language like English where a space denotes as a separator. When I work with language like Chinese, this frequently creating a problem where the Chinese characters are treated as parts of the link. Therefore, I still prefer to write it with angular brackets and get into the habit in doing:

    <http://ghost.org/>

**Line divider**

One feature I like so far is the support of a line divider. Just need three dashes in a line, it will be converted into a fancy divider. This is great for writing references and footnotes.


_<small>This is a quick blog for getting started with Ghost.</small>_

---

Exploration continues...


[ghost]: http://ghost.org/
[campaign]: http://www.kickstarter.com/projects/johnonolan/ghost-just-a-blogging-platform
[markdown]: http://daringfireball.net/projects/markdown/
