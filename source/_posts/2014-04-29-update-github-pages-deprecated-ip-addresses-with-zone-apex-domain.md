title: Update GitHub Pages Deprecated IP Addresses with Zone Apex Domain
date: 2014-04-29 12:26:48
tags: [ip-address, custom-domain, domain, dns, zone-apex, github-pages, github]
---

Recently, I have received a warning email from GitHub:

> GitHub Pages recently underwent some improvements (https://github.com/blog/1715-faster-more-awesome-github-pages) to make your site faster and more awesome, but we've noticed that realguess.net isn't properly configured to take advantage of these new features. While your site will continue to work just fine, updating your domain's configuration offers some additional speed and performance benefits. Instructions on updating your site's IP address can be found at https://help.github.com/articles/setting-up-a-custom-domain-with-github-pages#step-2-configure-dns-records, and of course, you can always get in touch with a human at support@github.com. For the more technical minded folks who want to skip the help docs: your site's DNS records are pointed to a deprecated IP address.

What are the improvements on [GitHub Pages]? Here are the two major improvements [[1]]:

- Pages are served via CDN (Content Delivery Network)
- DoS (Denial of Service) protection

But my site wasn't properly configured to take advantage of speed and performance due to that the "DNS records are pointed to a deprecated IP address".

_What are the IP addresses that GitHub Pages uses before but have been deprecated now?_

The domain `realguess.net` is an custom zone apex domain (also called bare, naked, or root domain). The domain `blog.realguess.net` is not a zone apex domain, but a subdomain. "A custom subdomain will not be affected by changes in the underlying IP addresses of GitHub's servers." [[2]] But, I am not using a subdomain. I have configured the zone apex domain to point to currently deprecated IP addresses. "If you are using an A record that points to 207.97.227.245 or 204.232.175.78, you will need to update your DNS settings, as we no longer serve Pages directly from those servers." [[3]] So, these IP addresses are deprecated, and need to update the current DNS from:
```
$ dig realguess.net +nostats +nocomments +nocmd
;realguess.net.                 IN      A
realguess.net.          86400   IN      A       204.232.175.78
```

to the new ones pointed by `username.github.io`:
```
$ dig realguess.github.io +nostats +nocomments +nocmd
;realguess.github.io.           IN      A
realguess.github.io.    3600    IN      CNAME   github.map.fastly.net.
github.map.fastly.net.  28      IN      A       199.27.73.133
```

And the new IP address are:

    192.30.252.153
    192.30.252.154

Using a subdomain is a better solution, so I don't need to care about the changing GitHub Pages IP addresses in the future. But I don't think the frequency of IP address updating is going to be very often. So, I will stick with my zone apex domain.

1. [Faster, More Awesome GitHub Pages][1]
2. [Setting up a custom domain with GitHub Pages][2]
3. [My custom domain isn't working][3]

[1]: https://github.com/blog/1715-faster-more-awesome-github-pages
[2]: https://help.github.com/articles/setting-up-a-custom-domain-with-github-pages
[3]: https://help.github.com/articles/my-custom-domain-isn-t-working
[github pages]: https://pages.github.com/
