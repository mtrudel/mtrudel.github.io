---
layout: post
title: Smart Keywords are Smart
githubproject: mtrudel/Smart-Keywords
---

Safari is a great browser, but has always had one feature missing that's present in every other modern browser: [Smart Keywords](http://support.mozilla.com/en-US/kb/Smart%20keywords). Previously I've used tools such as [Sogudi](http://www.kitzkikz.com/Sogudi) or [Keywurl](http://alexstaubo.github.com/keywurl/) to provide that functionality within Safari, but they were both implemented via a fragile (if awesome) [hack](http://www.culater.net/software/SIMBL/SIMBL.php), and as a consequence I always pointed fingers at them whenever Safari started losing its shit in the past. Happily, with the arrival of Safari 5.1, Apple has added the requisite [event hooks](http://developer.apple.com/technologies/safari/whats-new.html) to the extension API to enable Smart Keyword functionality as a first class extension citizen.

[Smart Keywords](https://github.com/mtrudel/Smart-Keywords) is my implementation of Smart Keywords for Safari, using these new APIs. It's super simple (28 lines of Javascript, the majority of which is code to read & write configuration data), easily configurable, and &ndash; owing to its simplicity &ndash; should be crashproof.

To install Smart Keywords, download the [extension](https://github.com/downloads/mtrudel/Smart-Keywords/SmartKeywords.safariextz) and double click on it to install. Configuration is straightforward, and is done in the extension tab of Safari's preferences. Due to the way in which extension configurations are defined, you can define a maximum of ten keywords (this is a soft limit. If you want more, either ask me, or else [fork the project](https://github.com/mtrudel/Smart-Keywords/) and add them yourself). 

To customize a keyword, enter in the keyword you want to match (say, for example 'wiki' to match searches of the form 'wiki bananarama'). When you enter this keyword in the address bar, it will be replaced with the contents of the matching URL instead, with the remainder of your query being substituted in for `%s` in the URL. As an example, if you have the keyword 'foo' defined with a URL of `http://example.com/search?query=%s`, then entering 'foo bananarama' will take you to the URL `http://example.com/search?query=bananarama`. Note that spaces in your entered URL are automatically URL escaped to `%20`, which is usually what you want.

Comments / feedback are welcome. If you find any bugs or missing features, please [let me know](https://github.com/mtrudel/Smart-Keywords/issues)!
