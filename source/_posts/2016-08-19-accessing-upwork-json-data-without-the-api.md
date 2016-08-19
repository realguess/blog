title: Accessing Upwork JSON Data without the API
date: 2016-08-18 12:00:00
categories: [Data Science]
tags: [data-retrieval, upwork, rule-of-diversity]
---

[Upwork], formerly Elance-oDesk, is the world's largest freelancing marketplace. I'm interested to know what types of jobs they are in the platform, and how many. For a lazy programmer, browsing each job category and clicking on each link, and copying those numbers is not the way to go. I need to automate this. There is an [API]. But before diving into the API documentation, let's see if there is another way ("Rule of Diversity").

Before continuing, a word of warning, **this is prohibited**:

> Using any robot, spider, scraper, or other automated means to access the Site for any purpose without our express written permission or collecting or harvesting any personally identifiable information, including Account names, from the Site;[^2]

After poking around the web app, it communicates with its backend by using [JSON] data exchange format via the URL: <https://www.upwork.com/o/jobs/browse/url>. However, if accessing the URL directly, it will respond with 404 page not exist error. Something is missing.

Well, the web app is able to successfully make the request, so this is not difficult to tackle. Just use the process of elimination from the working request, it will reveal the required information.

After a couple tries, just need to add the request header: `X-Requested-With: XMLHttpRequest`, then the JSON response with the status code `200` will be returned:

```
$ http --verbose https://www.upwork.com/o/jobs/browse/url \
  X-Requested-With:XMLHttpRequest
GET /o/jobs/browse/url HTTP/1.1
Accept: */*
Accept-Encoding: gzip, deflate
Connection: keep-alive
Host: www.upwork.com
User-Agent: HTTPie/0.9.6
X-Requested-With: XMLHttpRequest
```

The default sort is by creation time in descending order, so you don't need to add the query parameters: `sort==create_time+desc` ([HTTPie]).

Let's load the response data into Node.js and perform a quick analysis:

```
$ node
> data = require('./upwork.json')
{ url: '/o/jobs/browse/',
  searchResults:
   { q: '',
     paging: { total: 87654, offset: 0 },
     spellcheck: { corrected_queries: [] },
     jobs:
      [ [Object],
        [Object],
        [Object],
        [Object],
        [Object],
        [Object],
        [Object],
        [Object],
        [Object],
        [Object] ],
     smartSearch: { downloadTeamApplication: false },
     facets:
      { jobType: [Object],
        workload: [Object],
        duration: [Object],
        clientHires: [Object],
        contractorTier: [Object],
        categories: [Object],
        previousClients: [Object],
        subcategories: [] },
     isSearchWithEmptyParams: true,
     subcategories: [],
     currentQuery: {},
     rssLink: '/ab/feed/jobs/rss?api_params=1&q=',
     atomLink: '/ab/feed/jobs/atom?api_params=1&q=',
     queryParsedParams: [],
     pageTitle: 'Freelance Jobs - Upwork' } }
```

The property `searchResults.paging.total` is the total number of jobs available:

```
> data.searchResults.paging
{ total: 87654, offset: 0 }
```

But, the number is different from the web app, a lot less, 50% less jobs found. Is that because the request is not recognized as a logged-in user? Let's find out.

<!-- more -->

Request header `Cookie` field is most likely to be the candidate for this type of data. As it turns out, just log in from the web and grab the session ID, then insert that into the header field `Cookie`, now you can get all the jobs:

```
$ http https://www.upwork.com/o/jobs/browse/url \
  X-Requested-With:XMLHttpRequest \
  Cookie:session_id=5fdb83821afb076fcbeb6ece829d937c
```

Make sure to replace the session ID with the correct one. The ID (`5fdb83821afb076fcbeb6ece829d937c`[^1]) appears to be a MD5 hash.

To obtain number of jobs for each category, print the `searchResults.facets.categories` property:

```
$ http https://www.upwork.com/o/jobs/browse/url \
  X-Requested-With:XMLHttpRequest \
  Cookie:session_id=5fdb83821afb076fcbeb6ece829d937c \
  | jq .searchResults.facets.categories
{
  "web-mobile-software-dev": 26542,
  "design-creative": 14138,
  "writing": 10623,
  "sales-marketing": 9662,
  "admin-support": 6076,
  "translation": 3222,
  "it-networking": 3118,
  "engineering-architecture": 2466,
  "data-science-analytics": 1795,
  "accounting-consulting": 1564,
  "customer-service": 1135,
  "legal": 718
}
```

Now, just grab your own session ID, you have something to start with. But don't try it too often, it's not the allowed method of use[^2].

Settings:

```
$ http --version
0.9.6
$ jq --version
jq-1.5
```


[^1]: `$ echo -n Upwork | md5sum`
[^2]: [Terms of Use](https://www.upwork.com/legal/terms-of-use/), Upwork, Effective Date: August 8, 2016
[Upwork]: https://www.upwork.com/
[API]: https://developers.upwork.com/
[JSON]: http://json.org/
[HTTPie]: https://github.com/jkbrzt/httpie
