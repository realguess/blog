title: Obtain Username from Custom Domain in Tumblr
date: 2014-04-23 19:05:44
tags: [username, tumblr, custom-domain]
---

Users frequently have custom domain for their blogs in Tumblr. If you need to know the [Tumblr] username from the custom domain, just need to grab the value from the header field `X-Tumblr-User`:

    $ curl -Is blog.foursquare.com | grep -i x-tumblr-user
    X-Tumblr-User: foursquare

Another example:

    $ curl -Is blog.birchbox.com | grep -i x-tumblr-user
    X-Tumblr-User: birchbox

Now you can use the username to make some API calls to Tumblr.
