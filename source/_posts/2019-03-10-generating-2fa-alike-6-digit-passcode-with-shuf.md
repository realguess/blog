title: Generating 2FA alike 6-digit Passcode with shuf
date: 2019-03-10T12:00:00
categories: [Computing]
tags: [shuf, shuffle, one-liner, cli, random, passcode]
---

This is a one-liner to generate 2FA alike 6-digit passcode:

```sh
$ for i in $(seq 6); do seq 0 9; done | shuf -n 6 | tr -d "\n"; echo
719154
```

Let's break it down.

To randomize 10 digits with shuf command, we can do either of the following:

```sh
$ seq 0 9 | shuf | tr -d "\n"; echo
0421568793

$ shuf -i 0-9 | tr -d "\n"; echo
8521369407
```

The shuf command provides an `-i` or `--input-range` option to generate numeric random sequence. This reduces the need for another command. The last `echo` command is used to add the newline.

If we just need a subset of them, such as 6 digits:

```sh
$ shuf -i 0-9 | head -n 6 | tr -d "\n"; echo
867923
```

Again, shuf has another option `-n` or `--head-count`, which is similar to the head command:

```sh
$ shuf -i 0-9 -n 6 | tr -d "\n"; echo
138472
```

The output looks like a 2FA passcode.

Let's try a few iterations:

```sh
$ for i in $(seq 10); do shuf -i 0-9 -n 6 | tr -d "\n"; echo; done
360724
945803
381670
654982
957186
852401
759601
968207
106953
753609
```

However, there is one problem. The digits are not repeatable.

This is easy to resolve. We can do it one digit at a time and repeat for six times:

```sh
$ for i in $(seq 6); do shuf -i 0-9 -n 1; done | tr -d "\n"; echo
013750
```

To avoid calling `shuf` multiple times, we can generate our data space first:

```sh
$ for i in $(seq 6); do seq 0 9; done | shuf -n 6 | tr -d "\n"; echo
781895
```

Let's test out a few examples:

```sh
$ for i in $(seq 10); do for j in $(seq 6); do seq 0 9; done | shuf -n 6 | tr -d "\n"; echo; done
614808
738581
864319
334667
319894
576062
072202
103342
770161
940559
```

Now they look more like those 2FA passcodes.

<!-- more -->

Finally, test the one-liner on two different shells:

```sh
$ docker run --rm bash:4.4.23 bash -c 'for i in $(seq 10); do for j in $(seq 6); do seq 0 9; done | shuf -n 6 | tr -d "\n"; echo; done'
746163
938135
456273
594338
367268
173899
578514
195571
481287
889203
```

And Ash:

```sh
$ docker run --rm bash:4.4.23 ash -c 'for i in $(seq 10); do for j in $(seq 6); do seq 0 9; don
e | shuf -n 6 | tr -d "\n"; echo; done'
272842
555047
101576
040439
588857
472194
865996
827611
786152
201563
```

_Note that this is just a demonstration of using CLI command to randomize an array, and the outcome looks like a 2FA passcode._
