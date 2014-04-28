title: Understand Google Trends Search Term and Topic
date: 2014-04-27 16:41:01
tags: [search-term, topic, google-trends, search, semantics]
---

"[Google Trends] is a public web facility of Google Inc., based on Google Search, that shows how often a particular search-term is entered relative to the total search-volume across various regions of the world, and in various languages." - [Google Trends - Wikipedia]

How you type your search term in Google Trends will determine the results you will see. As described in Google Trends [help page], there are four different ways:

1. A B
2. "A B"
3. A + B
4. A - B

I am going to illustrate a few examples to explain the difference with the following fixed parameters:

- Location: _United States_
- Time range: _2013_
- Categories: _All categories_
- Type: _Web Search_


## A B

Search term format `A B` is the most common format:

1. Must contains both words
2. Words can be any order
3. Other words can be included, such as `A B C`
4. "No misspellings, spelling variations, synonyms, plural or singular versions" - [Google Trends Help][help page]

Let's start by comparing three search terms ['tennis shoes', 'tennis' and 'shoes']:
```
Search term  | Avg
------------------
tennis shoes | 1
tennis       | 20
shoes        | 89
```

This shows the first point above, that both words much be in the search term. Otherwise, the difference should not be this large.

The order of the search term matters a little. This can be done by simply comparing ['tennis shoes' and 'shoes tennis']:

```
Search term  | Avg
------------------
tennis shoes | 73
shoes tennis | 73
```

Another example is ['Barack Obama' vs. 'Obama Barack']:
```
Search term  | Avg
------------------
Barack Obama | 26
Obama Barack | 26
```

The third point can be demonstrated with the conjunction of the format `"A B"` (to be shown later).

The fourth point is actually the most important, because variation matters. For example, comparing ['tennis shoes', 'tennis shoe', and 'tenis shoes']:
```
Search term  | Avg
------------------
tennis shoes | 74
tennis shoe  | 11
tenis shoes  |  2
```

Or, ['ugg' and 'uggs']:
```
Search term  | Avg
------------------
ugg          | 24
uggs         | 25
```

Here, when people search either 'ugg' or 'uggs' on Google, most likely, they mean the same thing. So, the actual trend should include both search terms.

['tennis shoes', 'tennis' and 'shoes']: http://www.google.com/trends/explore#q=tennis%20shoes%2C%20tennis%2C%20shoes&geo=US&date=1%2F2013%2012m&cmpt=q
['tennis shoes' and 'shoes tennis']: http://www.google.com/trends/explore#q=tennis%20shoes%2C%20shoes%20tennis&geo=US&date=1%2F2013%2012m&cmpt=q
['Barack Obama' vs. 'Obama Barack']: http://www.google.com/trends/explore#q=Barack%20Obama%2C%20Obama%20Barack&geo=US&date=1%2F2013%2012m&cmpt=q
['tennis shoes', 'tennis shoe', and 'tenis shoes']: http://www.google.com/trends/explore#q=tennis%20shoes%2C%20tennis%20shoe%2C%20tenis%20shoes&geo=US&date=1%2F2013%2012m&cmpt=q
['ugg' and 'uggs']: http://www.google.com/trends/explore#q=ugg%2C%20uggs&geo=US&date=1%2F2013%2012m&cmpt=q


## "A B"

The format `"A B"` means exact search:

1. Order is important
2. No additional words included

Here is comparison among three search terms ['"tennis shoes"', '"shoes tennis"' and 'tennis shoes']:
```
Search term    | Avg
--------------------
"tennis shoes" | 69
"shoes tennis" | NA
tennis shoes   | 74
```

There are people who search for ['"shoes tennis"'] exactly, but that the volume is too small when comparing to the other two.

This example also shows that since search term format `A B` allows additional words to be added such as `A B C`. Therefore, its volume is higher than `"A B"` search term format.

['"tennis shoes"', '"shoes tennis"' and 'tennis shoes']: http://www.google.com/trends/explore#q=%22tennis%20shoes%22%2C%20%22shoes%20tennis%22%2C%20tennis%20shoes&geo=US&date=1%2F2013%2012m&cmpt=q
['"shoes tennis"']: http://www.google.com/trends/explore#q=%22shoes%20tennis%22&geo=US&date=1%2F2013%2012m&cmpt=q


## A + B

Search term format `A + B` is an `OR` operation. For example, compare ['tennis', 'shoes', 'tennis + shoes', and 'shoes + tennis']:
```
Search term    | Avg
--------------------
tennis         | 13
shoes          | 58
tennis + shoes | 71
shoes + tennis | 71
```

The sum of `tennis` and `shoes` is equal to either `tennis + shoes` or `shoes + tennis`.

['tennis', 'shoes', 'tennis + shoes', and 'shoes + tennis']: http://www.google.com/trends/explore#q=tennis%2C%20shoes%2C%20tennis%20%2B%20shoes%2C%20shoes%20%2B%20tennis&geo=US&date=1%2F2013%2012m&cmpt=q


## A - B

`A - B` search term format excludes searches containing `B`. The search words can be `A C`, but not `A B`. For example, ['tennis' and 'tennis - shoes']:
```
Search term    | Avg
--------------------
tennis         | 35
tennis - shoes | 32
```

Search term `tennis` can include `shoes`, therefore, it is higher than `tennis - shoes`, which disallow `shoes`.


['tennis' and 'tennis - shoes']: http://www.google.com/trends/explore#q=tennis%2C%20tennis%20-%20shoes&geo=US&date=1%2F2013%2012m&cmpt=q


## Special Characters

"Currently, search terms that contain special characters, such as an apostrophe, single quotes, and parentheses, will be ignored. For example, if you type `women's tennis world ranking`, you get results for `womens tennis world ranking`." - [Google Trends Help][help page]


## Topics

Without variations such as misspellings, synonyms or special characters, we cannot truly capture the user's online search intention. As explained by the blog post, [An easier way to explore topics and entities in Google Trends], when we search for `rice`, are we looking for `Rice University` or the `rice` we eat? Or how to count all the variations when looking up [Gwyneth Paltrow], such as `Gwen Paltro` or `Lead actress in Iron Man`?

That's why Google Trends introduces _topics_, where the semantics of the search are used, not only variations, but actual meanings. Google Trends topics is still in beta: "Measuring search interest in topics is a beta feature which quickly provides accurate measurements of overall search interest. To measure search interest for a specific query, select the 'search term' option." And "when you measure interest in a search topic (Tokyo - Capital of Japan) our algorithms (Google Trends) count many different search queries that may relate to the same topic (東京, Токио, Tokyyo, Tokkyo, Japan Capital, etc). When you measure interest in a search query (Toyko - Search term), our systems will count only searches including that string of text ("Tokyo")."

Here is an example of the retail company Nordstrom, comparing between [search term and topic][nordstrom] (the dotted line is topic and the solid line is search term):
<script type="text/javascript" src="//www.google.com/trends/embed.js?hl=en-US&q=Nordstrom,+/m/01fc_q&geo=US&date=1/2013+12m&cmpt=q&content=1&cid=TIMESERIES_GRAPH_0&export=5&w=500&h=330"></script>

Here is another example of [Laura Mercier Cosmetics]:
<script type="text/javascript" src="//www.google.com/trends/embed.js?hl=en-US&q=Laura+Mercier+Cosmetics,+/m/0c5_56&geo=US&date=1/2013+12m&cmpt=q&content=1&cid=TIMESERIES_GRAPH_0&export=5&w=500&h=330"></script>

The topic algorithm is likely to count both ['Laura Mercier Cosmetics' and 'Laura Mercier']:
<script type="text/javascript" src="//www.google.com/trends/embed.js?hl=en-US&q=/m/0c5_56,+Laura+Mercier+Cosmetics,+Laura+Mercier&geo=US&date=1/2013+12m&cmpt=q&content=1&cid=TIMESERIES_GRAPH_0&export=5&w=500&h=330"></script>

Another good example is ['Apple' (fruit), 'Apple Inc.' (consumer electronic company) and 'apple' (search term)]:
<script type="text/javascript" src="//www.google.com/trends/embed.js?hl=en-US&q=/m/014j1m,+/m/0k8z,+apple&geo=US&date=1/2013+12m&cmpt=q&content=1&cid=TIMESERIES_GRAPH_0&export=5&w=500&h=330"></script>

Indeed, when a search is `apple`, it means more than just the maker of iPhone and iPad:
```
Search term    | Avg
--------------------
Apple          | 13
Apple Inc.     | 47
apple          | 55
```

We can build our own topic by using the format `A + B` to include all variations, for example, topic [Flip-flops] (Garment) closely resembles the sum of `flip flop` and `flip flops` (see the OR operator):
<script type="text/javascript" src="//www.google.com/trends/embed.js?hl=en-US&q=flip+flop,+flip+flops,+flip+flop+%2B+flip+flops,+/m/025znc&geo=US&date=1/2013+12m&cmpt=q&content=1&cid=TIMESERIES_GRAPH_0&export=5&w=500&h=330"></script>

For some search terms, if we do not use topic or omitting different variations, we might get a wrong impression of the search trend. For example, comparing [flip flops and Bahamas], on the peak, people who search for `flip-flops` is almost the same as people look for `Bahamas`:
<script type="text/javascript" src="//www.google.com/trends/embed.js?hl=en-US&q=flip+flops,+flip+flop,+/m/025znc,+/m/0160w&geo=US&date=1/2013+12m&cmpt=q&content=1&cid=TIMESERIES_GRAPH_0&export=5&w=500&h=330"></script>


[Gwyneth Paltrow]: http://www.google.com/trends/explore#q=%2Fm%2F0bq2g&geo=US&date=1%2F2013%2012m&cmpt=q
[Nordstrom]: http://www.google.com/trends/explore#q=Nordstrom%2C%20%2Fm%2F01fc_q&geo=US&date=1%2F2013%2012m&cmpt=q
[Laura Mercier Cosmetics]: http://www.google.com/trends/explore#q=Laura%20Mercier%20Cosmetics%2C%20%2Fm%2F0c5_56&geo=US&date=1%2F2013%2012m&cmpt=q
['Laura Mercier Cosmetics' and 'Laura Mercier']: http://www.google.com/trends/explore#q=%2Fm%2F0c5_56%2C%20Laura%20Mercier%20Cosmetics%2C%20Laura%20Mercier&geo=US&date=1%2F2013%2012m&cmpt=q
['apple' (fruit), 'Apple Inc.' (consumer electronic company) and 'apple' (search term)]: http://www.google.com/trends/explore#q=%2Fm%2F014j1m%2C%20%2Fm%2F0k8z%2C%20apple&geo=US&date=1%2F2013%2012m&cmpt=q
[flip-flops]: http://www.google.com/trends/explore#q=flip%20flop%2C%20flip%20flops%2C%20flip%20flop%20%2B%20flip%20flops%2C%20%2Fm%2F025znc&geo=US&date=1%2F2013%2012m&cmpt=q
[flip flops and Bahamas]: http://www.google.com/trends/explore#q=flip%20flops%2C%20flip%20flop%2C%20%2Fm%2F025znc%2C%20%2Fm%2F0160w&geo=US&date=1%2F2013%2012m&cmpt=q


## Conclusion

Using _search term_ in Google Trends has its limitation because it does not capture all variations such as misspellings, spelling variations, synonyms, plural or singular version, or special characters. And more importantly, the semantics of the search term is not captured. That's why Google introduces _topic_ to improve the result.

Google Trends has a limited number of topics, for those search terms without a corresponding topics, we need to a taxonomy system to include all the variations in order to capture the true semantics of the search.


[Google Trends]: https://www.google.com/trends/
[Google Trends - Wikipedia]: http://en.wikipedia.org/wiki/Google_Trends
[help page]: https://support.google.com/trends/answer/4359582?hl=en&ref_topic=4365599
[An easier way to explore topics and entities in Google Trends]: http://insidesearch.blogspot.com/2013/12/an-easier-way-to-explore-topics-and.html
