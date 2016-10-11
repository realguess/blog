title: A CLI Method to Check SSL Certificate Expiration Date
date: 2016-10-11 12:00:00
categories: [Computing]
tags: [cli, expiration, date, ssl, openssl, xargs]
---

I know that browser does this automatically, but it might come in handy if you need to check the expiration date of a SSL certificate through CLI. The key is `openssl`, OpenSSL command line tool.

```plain
$ echo | openssl s_client -connect example.com:443 2> /dev/null | \
  openssl x509 -noout -enddate
notAfter=Nov 28 12:00:00 2018 GMT
```

The command is consisted of two parts:

- Retrieve SSL certificate from the server
- Extract the expiration date data

> The openssl program is a command line tool for using the various cryptography functions of OpenSSL's crypto library from the shell. It can be used for[^1]
>
> - Creation and management of private keys, public keys and parameters
> - Public key cryptographic operations
> - Creation of X.509 certificates, CSRs and CRLs
> - Calculation of Message Digests
> - Encryption and Decryption with Ciphers
> - SSL/TLS Client and Server Tests
> - Handling of S/MIME signed or encrypted mail
> - Time Stamp requests, generation and verification

What we need here is to perform *SSL/TLS Client and Server Tests*.

`s_client` is one of the standard commands of `openssl` command line tool:

> This implements a generic SSL/TLS client which can establish a transparent connection to a remote server speaking SSL/TLS. It's intended for testing purposes only and provides only rudimentary interface functionality but internally uses mostly all functionality of the OpenSSL ssl library.[^1]

Dig deeper into `s_client` command:

> The s_client command implements a generic SSL/TLS client which connects to a remote host using SSL/TLS. It is a very useful diagnostic tool for SSL servers.[^2]

Option `-connect host:port`:

> This specifies the host and optional port to connect to. If not specified then an attempt is made to connect to the local host on port 4433.[^2]

And the format is:

```sh
$ openssl s_client -connect servername:443 > data
```

If a connection is established, `openssl` enters interactive mode:

> If a connection is established with an SSL server then any data received from the server is displayed and any key presses will be sent to the server. When used interactively (which means neither `-quiet` nor `-ign_eof` have been given), the session will be renegotiated if the line begins with an R, and if the line begins with a Q or if end of file is reached, the connection will be closed down.[^2]

To quit, type `Q` or `<ctr>+d` (EOF).

```plain
$ openssl s_client -connect example.com:443 > /tmp/example.com
depth=1 C = US, O = DigiCert Inc, OU = www.digicert.com, CN = DigiCert SHA2 High Assurance
 Server CA
verify error:num=20:unable to get local issuer certificate
verify return:0
Q
DONE
```

Dump the session data:

```plain
$ cat /tmp/example.com
CONNECTED(00000003)
---
Certificate chain
 0 s:/C=US/ST=California/L=Los Angeles/O=Internet Corporation for Assigned Names and Numbers/OU=Technology/CN=www.example.org
   i:/C=US/O=DigiCert Inc/OU=www.digicert.com/CN=DigiCert SHA2 High Assurance Server CA
 1 s:/C=US/O=DigiCert Inc/OU=www.digicert.com/CN=DigiCert SHA2 High Assurance Server CA
   i:/C=US/O=DigiCert Inc/OU=www.digicert.com/CN=DigiCert High Assurance EV Root CA
---
Server certificate
-----BEGIN CERTIFICATE-----
MIIF8jCCBNqgAwIBAgIQDmTF+8I2reFLFyrrQceMsDANBgkqhkiG9w0BAQsFADBw
MQswCQYDVQQGEwJVUzEVMBMGA1UEChMMRGlnaUNlcnQgSW5jMRkwFwYDVQQLExB3
d3cuZGlnaWNlcnQuY29tMS8wLQYDVQQDEyZEaWdpQ2VydCBTSEEyIEhpZ2ggQXNz
dXJhbmNlIFNlcnZlciBDQTAeFw0xNTExMDMwMDAwMDBaFw0xODExMjgxMjAwMDBa
MIGlMQswCQYDVQQGEwJVUzETMBEGA1UECBMKQ2FsaWZvcm5pYTEUMBIGA1UEBxML
TG9zIEFuZ2VsZXMxPDA6BgNVBAoTM0ludGVybmV0IENvcnBvcmF0aW9uIGZvciBB
c3NpZ25lZCBOYW1lcyBhbmQgTnVtYmVyczETMBEGA1UECxMKVGVjaG5vbG9neTEY
MBYGA1UEAxMPd3d3LmV4YW1wbGUub3JnMIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8A
MIIBCgKCAQEAs0CWL2FjPiXBl61lRfvvE0KzLJmG9LWAC3bcBjgsH6NiVVo2dt6u
Xfzi5bTm7F3K7srfUBYkLO78mraM9qizrHoIeyofrV/n+pZZJauQsPjCPxMEJnRo
D8Z4KpWKX0LyDu1SputoI4nlQ/htEhtiQnuoBfNZxF7WxcxGwEsZuS1KcXIkHl5V
RJOreKFHTaXcB1qcZ/QRaBIv0yhxvK1yBTwWddT4cli6GfHcCe3xGMaSL328Fgs3
jYrvG29PueB6VJi/tbbPu6qTfwp/H1brqdjh29U52Bhb0fJkM9DWxCP/Cattcc7a
z8EXnCO+LK8vkhw/kAiJWPKx4RBvgy73nwIDAQABo4ICUDCCAkwwHwYDVR0jBBgw
FoAUUWj/kK8CB3U8zNllZGKiErhZcjswHQYDVR0OBBYEFKZPYB4fLdHn8SOgKpUW
5Oia6m5IMIGBBgNVHREEejB4gg93d3cuZXhhbXBsZS5vcmeCC2V4YW1wbGUuY29t
ggtleGFtcGxlLmVkdYILZXhhbXBsZS5uZXSCC2V4YW1wbGUub3Jngg93d3cuZXhh
bXBsZS5jb22CD3d3dy5leGFtcGxlLmVkdYIPd3d3LmV4YW1wbGUubmV0MA4GA1Ud
DwEB/wQEAwIFoDAdBgNVHSUEFjAUBggrBgEFBQcDAQYIKwYBBQUHAwIwdQYDVR0f
BG4wbDA0oDKgMIYuaHR0cDovL2NybDMuZGlnaWNlcnQuY29tL3NoYTItaGEtc2Vy
dmVyLWc0LmNybDA0oDKgMIYuaHR0cDovL2NybDQuZGlnaWNlcnQuY29tL3NoYTIt
aGEtc2VydmVyLWc0LmNybDBMBgNVHSAERTBDMDcGCWCGSAGG/WwBATAqMCgGCCsG
AQUFBwIBFhxodHRwczovL3d3dy5kaWdpY2VydC5jb20vQ1BTMAgGBmeBDAECAjCB
gwYIKwYBBQUHAQEEdzB1MCQGCCsGAQUFBzABhhhodHRwOi8vb2NzcC5kaWdpY2Vy
dC5jb20wTQYIKwYBBQUHMAKGQWh0dHA6Ly9jYWNlcnRzLmRpZ2ljZXJ0LmNvbS9E
aWdpQ2VydFNIQTJIaWdoQXNzdXJhbmNlU2VydmVyQ0EuY3J0MAwGA1UdEwEB/wQC
MAAwDQYJKoZIhvcNAQELBQADggEBAISomhGn2L0LJn5SJHuyVZ3qMIlRCIdvqe0Q
6ls+C8ctRwRO3UU3x8q8OH+2ahxlQmpzdC5al4XQzJLiLjiJ2Q1p+hub8MFiMmVP
PZjb2tZm2ipWVuMRM+zgpRVM6nVJ9F3vFfUSHOb4/JsEIUvPY+d8/Krc+kPQwLvy
ieqRbcuFjmqfyPmUv1U9QoI4TQikpw7TZU0zYZANP4C/gj4Ry48/znmUaRvy2kvI
l7gRQ21qJTK5suoiYoYNo3J9T+pXPGU7Lydz/HwW+w0DpArtAaukI8aNX4ohFUKS
wDSiIIWIWJiJGbEeIO0TIFwEVWTOnbNl/faPXpk5IRXicapqiII=
-----END CERTIFICATE-----
subject=/C=US/ST=California/L=Los Angeles/O=Internet Corporation for Assigned Names and Numbers/OU=Technology/CN=www.example.org
issuer=/C=US/O=DigiCert Inc/OU=www.digicert.com/CN=DigiCert SHA2 High Assurance Server CA ---
No client certificate CA names sent
---
SSL handshake has read 3393 bytes and written 421 bytes
---
New, TLSv1/SSLv3, Cipher is ECDHE-RSA-AES128-GCM-SHA256
Server public key is 2048 bit
Secure Renegotiation IS supported
Compression: NONE
Expansion: NONE
SSL-Session:
    Protocol  : TLSv1.2
    Cipher    : ECDHE-RSA-AES128-GCM-SHA256
    Session-ID: C828441A824CE7B0F6A74BBE890AB23727445EAE8521E19F438E679C39E969B1
    Session-ID-ctx:
    Master-Key: 38BE4F754FBCB5F41650AD91AA5588ACD88B75D7939487052D9FD2790476E7C6D2512A6451A3FC102958488BF173CB54
    Key-Arg   : None
    PSK identity: None
    PSK identity hint: None
    SRP username: None
    TLS session ticket lifetime hint: 300 (seconds)
    TLS session ticket:
    0000 - 83 70 c4 28 23 ee 9c 9e-87 1b 96 bf 44 76 ee d3   .p.(#.......Dv..
    0010 - 45 c9 be ee a5 c5 42 49-c9 08 35 10 ba 79 03 b4   E.....BI..5..y..
    0020 - 46 99 9a f2 d3 7b b5 f2-ad 9e 10 5c 7a 61 c3 0e   F....{.....\za..
    0030 - e0 09 aa 7a 5e 2a 2e bb-42 6a 08 18 16 ae 56 66   ...z^*..Bj....Vf
    0040 - 11 0c 96 1a 4a 20 9f 50-6d f7 e2 53 00 75 6f 07   ....J .Pm..S.uo.
    0050 - 7f 94 bf 4a 5f e1 f6 3b-d5 b7 6c 11 bc 33 7b 10   ...J_..;..l..3{.
    0060 - 78 e3 81 a0 0b 83 25 d6-e6 a5 64 90 59 24 a6 e9   x.....%...d.Y$..
    0070 - 9b b6 4b be 9e 42 1b 03-e0 d7 76 e9 32 87 3e 0d   ..K..B....v.2.>.
    0080 - 3d 09 09 32 18 fd 04 63-93 fe 33 9f 47 50 d4 c1   =..2...c..3.GP..
    0090 - e1 a9 21 cc 67 30 ea 03-7f c1 ee 2a 54 02 c8 11   ..!.g0.....*T...

    Start Time: 1475971200
    Timeout   : 300 (sec)
    Verify return code: 20 (unable to get local issuer certificate)
---
```

To avoid the interactive mode, we can pipe an empty string into the command:

```plain
$ echo | openssl s_client -connect example.com:443 > /tmp/example.com 2> /dev/null
```

Now we have retrieved the SSL certificate from the server. Next, extract the expiration date. This is done by using the standard command `x509`:

<!-- more -->

```sh
$ cat /tmp/example.com | openssl x509 -noout -enddate
```

Standard command `x509` is used for X.509 certificate data management.

> The x509 command is a multi purpose certificate utility. It can be used to display certificate information, convert certificates to various forms, sign certificate requests like a "mini CA" or edit certificate trust settings.[^3]

What we need is to display certificate information, in particular, the expiration date. That's the option `-enddate`:

> prints out the expiry date of the certificate, that is the notAfter date.[^3]

Command:

```plain
$ cat /tmp/example.com | openssl x509 -enddate
notAfter=Nov 28 12:00:00 2018 GMT
-----BEGIN CERTIFICATE-----
MIIF8jCCBNqgAwIBAgIQDmTF+8I2reFLFyrrQceMsDANBgkqhkiG9w0BAQsFADBw
MQswCQYDVQQGEwJVUzEVMBMGA1UEChMMRGlnaUNlcnQgSW5jMRkwFwYDVQQLExB3
d3cuZGlnaWNlcnQuY29tMS8wLQYDVQQDEyZEaWdpQ2VydCBTSEEyIEhpZ2ggQXNz
dXJhbmNlIFNlcnZlciBDQTAeFw0xNTExMDMwMDAwMDBaFw0xODExMjgxMjAwMDBa
MIGlMQswCQYDVQQGEwJVUzETMBEGA1UECBMKQ2FsaWZvcm5pYTEUMBIGA1UEBxML
TG9zIEFuZ2VsZXMxPDA6BgNVBAoTM0ludGVybmV0IENvcnBvcmF0aW9uIGZvciBB
c3NpZ25lZCBOYW1lcyBhbmQgTnVtYmVyczETMBEGA1UECxMKVGVjaG5vbG9neTEY
MBYGA1UEAxMPd3d3LmV4YW1wbGUub3JnMIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8A
MIIBCgKCAQEAs0CWL2FjPiXBl61lRfvvE0KzLJmG9LWAC3bcBjgsH6NiVVo2dt6u
Xfzi5bTm7F3K7srfUBYkLO78mraM9qizrHoIeyofrV/n+pZZJauQsPjCPxMEJnRo
D8Z4KpWKX0LyDu1SputoI4nlQ/htEhtiQnuoBfNZxF7WxcxGwEsZuS1KcXIkHl5V
RJOreKFHTaXcB1qcZ/QRaBIv0yhxvK1yBTwWddT4cli6GfHcCe3xGMaSL328Fgs3
jYrvG29PueB6VJi/tbbPu6qTfwp/H1brqdjh29U52Bhb0fJkM9DWxCP/Cattcc7a
z8EXnCO+LK8vkhw/kAiJWPKx4RBvgy73nwIDAQABo4ICUDCCAkwwHwYDVR0jBBgw
FoAUUWj/kK8CB3U8zNllZGKiErhZcjswHQYDVR0OBBYEFKZPYB4fLdHn8SOgKpUW
5Oia6m5IMIGBBgNVHREEejB4gg93d3cuZXhhbXBsZS5vcmeCC2V4YW1wbGUuY29t
ggtleGFtcGxlLmVkdYILZXhhbXBsZS5uZXSCC2V4YW1wbGUub3Jngg93d3cuZXhh
bXBsZS5jb22CD3d3dy5leGFtcGxlLmVkdYIPd3d3LmV4YW1wbGUubmV0MA4GA1Ud
DwEB/wQEAwIFoDAdBgNVHSUEFjAUBggrBgEFBQcDAQYIKwYBBQUHAwIwdQYDVR0f
BG4wbDA0oDKgMIYuaHR0cDovL2NybDMuZGlnaWNlcnQuY29tL3NoYTItaGEtc2Vy
dmVyLWc0LmNybDA0oDKgMIYuaHR0cDovL2NybDQuZGlnaWNlcnQuY29tL3NoYTIt
aGEtc2VydmVyLWc0LmNybDBMBgNVHSAERTBDMDcGCWCGSAGG/WwBATAqMCgGCCsG
AQUFBwIBFhxodHRwczovL3d3dy5kaWdpY2VydC5jb20vQ1BTMAgGBmeBDAECAjCB
gwYIKwYBBQUHAQEEdzB1MCQGCCsGAQUFBzABhhhodHRwOi8vb2NzcC5kaWdpY2Vy
dC5jb20wTQYIKwYBBQUHMAKGQWh0dHA6Ly9jYWNlcnRzLmRpZ2ljZXJ0LmNvbS9E
aWdpQ2VydFNIQTJIaWdoQXNzdXJhbmNlU2VydmVyQ0EuY3J0MAwGA1UdEwEB/wQC
MAAwDQYJKoZIhvcNAQELBQADggEBAISomhGn2L0LJn5SJHuyVZ3qMIlRCIdvqe0Q
6ls+C8ctRwRO3UU3x8q8OH+2ahxlQmpzdC5al4XQzJLiLjiJ2Q1p+hub8MFiMmVP
PZjb2tZm2ipWVuMRM+zgpRVM6nVJ9F3vFfUSHOb4/JsEIUvPY+d8/Krc+kPQwLvy
ieqRbcuFjmqfyPmUv1U9QoI4TQikpw7TZU0zYZANP4C/gj4Ry48/znmUaRvy2kvI
l7gRQ21qJTK5suoiYoYNo3J9T+pXPGU7Lydz/HwW+w0DpArtAaukI8aNX4ohFUKS
wDSiIIWIWJiJGbEeIO0TIFwEVWTOnbNl/faPXpk5IRXicapqiII=
-----END CERTIFICATE-----
```

However, this will also print the certificate data. To avoid that, add `-noout` option.

> this option prevents output of the encoded version of the request.[^3]

In fact, this option should be used in combination with others when displaying certificate information.

```sh
$ cat /tmp/example.com | openssl x509 -noout -enddate
notAfter=Nov 28 12:00:00 2018 GMT
```

Putting everything together:

```sh
$ echo | openssl s_client -connect example.com:443 2> /dev/null | \
  openssl x509 -noout -enddate
notAfter=Nov 28 12:00:00 2018 GMT
```

Let's take it a step further. Strip others and leave just the date:

```sh
$ echo | openssl s_client -connect example.com:443 2> /dev/null | \
  openssl x509 -noout -enddate | \
  cut -d = -f 2
Nov 28 12:00:00 2018 GMT
```

Or:

```sh
$ echo example.com | \
  xargs -I {} openssl s_client -connect {}:443 2> /dev/null |\
  openssl x509 -noout -enddate | \
  cut -d = -f 2
Nov 28 12:00:00 2018 GMT
```

Handling multiple domains?

```sh
$ echo -e 'example.com\nletsencrypt.org'          | \
  xargs -n 1 bash -c '                              \
    openssl s_client -connect $0:443 2> /dev/null | \
    openssl x509 -noout -enddate                  | \
    cut -d = -f 2                                 | \
    xargs -I {} echo {} $0                          \
  '
Nov 28 12:00:00 2018 GMT example.com
Feb  2 21:24:51 2018 GMT letsencrypt.org
```

Verbosely:

```sh
$ echo -e 'example.com\nletsencrypt.org'          | \
  xargs --verbose -n 1 bash -c '                    \
    openssl s_client -connect $0:443 2> /dev/null | \
    openssl x509 -noout -enddate                  | \
    cut -d = -f 2                                 | \
    xargs -I {} echo {} $0                          \
  '
bash -c                     \
    openssl s_client -connect $0:443 2> /dev/null | \
    openssl x509 -noout -enddate                  | \
    cut -d = -f 2                                 | \
    xargs -I {} echo {} $0                          \
   example.com
Nov 28 12:00:00 2018 GMT example.com
bash -c                     \
    openssl s_client -connect $0:443 2> /dev/null | \
    openssl x509 -noout -enddate                  | \
    cut -d = -f 2                                 | \
    xargs -I {} echo {} $0                          \
   letsencrypt.org
Feb  2 21:24:51 2018 GMT letsencrypt.org
```

The entire command chain inside the sub shell was executed for every domain. Because it's not simple to use `openssl x509` command to handle multiple session documents generated from the output of `openssl s_client`. Therefore, for each domain, we run the entire retrieval and extraction steps under a sub shell. Added a little formatting with `xargs` and `echo`.

☺

References:

- [HowTo : Check SSL Certificate Expiration Date from the Linux Shell](http://www.shellhacks.com/en/HowTo-Check-SSL-Certificate-Expiration-Date-from-the-Linux-Shell)


[^1]: `$ man openssl`
[^2]: `$ man s_client`
[^3]: `$ man x509`
