title: Get Yesterday's Date by date Command
date: 2013-11-14 15:27:59
tags: [date, linux]
---

By using Linux `date` command, we can get today's date in the following format:

```
$ date +%Y-%m-%d
2013-11-14
```

To get yesterday's date, we can use the `--date` or `-d` option:

```shell
% date -d 'yesterday' +%Y-%m-%d
2013-11-13
% date -d '-1 day' +%Y-%m-%d
2013-11-13
% date -d '1 day ago' +%Y-%m-%d
2013-11-13
```

As the manual explains:

```plain
DATE STRING
       The --date=STRING is a mostly free format human  readable  date  string
       such  as  "Sun, 29 Feb 2004 16:21:42 -0800" or "2004-02-29 16:21:42" or
       even "next Thursday".  A date string may contain items indicating  cal‐
       endar  date,  time of day, time zone, day of week, relative time, rela‐
       tive date, and numbers.  An empty string indicates the beginning of the
       day.   The date string format is more complex than is easily documented
       here but is fully described in the info documentation.
```

Just be careful when you are trying to get the date from last month.
