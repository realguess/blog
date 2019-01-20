title: Identifying Duplicate Files in the Current Directory
date: 2019-01-20T12:00:00
categories: [Computing]
tags: [duplicate, awk, duff, fdupes]
---

Here are some tools to find duplicates files:

- **duff**: Quickly find duplicate files
- **fdupes**: Finds duplicate files in a given set of directories

But we can also just cobble together with a few commonly used CLI tools:

```sh
$ find -type f | xargs -n 1 -I {} md5sum '{}' | sort | \
	awk '{if(k==$1){printf("%s\n%s\n",v,$2)}else{print("")};k=$1;v=$2}' | uniq
```

The command finds all files in the current directory, computes the MD5 checksum of each file, sorts them by the checksum first (`k`), then the file name (`v`). Finally, prints the duplicates ones and runs through a unique filter to obtain the final result.

Put into a shell script (`find-duplicates.sh`) with some dummy files to test the command and the above mentioned tools:

```sh
#!/bin/sh

apt-get update && apt-get install -y duff fdupes
cd /tmp && seq 3 | xargs -I {} bash -c 'touch file{}; date > date{}; echo $RANDOM > rand{}'

find -type f | xargs -n 1 -I {} md5sum '{}' | sort | \
	awk '{if(k==$1){printf("%s\n%s\n",v,$2)}else{print("")};k=$1;v=$2}' | uniq

duff -r .
fdupes -r .
```

Execute the script in a disposable environment:

```sh
$ cat find-duplicates.sh | docker run -i --rm debian:9.6
```

Output:

```
Ign:1 http://cdn-fastly.deb.debian.org/debian stretch InRelease
Get:2 http://security-cdn.debian.org/debian-security stretch/updates InRelease [94.3 kB]
Get:3 http://cdn-fastly.deb.debian.org/debian stretch-updates InRelease [91.0 kB]
Get:4 http://cdn-fastly.deb.debian.org/debian stretch Release [118 kB]
Get:5 http://security-cdn.debian.org/debian-security stretch/updates/main amd64 Packages [467 kB]
Get:6 http://cdn-fastly.deb.debian.org/debian stretch Release.gpg [2434 B]
Get:7 http://cdn-fastly.deb.debian.org/debian stretch-updates/main amd64 Packages [5152 B]
Get:8 http://cdn-fastly.deb.debian.org/debian stretch/main amd64 Packages [7089 kB]
Fetched 7868 kB in 1s (4119 kB/s)
Reading package lists...
Reading package lists...
Building dependency tree...
Reading state information...
The following NEW packages will be installed:
  duff fdupes
0 upgraded, 2 newly installed, 0 to remove and 3 not upgraded.
Need to get 52.4 kB of archives.
After this operation, 146 kB of additional disk space will be used.
Get:1 http://cdn-fastly.deb.debian.org/debian stretch/main amd64 fdupes amd64 1:1.6.1-1+b1 [21.2 kB]
Get:2 http://cdn-fastly.deb.debian.org/debian stretch/main amd64 duff amd64 0.5.2-1.1+b2 [31.2 kB]
debconf: delaying package configuration, since apt-utils is not installed
Fetched 52.4 kB in 0s (110 kB/s)
Selecting previously unselected package fdupes.
(Reading database ... 6498 files and directories currently installed.)
Preparing to unpack .../fdupes_1%3a1.6.1-1+b1_amd64.deb ...
Unpacking fdupes (1:1.6.1-1+b1) ...
Selecting previously unselected package duff.
Preparing to unpack .../duff_0.5.2-1.1+b2_amd64.deb ...
Unpacking duff (0.5.2-1.1+b2) ...
Setting up duff (0.5.2-1.1+b2) ...
Setting up fdupes (1:1.6.1-1+b1) ...
+ xargs -n 1 -I '{}' md5sum '{}'
+ sort
+ find -type f
+ awk '{if(k==$1){printf("%s\n%s\n",v,$2)}else{print("")};k=$1;v=$2}'
+ uniq

./file1
./file2
./file3

./date1
./date2
./date3
+ duff -r .
3 files in cluster 1 (0 bytes, digest da39a3ee5e6b4b0d3255bfef95601890afd80709)
./file3
./file1
./file2
3 files in cluster 2 (29 bytes, digest c20aeceea6a6b4bb1903d9124ea69223da08c69c)
./date2
./date1
./date3
+ fdupes -r .
./date2
./date1
./date3

./file3
./file1
./file2

```

Looks similar, same result.

However, those tools provide additional configurations such as following symlink files. Worth to install.

<!-- more -->

---

Notes:

```
debian:9.6 (docker)
fdupes 1.6.1
duff 0.5.2
```
