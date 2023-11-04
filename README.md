# *Conteg*
A Content-Negotiation + Cache-Control Class for PHP-produced HTTP web-output

Web Servers such as Apache by default will auto-negotiate web-page delivery when getting a Request for static files. All of that is lost if you stream web-pages from a database (the classic case here being a web Forum). If you use PHP you have to manage the entire process of Cache-Control and Content-Negotiation yourself, which is more than a bit daunting.

Conteg is a tool-set to make it possible to conduct Content-Negotiation + Cache-Control for web-page delivery. Once implemented, most pages will be delivered both more quickly and consume less bandwidth. The *Conteg* include file is a single PHP class. It attempts to make the entire span of HTTP/1.1 Content-Negotiation & Cache-Control available for use. Some Negotiation is auto-conducted internally, but most is external, and is operated by values provided within the single setup parameter.

## *History & Background*
See [background.md](https://github.com/alexkemp9/Conteg/blob/main/background.md) for background to the development of the HTTP protocol that is relevant to *Conteg*. The most important was the emergence of HTTP/1.0, formalised in May 1996 (see [rfc-1945 (pdf)](https://github.com/alexkemp9/Conteg/blob/main/RFC/rfc-1945_HTTP-1.0.pdf)).

Sir Tim Berners-Lee had written the software for pretty much all of what became the World Wide Web in the 1980s. He packaged together all of the software & documentation as a practical implementation whole and made it available FoC, with HTTP/0.9. His initiative was enthusiastically taken up by most of his peers, but that highlighted the deficiencies & quickly led to HTTP/1.0. This second version allowed interaction between *Client* & *Server* which could lead to considerable reductions in bandwidth + thus speed of delivery. The mechanism to get to that was the introduction of *Headers* (see *“4. HTTP Message”* on p20 of the rfc-1945 pdf), and thus a split between the *Entity* that constitutes the web-body of a data transfer, and the *Header* that constitutes the metadata of that transfer.

This introduction of *“headers”* & *“bodies”* allows content negotiation between client & server. Introduced first at HTTP/1.0 it was basic but already remarkably complete. Caching is introduced as an important feature, and this is relevant both for Clients *and* proxy-servers. The fundamental feature of this is a method for cooperation between client, server & proxies to allow substantial reduction in network bandwidth & subsequent increase in speed. My own testing showed typical values of +70% at level 8/9 (reduction to one-third of original size), with some pages better than 80% (reduction to one-fifth). On admin-edit pages (masses of duplicated &lt;select> drop-down boxes) reductions exceed 90%. So, most effective.

These are the relevant new features introduced in the HTTP/1.0 spec:

| Name             | RFC-1945         | PDF Page         | Info             |
|:-----------------|:-----------------|:-----------------|:-----------------|
URI | 3.2 Uniform Resource Identifiers | 13 | 
URL | 3.2.2 http URL | 14 | http_URL= "http:" "//" host [ ":" port ] [ abs_path ]
Compression | 3.5 Content Codings | 17 | ***Content Negotiation***: <br /> content-coding = "gzip" \| "compress"
Methods | 5.1.1 Method | 23 | Method = "GET" \| "HEAD" \| "POST"
Headers | 5.2 Request Header Fields | 24 | ***Content Negotiation***:<br />Request-Header = Authorization \| From \| If-Modified-Since \| Referer \| User-Agent
Status | 6.1 Status-Line | 25 | eg first line is *“HTTP/1.0 200 ”*; This allows the client to differentiate a http/0 response from a http/1 response.
Status Codes | 6.1.1 Status Code and Reason Phrase | 26 | 15 codes were introduced: Status-Code =<br />"200"; OK<br />"201"; Created<br />"202"; Accepted<br />"204"; No Content<br />"301"; Moved Permanently<br />"302"; Moved Temporarily<br />"304"; Not Modified<br />"400"; Bad Request<br />"401"; Unauthorized<br />"403"; Forbidden<br />"404"; Not Found<br />"500"; Internal Server Error<br />"501"; Not Implemented<br />"502"; Bad Gateway<br />"503"; Service Unavailable
Response Headers | 6.2 Response Header Fields | 27 | ***Content Negotiation***:<br />Response-Header = Location \| Server \| WWW-Authenticate
Entity Headers | 7.1 Entity Header Fields | 28 | 7 types of Entity Header were introduced: Entity-Header =<br />Allow \|<br />Content-Encoding \|<br />Content-Length \|<br />Content-Type \|<br />Expires \|<br />Last-Modified \|<br />extension-header
Expires | 10.7 Expires | 39 | ***Content Negotiation***: affects caching
If-Modified-Since | 10.9 If-Modified-Since | 41 | ***Content Negotiation***:<br />server returns 304 (not modified) if resource unchanged
Last-Modified | 10.10 Last-Modified | 42 | ***Content Negotiation***:<br />eg *“Last-Modified: Tue, 15 Nov 1994 12:45:26 GMT”*
Location | 10.11 Location | 42 | ***Content Negotiation***: (accompanied by 301 \| 302)<br />A directive to client to re-issue request (absolute URL)<br />eg *“Location: http ://www .w3.org/hypertext/WWW/NewLocation.html”*
Pragma | 10.12 Pragma | 42 | ***Content Negotiation***: (curiously, still in use although deprecated)<br />Client telling proxies not to use any cached entity:<br />eg *“Pragma: no-cache”*
Referer (sic) | 10.13 Referer | 43 | 
