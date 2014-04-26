title: Use Vim Cryptographic Features to Encrypt Important Text Files
date: 2014-01-05 14:18:22
tags: [vim, cryptography, encryption]
---

You can protect your important text files by using [Vim](http://www.vim.org/) cryptographic features.

Set cryptographic method to [Blowfish][]:

    :set cryptmethod=blowfish

[Blowfish]: http://en.wikipedia.org/wiki/Blowfish_(cipher)

Set encryption key:

    :X

Save and get out:

    :wq

But the drawback is:

    $ grep 'Vim' encrypted.txt
    Binary file encrypted.txt matches

References:

- [Protect Notes with Vim Encryption](http://usevim.com/2013/11/01/vim-encryption/)
- [Encryption - Vim Tips Wiki](http://vim.wikia.com/wiki/Encryption)
