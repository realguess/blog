title: Run GNOME Terminal with Named Multi-Tabs
date: 2014-11-22 12:00:00
category: [Tools]
tags: [gnome, terminal, profile, multiple]
---

[GNOME Terminal] is a terminal emulation application, installed by default in Ubuntu. GNOME Terminal supports multiple profiles. So, create a few [profiles], then execute them one for each tab:

```sh
$ gnome-terminal --tab-with-profile=work --tab-with-profile=life
```

<!-- more -->

Additionally, set the title for the window or tab and run custom command:

![GNOME Terminal Profile Title and Custom Command](gnome-terminal-profile.png)

Also, provide a consistent layout and format:

```plain
Custom terminal size: 172 x 43
Font: Monospace 11
```

Now, you have multiple terminal tabs in a single GNOME Terminal window, possibly one for work, another one for life.

[GNOME Terminal]: http://en.wikipedia.org/wiki/GNOME_Terminal
[profiles]: https://help.gnome.org/users/gnome-terminal/stable/pref-profiles.html.en
