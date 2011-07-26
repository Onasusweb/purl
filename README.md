jQuery URL Parser plugin (No jQuery version!)
======================

A plain JavaScript branch of my [jQuery URL parser](https://github.com/allmarkedup/jQuery-URL-Parser). It parses urls and provide easy access to their attributes (such as the protocol, host, port etc), path segments, querystring parameters, fragment parameters and more.

The core parser functionality is based on the [Regex URI parser by Steven Levithan](http://blog.stevenlevithan.com/archives/parseuri).

This has all the functionality of the jQuery version **apart from** the ability to retrieve and work with the URL of an specific element specified by a CSS selector. If you need that then use the jQuery version!

**License:** http://unlicense.org/ - i.e. do what you want with it :-)

Specifying the URL to parse
---------------------------

There are a few different ways to choose what URL to parse:

``` javascript
var url = purl(); // parse the current page URL
var url = purl('http://allmarkedup.com'); // pass in a URI as a string and parse that 
```

URL attributes
--------------

The `.attr()` method is used to return information on various parts of the URL. For example:

``` javascript
var url = purl('http://allmarkedup.com/folder/dir/index.html?item=value');
url.attr('protocol'); // returns 'http'
url.attr('path'); // returns '/folder/dir/index.html'
```

The attributes available for querying are:

* **source** - the whole url being parsed
* **protocol** - eg. http, https, file, etc
* **host** - eg. www.mydomain.com, localhost etc
* **port** - eg. 80
* **relative** - the relative path to the file including the querystring (eg. /folder/dir/index.html?item=value)
* **path** - the path to the file (eg. /folder/dir/index.html)
* **directory** - the directory part of the path (eg. /folder/dir/)
* **file** - the basename of the file eg. index.html
* **query** - the entire querystring if it exists, eg. item=value&item2=value2
* **fragment** (also available as **anchor**) - the entire string after the # symbol 

There are also a few more obscure ones available too if you want to dig about a bit ;-)

If you don't specify an attribute then this method will return an object literal with all the available attribute key:value pairs in it.

Query string parameters
-----------------------

The `.param()` method is used to return the values of querystring parameters.

Pass in a string to access that parameter's value:

``` javascript
purl('http://allmarkedup.com?sky=blue&grass=green').param('sky'); // returns 'blue'
```

If no argument is passed in it will return an object literal containing a key:value map of all the querystring parameters.

``` javascript
purl('http://allmarkedup.com?sky=blue&grass=green').param(); // returns { 'sky':'blue', 'grass':'green' }
```

Note that the `.param()` method will work on both ampersand-split and semicolon-split querystrings.

URL segments
-----------------------

The `.segment()` method is used to return values of individual segments from the URL's path.

Pass in an integer value to get the value of that segment - note however that the count is *not* zero-indexed like an array - i.e. `.segment(1)` returns the *first* segment, not the second one.

You can also pass in negative values, in which case it will count back from the end of the path rather than forwards from the start.

``` javascript
var url = purl('http://allmarkedup.com/folder/dir/example/index.html');
url.segment(1); // returns 'folder'
url.segment(-2); // returns 'example'
```
If no argument is passed in it will return an array of all the segments (which will be zero-indexed!).

``` javascript
purl('http://allmarkedup.com/folder/dir/example/index.html').segment(); // returns ['folder','dir','example','index.html']
```

Fragment parameters and/or segments
-------------------------------

Some sites and apps also use the hash fragment to store querystring-style key value pairs (eg. `http://test.com/#sky=blue&grass=green`), or slash-delimited paths (eg. `http://test.com/#/about/us/`).

There are two methods available for extracting information from fragments of these types - `.fparam()` and `.fsegment()`, both of which behave indentically to their `.param()` and `.segment()` counterparts but act on the fragment rather than the main URL.

``` javascript
purl('http://test.com/#sky=blue&grass=green').fparam('grass'); // returns 'green'
purl('http://test.com/#/about/us/').fsegment(1); // returns 'about'
```

Enabling strict mode
--------------------

Internally this plugin uses Steven Levithan's excellent Regex URI parser, which has two modes - loose and strict. This plugin uses the loose mode by default (i.e. strict mode set to `false`), which deviates slightly from the specs but produces more intuitive results. If for some reason you prefer to use the strict parser and so be fully spec-compatible, then you can enable this when calling the plugin as follows:

``` javascript
var url = purl(true); // parse the current page URL in strict mode
var url = purl('http://allmarkedup.com',true); // pass in a URI as a string and parse that in strict mode
```

A note on improperly encoded URLs
---------------------------------

If you attempt to use this plugin to parse a URL that has an invalid character encoding in it, it will throw a `URIError` Exception. This will happen if the URL has a percentage sign followed by either a non-numeric character or a numeric value of greater than 80 (i.e. 128 in decimal).

If there is a chance you may end up parsing a badly encoded URL you should probably wrap your calls to this plugin in a try/catch block to prevent this causing unforseen problems.

Thanks to [steve78b](https://github.com/steve78b) for pointing this out.