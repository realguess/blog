title: Sharpening the Ax Before Chopping Down a Tree
date: 2014-05-11 14:11:43
tags: [heartbleed, openssl, libssl, ubuntu, nginx, abraham-lincoln]
---

I was helping to examine a server that was impacted by [Heartbleed]. According to the developer who was patching the server, he had updated the [OpenSSL] library to the following:

```
$ openssl version -a
OpenSSL 1.0.1g 7 Apr 2014
built on: Fri Apr 18 11:04:34 EDT 2014
platform: linux-x86_64
options: bn(64,64) rc4(16x,int) des(idx,cisc,16,int) idea(int) 
blowfish(idx) 
compiler: gcc -DOPENSSL_THREADS -D_REENTRANT -DDSO_DLFCN -DHAVE_DLFCN_H 
-Wa,--noexecstack -m64 -DL_ENDIAN -DTERMIO -O3 -Wall -DOPENSSL_IA32_SSE2
 -DOPENSSL_BN_ASM_MONT -DOPENSSL_BN_ASM_MONT5 -DOPENSSL_BN_ASM_GF2m 
-DSHA1_ASM -DSHA256_ASM -DSHA512_ASM -DMD5_ASM -DAES_ASM -DVPAES_ASM 
-DBSAES_ASM -DWHIRLPOOL_ASM -DGHASH_ASM
OPENSSLDIR: "/usr/ssl"
```

And the developer claimed: "According to <http://heartbleed.com/>. OpenSSL 1.0.1g is NOT vulnerable. Also I have restarted all services on this server."

So, OpenSSL has been updated and the all services have been restarted, but why does the problem still persist?

I took a look at the command history he ran:

```
wget http://www.openssl.org/source/openssl-1.0.1g.tar.gz
ls
tar xvzf openssl-1.0.1g.tar.gz
cd openssl-1.0.1g/
sudo ./config --prefix=/usr
sudo make
sudo make install
exit
openssl version -a
sudo reboot
```

The OpenSSL library has been built from the source, which is fine, but the problem is that the Nginx server was still using the old library distributed by Ubuntu:

```
$ ldd `which nginx` | grep ssl
        libssl.so.1.0.0 => /lib/x86_64-linux-gnu/libssl.so.1.0.0 (0x00007fafe82a3000)
$ strings /lib/x86_64-linux-gnu/libssl.so.1.0.0 | grep '^OpenSSL '
OpenSSL 1.0.1c 10 May 2012
```

In effect, there were two versions of OpenSSL library installed in the system, one was built from the source, and another one was managed by `dpkg`:

```
$ dpkg -l openssl
||/ Name                          Version             Architecture
+++-=============================-===================-===================
ii  openssl                       1.0.1c-4ubuntu8.2   amd64
```

However, the bigger problem is the version of the operating system:
```
$ lsb_release -a
No LSB modules are available.
Distributor ID: Ubuntu
Description:    Ubuntu 13.04
Release:        13.04
Codename:       raring
```

Ubuntu 13.04 is not supported anymore according to <https://wiki.ubuntu.com/Releases>. The developer probably issued `apt-get upgrade`, but nothing to be updated, because Ubuntu stopped supporting the release. Therefore, no security update. And Ubuntu 13.04 is not listed in [Ubuntu Security Notice USN-2165-1]. So, the developer opted for building the library from the source. After installation from the source, the binary `openssl` was overridden by the source build, and the command `openssl version` showed the latest and patched version `1.0.1g`.

To fix the problem, we need to reinstall the package first:

    $ sudo apt-get install --reinstall openssl

Now, this will revert control back to `apt-get` and overwrite the binary `/usr/bin/openssl`:

```
$ openssl version -a
OpenSSL 1.0.1c 10 May 2012
built on: Wed Jan  8 20:51:55 UTC 2014
platform: debian-amd64
options:  bn(64,64) rc4(16x,int) des(idx,cisc,16,int) blowfish(idx) 
compiler: cc -fPIC -DOPENSSL_PIC -DZLIB -DOPENSSL_THREADS -D_REENTRANT -DDSO_DLFCN -DHAVE_DLFCN_H -m64 -DL_ENDIAN -DTERMIO -g -O2 -fstack-protector --param=ssp-buffer-size=4 -Wformat -Werror=format-security -D_FORTIFY_SOURCE=2 -Wl,-Bsymbolic-functions -Wl,-z,relro -Wa,--noexecstack -Wall -DOPENSSL_NO_TLS1_2_CLIENT -DOPENSSL_MAX_TLS1_2_CIPHER_LENGTH=50 -DMD32_REG_T=int -DOPENSSL_IA32_SSE2 -DOPENSSL_BN_ASM_MONT -DOPENSSL_BN_ASM_MONT5 -DOPENSSL_BN_ASM_GF2m -DSHA1_ASM -DSHA256_ASM -DSHA512_ASM -DMD5_ASM -DAES_ASM -DVPAES_ASM -DBSAES_ASM -DWHIRLPOOL_ASM -DGHASH_ASM
OPENSSLDIR: "/usr/lib/ssl"
```

And then we must perform the distribution upgrade to the latest long term support version, in order to continue receiving updates.

The lesson I have learned from this is that if you are going the wrong direction, no matter how hard you work, you are not going to make it. Make sure to take the initial investment, and really understand the true cause of the problem before attempting to resolve the issue. And don't blindly follow the procedure. Understand it first, and adapt to your specific situation. As Abraham Lincoln once said:

> "If I have nine hours to chop down a tree, I'd spend the first six sharpening my ax."


[Heartbleed]: http://heartbleed.com/
[OpenSSL]: http://www.openssl.org/
[Ubuntu Security Notice USN-2165-1]: http://www.ubuntu.com/usn/usn-2165-1/

