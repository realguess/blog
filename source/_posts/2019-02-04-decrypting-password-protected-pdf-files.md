title: Decrypting Password Protected PDF Files
date: 2019-02-04T12:00:00
categories: [Computing]
tags: [qpdf, pdf, descrypt, password]
---

Receiving password protected PDF files? If you get annoyed to type the password every time, decrypt and save into a new file:

```sh
$ qpdf --password=secret --decrypt infile.pdf outfile.pdf
```

QPDF is a command-line tools and library for transforming PDF files, an alternative to [pdftk][].


[pdftk]: https://www.pdflabs.com/tools/pdftk-the-pdf-toolkit/

Notes:

```
qpdf 7.1.1
```
