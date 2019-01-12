title: Using Two Different Phones for 2FA
date: 2019-01-12T12:00:00
categories: [Security]
tags: [2fa, authentication, otp, totp, password]
---

When setting up two-factor authentication (2FA), there is always an option to print backup codes in case you lose your phone. But if you have a spare phone, you can use it as the backup authenticator device.

During the 2FA setup process, scan the QR code on two different phones. Both phones will show the identical codes. This is because of time-based OTP (One-Time Password) implementation. Time-based OTP establishes authentication by an Unix time to start counting and an interval for computing the next set of codes (usually 30 seconds).

In fact, you can use three, four, or as many devices you want. When done, set a strong password for the phone, turn it off, store somewhere safe. It's better than a piece of paper, because when your safe was broken into, there's another layer of protection.

BTW, when Google asks you "What kind of phone do you have?", you can pick Android and still use the authenticator app from the iPhone.

![Get codes from the Authenticator app](/2019/01/12/using-two-different-phones-for-2fa/google-2fa-authenticator-selection.png)

Therefore, 2FA, two different phones, two locations, no LastPass, no Authy, no cloud based backup.
