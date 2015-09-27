title: Get Started with the Cut Command of GNU Coreutils
date: 2015-09-27 12:00:00
categories: [Linux]
tags: [cut, gnu, coreutils, field, section, part]
---

A part of [GNU Coreutils], [cut] command likes [paste] and [join] is one that operates on fields. The `cut` command prints selected parts (fields or sections) of lines.

Things to keep in mind:

- Work on text stream line by line
- Print sections/parts/fields
- Either `-c`, `-b` or `-f` option must be used
- The default delimiter is `TAB`
- The delimiter must be a single character
- Consecutive delimiters need to be consolidated into one
- Cut multiple or a range of fields

Generate sample data and saved in the file `tab.txt`:

```sh
$ file=tab.txt &&              \
  for i in $(seq 1 9); do      \
    for j in $(seq 1 9); do    \
      if [ "$j" == "9" ]; then \
        echo -e "$i$j";        \
      else                     \
        echo -en "$i$j\t";     \
      fi;                      \
    done;                      \
  done > $file && cat $file
11      12      13      14      15      16      17      18      19
21      22      23      24      25      26      27      28      29
31      32      33      34      35      36      37      38      39
41      42      43      44      45      46      47      48      49
51      52      53      54      55      56      57      58      59
61      62      63      64      65      66      67      68      69
71      72      73      74      75      76      77      78      79
81      82      83      84      85      86      87      88      89
91      92      93      94      95      96      97      98      99
```

Fields on each line is separated by a tab character.

There is a required option:

```sh
$ cut tab.txt
cut: you must specify a list of bytes, characters, or fields
Try 'cut --help' for more information.
```

These options are:

- bytes: `-b` or `--bytes=LIST`
- characters: `-c` or `--characters=LIST`
- fields: `-f` or `--fields=LIST`

This is very odd. Option means optional, a default should be provided. But let's focus on the common used one: _fields_.

Cut the first and the ninth fields with the default delimiter `TAB`:

```sh
$ cut -f 1 tab.txt
11
21
31
41
51
61
71
81
91

$ cut -f 9 tab.txt
19
29
39
49
59
69
79
89
99
```

Use space as the delimiter:

```sh
$ cp tab.txt space.txt && sed -i 's/\t/ /g' $_ && cat $_
11 12 13 14 15 16 17 18 19
21 22 23 24 25 26 27 28 29
31 32 33 34 35 36 37 38 39
41 42 43 44 45 46 47 48 49
51 52 53 54 55 56 57 58 59
61 62 63 64 65 66 67 68 69
71 72 73 74 75 76 77 78 79
81 82 83 84 85 86 87 88 89
91 92 93 94 95 96 97 98 99
```

We must choose a different delimiter via `-d` or `--delimiter=DELIM` option:

```sh
$ cut -f 1 $_
11 12 13 14 15 16 17 18 19
21 22 23 24 25 26 27 28 29
31 32 33 34 35 36 37 38 39
41 42 43 44 45 46 47 48 49
51 52 53 54 55 56 57 58 59
61 62 63 64 65 66 67 68 69
71 72 73 74 75 76 77 78 79
81 82 83 84 85 86 87 88 89
91 92 93 94 95 96 97 98 99

$ cut -f 1 -d ' ' $_
11
21
31
41
51
61
71
81
91
```

Delimiter must be a single character:

```sh
$ cut -f 1 -d '\s' $_
cut: the delimiter must be a single character
Try 'cut --help' for more information.
```

Files containing mixed delimiters (tab and space):

```sh
$ cp tab.txt mixed.txt && sed -i 's/\(9.\)\t/\1 /g' $_ && cat $_
11      12      13      14      15      16      17      18      19
21      22      23      24      25      26      27      28      29
31      32      33      34      35      36      37      38      39
41      42      43      44      45      46      47      48      49
51      52      53      54      55      56      57      58      59
61      62      63      64      65      66      67      68      69
71      72      73      74      75      76      77      78      79
81      82      83      84      85      86      87      88      89
91 92 93 94 95 96 97 98 99
```

will print any line that contains no delimiter character:

```sh
$ cut -f 1 mixed.txt
11
21
31
41
51
61
71
81
91 92 93 94 95 96 97 98 99
```

Or use `-s` or `--only-delimited` option to omit those lines.

```sh
$ cut -sf 1 mixed.txt
11
21
31
41
51
61
71
81
```

But the better approach is to do data cleansing prior.

What about multiple `TAB` characters in the file:

```sh
$ sed -i 's/\(1.\)\t/\1\t\t/' mixed.txt && cat $_
11              12      13      14      15      16      17      18      19
21      22      23      24      25      26      27      28      29
31      32      33      34      35      36      37      38      39
41      42      43      44      45      46      47      48      49
51      52      53      54      55      56      57      58      59
61      62      63      64      65      66      67      68      69
71      72      73      74      75      76      77      78      79
81      82      83      84      85      86      87      88      89
91 92 93 94 95 96 97 98 99
```

An empty field is still a field:

```
$ cut -sf 2 mixed.txt

22
32
42
52
62
72
82
```

Therefore, the drawback here is that there cannot be multiple delimiter sticking together. Must perform data cleansing to reduce consecutive delimiters into a single one:

```sh
$ sed -i 's/\t\+/\t/g' mixed.txt && cat $_
11      12      13      14      15      16      17      18      19
21      22      23      24      25      26      27      28      29
31      32      33      34      35      36      37      38      39
41      42      43      44      45      46      47      48      49
51      52      53      54      55      56      57      58      59
61      62      63      64      65      66      67      68      69
71      72      73      74      75      76      77      78      79
81      82      83      84      85      86      87      88      89
91 92 93 94 95 96 97 98 99
```

Multiple fields can be cut:

```sh
$ cut -f 1,3,5,7,9 tab.txt
11      13      15      17      19
21      23      25      27      29
31      33      35      37      39
41      43      45      47      49
51      53      55      57      59
61      63      65      67      69
71      73      75      77      79
81      83      85      87      89
91      93      95      97      99
```

Cut a range:

```sh
$ cut -f 3-5 tab.txt
13      14      15
23      24      25
33      34      35
43      44      45
53      54      55
63      64      65
73      74      75
83      84      85
93      94      95
```

Cut up to or from a field:

```sh
$ cut -f -3 tab.txt
11      12      13
21      22      23
31      32      33
41      42      43
51      52      53
61      62      63
71      72      73
81      82      83
91      92      93

$ cut -f 7- tab.txt
17      18      19
27      28      29
37      38      39
47      48      49
57      58      59
67      68      69
77      78      79
87      88      89
97      98      99
```

When cut multiple fields, the fields are separated by the same delimiter used (indicated by `-d` field or TAB as the default). If change the output delimiter, it's not the job of `cut`, pipe to another program:

```sh
$ cut -f 3-5 tab.txt | sed 's/\t/ /g'
13 14 15
23 24 25
33 34 35
43 44 45
53 54 55
63 64 65
73 74 75
83 84 85
93 94 95
```

[GNU Coreutils]: http://www.gnu.org/software/coreutils/manual/html_node/index.html
[cut]: http://www.gnu.org/software/coreutils/manual/html_node/cut-invocation.html#cut-invocation
[paste]: http://www.gnu.org/software/coreutils/manual/html_node/paste-invocation.html#paste-invocation
[join]: http://www.gnu.org/software/coreutils/manual/html_node/join-invocation.html#join-invocation
