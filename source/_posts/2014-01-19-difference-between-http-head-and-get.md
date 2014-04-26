title: Difference between HTTP HEAD and GET
date: 2014-01-19 23:05:04
tags: [http, head, get]
---

> The HEAD method is identical to GET except that the server MUST NOT return a message-body in the response. The metainformation contained in the HTTP headers in response to a HEAD request SHOULD be identical to the information sent in response to a GET request. - [Method Definitions][method]

I was a little bit confused when I encoutered the HTTP status code `204`, which states that the response MUST NOT include a message body. The `HEAD` method contains no body content as well. Should I use `204` instead of `200`?

The answer is **NO**. `GET` method should always return HTTP status code of 200. Since `HEAD` method is idential to `GET`, it should return `200` as well. Just keep in mind that the cost of processing both `HEAD` and `GET` requests are almost the same.

[method]: http://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html
