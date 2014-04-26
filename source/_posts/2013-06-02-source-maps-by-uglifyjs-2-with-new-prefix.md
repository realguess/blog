title: Source Maps by UglifyJS 2 with New Prefix
date: 2013-06-02 23:53:52
tags: [source-maps, uglifyjs, chrome]
---

Living on the bleeding edge means more to learn. I was learning about creating source maps by using [UglifyJS][]. For some reason, the uncompressed JavaScript source codes were not able to load. After some `diff` with a working version from [Source Maps 101][nettus], the problem is just resulting of one character difference. With newly updated [UglifyJS][] v2.3.6, [SourceMapping pragma has changed to //#][change]:

    //# sourceMappingURL=script.uglify.js.map


but my Chrome browser lags behind. It recognizes source maps as `//@`:

    //@ sourceMappingURL=script.uglify.js.map

I did not use the latest or dev build, the current Chrome browser I am using is yet to support `//#`. What I really need is [Chrome Canary][]. Unfortunately, it is not available for Linux :(.

[uglifyjs]: https://github.com/mishoo/UglifyJS2
[chrome canary]: http://www.paulirish.com/2012/chrome-canary-for-developers/
[nettus]: http://net.tutsplus.com/tutorials/tools-and-tips/source-maps-101/
[change]: https://github.com/mishoo/UglifyJS2/commit/1dbffd48ea49ee278454d6b1c632b7ba4bbf1868
