title: Calculate Hashes for All OpenSSL Digest Algorithms
date: 2015-09-27 12:00:00
categories: [JavaScript]
tags: [crypto, node, openssl]
---

Based on the following [Node.js] [Crypto] statement:

```js
require('crypto').createHash(algorithm).update('secret', 'utf8').digest('base64');
```

On a Ubuntu 14.04 system with [OpenSSL]:

```sh
$ openssl version
OpenSSL 1.0.1f 6 Jan 2014
```

List available digest algorithms:

```sh
$ openssl list-message-digest-algorithms
DSA
DSA-SHA
DSA-SHA1 => DSA
DSA-SHA1-old => DSA-SHA1
DSS1 => DSA-SHA1
MD4
MD5
RIPEMD160
RSA-MD4 => MD4
RSA-MD5 => MD5
RSA-RIPEMD160 => RIPEMD160
RSA-SHA => SHA
RSA-SHA1 => SHA1
RSA-SHA1-2 => RSA-SHA1
RSA-SHA224 => SHA224
RSA-SHA256 => SHA256
RSA-SHA384 => SHA384
RSA-SHA512 => SHA512
SHA
SHA1
SHA224
SHA256
SHA384
SHA512
DSA
DSA-SHA
dsaWithSHA1 => DSA
dss1 => DSA-SHA1
ecdsa-with-SHA1
MD4
md4WithRSAEncryption => MD4
MD5
md5WithRSAEncryption => MD5
ripemd => RIPEMD160
RIPEMD160
ripemd160WithRSA => RIPEMD160
rmd160 => RIPEMD160
SHA
SHA1
sha1WithRSAEncryption => SHA1
SHA224
sha224WithRSAEncryption => SHA224
SHA256
sha256WithRSAEncryption => SHA256
SHA384
sha384WithRSAEncryption => SHA384
SHA512
sha512WithRSAEncryption => SHA512
shaWithRSAEncryption => SHA
ssl2-md5 => MD5
ssl3-md5 => MD5
ssl3-sha1 => SHA1
whirlpool
```

Calculate hashes for each and every uniquely listed algorithm:

```sh
$ openssl list-message-digest-algorithms | \
  cut -d ' ' -f 1 | \
  xargs -n 1 -I {} node -e "algorithm = '{}'; \
  digest = require('crypto').createHash('{}').update('secret', 'utf8').digest('base64'); \
  console.log(algorithm, digest)" | \
  sort -u
DSA 5en6G6MezRroT3XKqkdPOmY/BfQ=
DSA-SHA1 5en6G6MezRroT3XKqkdPOmY/BfQ=
DSA-SHA1-old 5en6G6MezRroT3XKqkdPOmY/BfQ=
DSA-SHA 5en6G6MezRroT3XKqkdPOmY/BfQ=
dsaWithSHA1 5en6G6MezRroT3XKqkdPOmY/BfQ=
dss1 5en6G6MezRroT3XKqkdPOmY/BfQ=
DSS1 5en6G6MezRroT3XKqkdPOmY/BfQ=
ecdsa-with-SHA1 5en6G6MezRroT3XKqkdPOmY/BfQ=
md4WithRSAEncryption Z9Pa/vY/8AYDru83ac+/DQ==
MD4 Z9Pa/vY/8AYDru83ac+/DQ==
md5WithRSAEncryption Xr4ilOzQ4PCOq3aQ0qbuaQ==
MD5 Xr4ilOzQ4PCOq3aQ0qbuaQ==
ripemd160WithRSA zZi/AgLvB+OOh/a9lEXl5zMeLHg=
RIPEMD160 zZi/AgLvB+OOh/a9lEXl5zMeLHg=
ripemd zZi/AgLvB+OOh/a9lEXl5zMeLHg=
rmd160 zZi/AgLvB+OOh/a9lEXl5zMeLHg=
RSA-MD4 Z9Pa/vY/8AYDru83ac+/DQ==
RSA-MD5 Xr4ilOzQ4PCOq3aQ0qbuaQ==
RSA-RIPEMD160 zZi/AgLvB+OOh/a9lEXl5zMeLHg=
RSA-SHA1-2 5en6G6MezRroT3XKqkdPOmY/BfQ=
RSA-SHA1 5en6G6MezRroT3XKqkdPOmY/BfQ=
RSA-SHA224 lcf7ypKsUIOv2mKlZKPQFPw7cskUDjy5nqa/Eg==
RSA-SHA256 K7gNU3sdo+OL0wNhqoVWhr3g6s1xYv72ol/pe/Unols=
RSA-SHA384 WKd1ukESvjAFrkQHznV9iP2nHUBJe7gCbsrFTU4//HIyzo3jq1rLMK45dg/ufFPt
RSA-SHA512 vSsar3708Jvp9Szi2NWZZ02Bqp1qRCFpbcTZPdBhnWgs5WtNZKnvCXdhztmeD2cmW192CF5bDufKRpayrW/isg==
RSA-SHA f72qWouzjoc6vqk8vf5NBIVHFXs=
SHA1 5en6G6MezRroT3XKqkdPOmY/BfQ=
sha1WithRSAEncryption 5en6G6MezRroT3XKqkdPOmY/BfQ=
SHA224 lcf7ypKsUIOv2mKlZKPQFPw7cskUDjy5nqa/Eg==
sha224WithRSAEncryption lcf7ypKsUIOv2mKlZKPQFPw7cskUDjy5nqa/Eg==
SHA256 K7gNU3sdo+OL0wNhqoVWhr3g6s1xYv72ol/pe/Unols=
sha256WithRSAEncryption K7gNU3sdo+OL0wNhqoVWhr3g6s1xYv72ol/pe/Unols=
sha384WithRSAEncryption WKd1ukESvjAFrkQHznV9iP2nHUBJe7gCbsrFTU4//HIyzo3jq1rLMK45dg/ufFPt
SHA384 WKd1ukESvjAFrkQHznV9iP2nHUBJe7gCbsrFTU4//HIyzo3jq1rLMK45dg/ufFPt
SHA512 vSsar3708Jvp9Szi2NWZZ02Bqp1qRCFpbcTZPdBhnWgs5WtNZKnvCXdhztmeD2cmW192CF5bDufKRpayrW/isg==
sha512WithRSAEncryption vSsar3708Jvp9Szi2NWZZ02Bqp1qRCFpbcTZPdBhnWgs5WtNZKnvCXdhztmeD2cmW192CF5bDufKRpayrW/isg==
SHA f72qWouzjoc6vqk8vf5NBIVHFXs=
shaWithRSAEncryption f72qWouzjoc6vqk8vf5NBIVHFXs=
ssl2-md5 Xr4ilOzQ4PCOq3aQ0qbuaQ==
ssl3-md5 Xr4ilOzQ4PCOq3aQ0qbuaQ==
ssl3-sha1 5en6G6MezRroT3XKqkdPOmY/BfQ=
whirlpool 4GG4emdK44gOFZq1XtNdbF6Kau+sarCDBKUFiAGNN3soY5uxX97t8AbVfkX3tCmObf786vfJLIJqcI/m0RVusw==
```

[Ubuntu]: http://www.ubuntu.com/
[OpenSSL]: http://www.openssl.org/
[Node.js]: https://nodejs.org/api/
[Crypto]: https://nodejs.org/api/crypto.html
