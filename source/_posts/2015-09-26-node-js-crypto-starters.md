title: Node.js Crypto Starters
date: 2015-09-26 12:00:00
categories: [JavaScript]
tags: [starter, crypto, node, md5, sha256, digest]
---

Starter for the most common usage.

Create a MD5 hex hash:

```js
require('crypto').createHash('md5').update(data, 'utf8').digest('hex');
```

Create a SHA256 base64 hash:

```js
require('crypto').createHash('sha256').update(data, 'utf8').digest('base64');
```

Calculating the [digest] varies with encoding:

```js
> data = 'secret'
'secret'
> require('crypto').createHash('sha256').update(data, 'utf8').digest('hex');
'2bb80d537b1da3e38bd30361aa855686bde0eacd7162fef6a25fe97bf527a25b'
> require('crypto').createHash('sha256').update(data, 'utf8').digest('base64');
'K7gNU3sdo+OL0wNhqoVWhr3g6s1xYv72ol/pe/Unols='
> require('crypto').createHash('sha256').update(data, 'utf8').digest('binary');
'+¸\rS{\u001d£ãÓ\u0003aªV½àêÍqbþö¢_é{õ\'¢['
> require('crypto').createHash('sha256').update(data, 'utf8').digest();
<Buffer 2b b8 0d 53 7b 1d a3 e3 8b d3 03 61 aa 85 56 86 bd e0 ea cd 71 62 fe f6 a2 5f e9 7b f5 27 a2 5b>
> require('crypto').createHash('sha256').update(data, 'utf8').digest().toString();
'+�\rS{\u001d����\u0003a��V�����qb���_�{�\'�['
```

Both `hex` and `base64` are commonly used ones.

Needs an `input_encoding` when [update] data, otherwise there will be different results:

```sh
> data = '秘密'
'秘密'
> require('crypto').createHash('sha256').update(data, 'ascii').digest('hex');
'7588bc51fd0ff10db9c66549e5cb7969b9f6b7cf3ccee080137a8eb1aa06e718'
> require('crypto').createHash('sha256').update(data, 'utf8').digest('hex');
'062a2931da683a9897c5a0b597113b1e9fd0d5bfb63e2a5d7c88b724f7f55c02'
> require('crypto').createHash('sha256').update(data, 'binary').digest('hex');
'7588bc51fd0ff10db9c66549e5cb7969b9f6b7cf3ccee080137a8eb1aa06e718'
> require('crypto').createHash('sha256').update(data).digest('hex');
'7588bc51fd0ff10db9c66549e5cb7969b9f6b7cf3ccee080137a8eb1aa06e718'
```

Don't assume ASCII.

Parameter `data` must be a string or buffer, otherwise there will be an error:

```sh
TypeError: Not a string or buffer
```

[digest]: https://nodejs.org/api/crypto.html#crypto_hash_digest_encoding
[update]: https://nodejs.org/api/crypto.html#crypto_hash_update_data_input_encoding
