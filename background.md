# *Conteg*
A Content-Negotiation + Cache-Control Class for PHP-produced HTTP web-output

## *HTTP Background*
This document is intended to provide background on the HTTP protocol as it relates to the use of Conteg. For that reason it emphasises the introduction of Content Negotiation & Caching within the different HTTP versions.

## *History*
- HTTP/0.9 :: 1989 (begun by TimBL) (zero content negotiation nor cache-control)
- HTTP/1.0 :: 1996 (see [rfc-1945 (pdf)](https://github.com/alexkemp9/Conteg/blob/main/RFC/rfc-1945_HTTP-1.0.pdf))    
(Accept-Encoding + Cached responses: 304 response)
- HTTP/1.1 :: 1997 (Updated 1999, 2014, and 2022) (see [rfc-2616 (pdf)](https://github.com/alexkemp9/Conteg/blob/main/RFC/rfc-2616_HTTP-1.1.pdf) + [rfc-7232 (pdf)](https://github.com/alexkemp9/Conteg/blob/main/RFC/rfc-7232_HTTP-1.1.pdf))    
(full content negotiation + cache-control responses available)

## *Informative Background*
Pretty much all of the WWW (World Wide Web), HTTP & Web Browsers was initiated, implemented & provided as a complete package FoC by Sir Tim Berners-Lee during the 1980s whilst employed at CERN. Like so many things, it started extremely simple and then increasingly became more complex across the following decades.
 
HTTP is a protocol universally agreed upon & used for communicating across the WWW. These words are written in 2023 & the idea of computer networking is now a commonplace affair for consumers as much as it has always been for Business, Government and so on. That was not the case in 1980.

The WWW is a network of networks. The premise, broadly, is that electronic servers are embedded within the WWW, and electronic clients are able to interact with those servers. That does include aspects of remote setup, service, etc., but we are going to concentrate upon the aspect of content delivery from Server to Client. The fundamental premise is that Clients make electronic *Requests* and Servers give electronic *Responses*. Wikipedia lists [9 current Request methods](https://en.wikipedia.org/wiki/HTTP#Request_methods) and the only one available in HTTP/1.0 was *“GET”*, which translates simply as *“send me this file”*. HTTP/1.1 has three (*“GET”*, *“HEAD”* & *“POST”*).

***HTTP/0.9*** specified only a simple one-line *“GET”* from Client to Server. The specification was listed on a single sheet of paper, and no RFC (Request for Comments) was ever produced. If you look closely in the HTTP/1.0 spec (next) you will find the HTTP/0.9 spec. After it was up & running, and proving both popular, effective & useful, work began immediately on the next iteration.

***HTTP/1.0*** was a formal specification within [RFC-1945 (pdf)](https://github.com/alexkemp9/Conteg/blob/main/RFC/rfc-1945_HTTP-1.0.pdf). From our perspective, the important aspect of http/1.0 over http/0.9 is the introduction of Web-Headers (header fields, also known as *“headers”*) & Web-Bodies (an entity body) (see *“4.HTTP Message”* on p20 of the pdf).

The introduction of *“headers”* & *“bodies”* allows the introduction of content negotiation, even though at this early beginning it is most basic. Caching is introduced as an important feature, and this is relevant both for Clients *and* proxy-servers. The fundamental feature of this is a method for cooperation between client, server & proxies to allow substantial reduction in network bandwidth & subsequent increase in speed. My own testing showed typical values of +70% at level 8/9 (reduction to one-third of original size), with some pages better than 80% (reduction to one-fifth). On admin-edit pages (masses of duplicated &lt;select> drop-down boxes) reductions exceed 90%. So, most effective.

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
Pragma | 10.12 Pragma | 42 | ***Content Negotiation***: (curiously, still in use although deprecated)<br />eg Client telling proxies not to use any cached entity:<br />eg *“Pragma: no-cache”*
Referer (sic) | 10.13 Referer | 43 | 