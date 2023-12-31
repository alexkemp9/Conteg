# *Conteg*
A Content-Negotiation + Cache-Control Class for PHP-produced HTTP web-output

## *HTTP Background*
This document is intended to provide background on the HTTP protocol as it relates to the use of Conteg. For that reason it emphasises the introduction of Content Negotiation & Caching within the different HTTP versions.

### *Notes:*
1. There are a large number of PDF-links in this file. Those links go to PDFs stored within the *[RFC Repository](https://github.com/alexkemp9/Conteg/tree/main/RFC)* directory. Unfortunately github cannot display those PDFs (although it is supposed to be able to do so) and after a while will just show an error page. You *can* download the PDfs to your device and they will show fine in your browser, or a dedicated PDF viewer.     
       
rfc-1945 + rfc-2616 PDFs were produced with *LibreOffice 4.7.7.2* (LO) under the *Devuan* OS from the official RFC text, but all others came as a PDF from the official link, and I have zero idea why github cannot display any of them.
2. In addition to Note(1) there is some bug within *LibreOffice* (LO) which prevents many intra-document links from working in the PDF (all links are fully effective within LO, but some not within an exported PDF)†.
3. This document is a work-in-progress (*very* slow to produce).

#### *Update Dec 1:*
† The LO bugs are damn annoying. I finally got them fixed today, sort-of.

I've made scores of documents which were *“`LO .odt =＞ exported as PDF`”*, full of internal links, and without any problems. Until I produced these RFC docs, and discovered late in the day that almost none of the manual intra-doc links within them functioned.

One of these earlier docs was started on May 25 2023, and has been worked on concurrently with the RFC docs; it has been continuously updated & is currently 66 Pages. It uses the identical structure & styles as the RFC docs. The May 25 doc has never shown problems with those links, even though both sets (May 25 + RFC docs) are produced on the same desktop computer.

The intra-doc linking method within all my *LibreOffice* documents previously used *Heading* styles as the target for internal document links; that statement is true both for the auto-produced *Table of Contents* and manual links accessed via *`Ctrl+K`* (the LO terminology for this is *Hyperlinks*, and the command/menu leads to links that will create Internet, Mail or Document targets). There are up to 6 *Heading* styles (labelled *“Heading 1”* to *“Heading 6”*). *Heading 1* is used just once for the page Title, and *Heading 6* is almost never used. I've never before discovered any problems with any links within any other document than the 2 RFC docs. The *Table of Contents* is fine within them, but most other internal links fail.

This sort of bug is above my pay-grade. It seemed to me that the simplest fix would be to use a *Bookmark* as a target rather than *Heading*, and a quick test worked fine. I therefore added a bookmark for every single internal link. As best as I can tell I have both switched every link-target to a bookmark in rfc-1945 (http-1.0) and they are all now effective. The new file is uploaded, and rfc-2616 (http-1.1) will follow soonish (Dec 2, hundreds of links, aargh!).

## *History*
- HTTP/0.9 :: 1989 (begun by TimBL) (zero content negotiation nor cache-control)
- HTTP/1.0 :: 1996 (see [rfc-1945 (pdf)](https://github.com/alexkemp9/Conteg/blob/main/RFC/rfc-1945_HTTP-1.0.pdf))    
(Accept-Encoding + Cached responses: 304 response)
- HTTP/1.1 :: 1997 (Updated 1999, 2014, and 2022) (see [rfc-2616 (pdf)](https://github.com/alexkemp9/Conteg/blob/main/RFC/rfc-2616_HTTP-1.1.pdf) + [rfc-7232 (pdf)](https://github.com/alexkemp9/Conteg/blob/main/RFC/rfc-7232_HTTP-1.1.pdf))    
(full content negotiation + cache-control responses available)

## *Informative Background*
Pretty much all of the WWW (World Wide Web), HTTP & Web Browsers was initiated, implemented & provided as a complete package FoC by Sir Tim Berners-Lee during the 1980s whilst employed at CERN. Like so many things, it started extremely simple and then increasingly became more complex across the following decades.
 
HTTP is a protocol universally agreed upon & used for communicating across the WWW. These words are written in 2023 & the idea of computer networking is now a commonplace affair for consumers as much as it has always been for Business, Government and so on. That was not the case in 1980.

The WWW is a network of networks. The premise, broadly, is that electronic servers are embedded within the WWW, and electronic clients are able to interact with those servers. That does include aspects of remote setup, service, etc., but we are going to concentrate upon the aspect of content delivery from Server to Client. The fundamental premise is that Clients make electronic *Requests* and Servers give electronic *Responses*:
* Wikipedia lists [9 current Request methods](https://en.wikipedia.org/wiki/HTTP#Request_methods)
* HTTP/0.9 only had *“GET”* <br /> (*“send me this file”*)
* HTTP/1.0 had three (*“GET”*, *“HEAD”* & *“POST”*).
* HTTP/1.1 has eight: (*“GET”*, *“HEAD”*, *“POST”*, *“OPTIONS”*, *“PUT”*, *“DELETE”*, *“TRACE”*, *“CONNECT”*).

### *HTTP/0.9:*
***HTTP/0.9*** specified only a simple one-line *“GET”* from Client to Server. The specification was listed on a single sheet of paper, and no RFC (Request for Comments) was ever produced. If you look closely in the HTTP/1.0 spec (next) you will find the HTTP/0.9 spec. After it was up & running, and proving both popular, effective & useful, work began immediately on the next iteration.

### *HTTP/1.0:*
***HTTP/1.0*** was a formal specification within [RFC-1945 (pdf)](https://github.com/alexkemp9/Conteg/blob/main/RFC/rfc-1945_HTTP-1.0.pdf). From our perspective, the important aspect of http/1.0 over http/0.9 is the introduction of Web-Headers (header fields, also known as *“headers”*) & Web-Bodies (an entity body) (see *“4.HTTP Message”* on p20 of the pdf).

The introduction of *“headers”* & *“bodies”* allows the introduction of content negotiation, even though at this early beginning it is most basic. Caching is introduced as an important feature, and this is relevant both for Clients *and* proxy-servers. The fundamental feature of this is a method for cooperation between client, server & proxies to allow substantial reduction in network bandwidth & subsequent increase in speed.

Savings come with the use of *HEAD* (allows a client to *“sniff”* the server & thus perhaps not have to receive a file); use of *If-Modified-Since* & *“304 Not Modified”* & again perhaps not have to receive a file; interaction between cache, *“Pragma: no-cache”*, *“Expires”* & *“Last-Modified”* to also perhaps not have to receive a file. However, those savings already mentioned are dwarfed by savings available when using *Compression*, and specifically if the entity is text.

My own testing of *content-coding* (server-enabled compression) showed typical values of +70% at level 8/9 (reduction to one-third of original size), with some pages better than 80% (reduction to one-fifth). On admin-edit pages (masses of duplicated &lt;select> drop-down boxes) reductions exceed 90%. So, most effective.

These are the relevant new features introduced in the HTTP/1.0 spec:

| Name             | RFC-1945         | PDF Page         | Info             |
|:-----------------|:-----------------|:-----------------|:-----------------|
URI | 3.2 Uniform Resource Identifiers | 15 | 
URL | 3.2.2 http URL | 16 | http_URL= "http:" "//" host [ ":" port ] [ abs_path ]
Compression | 3.5 Content Codings | 19 | 🟢 ***Content Negotiation***: <br /> content-coding = "x-gzip" \| "x-compress"
Headers | 4.3 General Header Fields | 24 | General-Header = Date \| Pragma
Methods | 5.1.1 Method | 25 | Method = "GET" \| "HEAD" \| "POST" <br /> *GET* = *“send me this file”* <br /> *HEAD* = a pure metadata interaction (no entity-body) <br /> *POST* = opposite of *GET* (eg forum posts)
Headers | 5.2 Request Header Fields | 27 | 🟢 ***Content Negotiation***:<br />5 fields were introduced: Request-Header = <br /> Authorization \|<br />From \|<br />If-Modified-Since \|<br />Referer \|<br />User-Agent
Status | 6.1 Status-Line | 28 | eg first line is *“HTTP/1.0 200 ”* <br /> This allows the client to differentiate a http/0 response from a http/1 response.
Status Codes | 6.1.1 Status Code and Reason Phrase | 28 | 15 codes were introduced: Status-Code =<br />"200"; OK<br />"201"; Created<br />"202"; Accepted<br />"204"; No Content<br />"301"; Moved Permanently<br />"302"; Moved Temporarily<br />"304"; Not Modified<br />"400"; Bad Request<br />"401"; Unauthorized<br />"403"; Forbidden<br />"404"; Not Found<br />"500"; Internal Server Error<br />"501"; Not Implemented<br />"502"; Bad Gateway<br />"503"; Service Unavailable
Response Headers | 6.2 Response Header Fields | 30 | 🟢 ***Content Negotiation***:<br />Response-Header = Location \| Server \| WWW-Authenticate
Entity Headers | 7.1 Entity Header Fields | 31 | 7 types of Entity Header were introduced: Entity-Header =<br />Allow \|<br />Content-Encoding \|<br />Content-Length \|<br />Content-Type \|<br />Expires \|<br />Last-Modified \|<br />extension-header
Expires | 10.7 Expires | 42 | 🟢 ***Content Negotiation***: affects caching
If-Modified-Since | 10.9 If-Modified-Since | 44 | 🟢 ***Content Negotiation***:<br />server returns 304 (not modified) if resource unchanged
Last-Modified | 10.10 Last-Modified | 44 | 🟢 ***Content Negotiation***:<br />eg *“Last-Modified: Tue, 15 Nov 1994 12:45:26 GMT”*
Location | 10.11 Location | 45 | 🟢 ***Content Negotiation***: (accompanied by 301 \| 302)<br />A directive to client to re-issue request (absolute URL)<br />eg *“Location: http ://www .w3.org/hypertext/WWW/NewLocation.html”*
Pragma | 10.12 Pragma | 45 | 🟢 ***Content Negotiation***: (curiously, still in use although deprecated)<br />eg Client telling proxies not to use any cached entity:<br />eg *“Pragma: no-cache”*
Referer (sic) | 10.13 Referer | 45 | 

### *HTTP/1.1:*
***HTTP/1.1*** was a formal specification within [RFC-2616 (pdf)](https://github.com/alexkemp9/Conteg/blob/main/RFC/rfc-2616_HTTP-1.1.pdf). As a development from HTTP/1.0 it adds consideration of the effects of hierarchical proxies, caching, the need for persistent connections, and virtual hosts.

Certain new 1.1 features were presaged within section D of the 1.0 Appendix. These include:     
- Request Methods: PUT, DELETE, LINK (not used), UNLINK (not used)
- Header Field Definitions: Accept, Accept-Charset, Accept-Encoding, Accept-Language, Content-Language, Link (not used), MIME-Version, Retry-After, Title (sometimes used)

These are the relevant 🟡 new features introduced in the HTTP/1.1 spec (entire section unless otherwise marked):

| Name             | RFC-2616         | PDF Page         | Info             |
|:-----------------|:-----------------|:-----------------|:-----------------|
URL | 3.2.2 http URL | 21 | “http:” “//” host [ “:” port ] [ abs_path [ “?” query ]]<br />🟡 ‘query’ is a 1.1 addition to this spec
Compression | 3.5 Content Codings | 25 | 🟢 ***Content Negotiation***: <br /> content-coding = "gzip" \| "compress" \| "deflate" \| "identity"<br />🟡 ‘deflate’ &amp; ‘identity’ are 1.1 additions to this spec
🟡 Q-Values | 3.9 Quality Values | 30 | 🟢 ***Content Negotiation***: <br /> Qvalue = ( “0” [ “.” 0*3DIGIT ] ) \| ( “1” [ “.” 0*3(“0”) ] )
🟡 Language Tags | 3.10 Language Tags | 30 | 🟢 ***Content Negotiation***: <br /> eg en, en-US, en-cockney, i-cherokee, x-pig-latin<br />used within Accept-Language and Content-Language
🟡 ETags | 3.11 Entity Tags | 31 | 🟢 ***Content Negotiation***: <br /> Entity-tag = [ weak ] opaque-tag<br />used within ETag, If-Match, If-None-Match, If-Range
🟡 Ranges | 3.12 Range Units | 32 | 🟢 ***Content Negotiation***: <br /> Range-unit = bytes-unit | other-range-unit<br />used within Range, Content-Range
Headers | 4.5 General Header Fields | 36 | 🟡 All except ‘Date’ &amp; ‘Pragma’ are 1.1 additions to this spec; General-Header =<br />Cache-Control \|<br />Connection \|<br />Date \|<br />Pragma \|<br />Trailer \|<br />Transfer-Encoding \|<br />Upgrade \|<br />Via \|<br />Warning
Methods | 5.1.1 Method | 37 | 🟡 All except ‘GET’, ‘HEAD’ &amp; ‘POST’ are 1.1 additions to this spec; Method =<br />“OPTIONS” \|<br />“GET” \|<br />“HEAD” \|<br />“POST” \|<br />“PUT” \|<br />“DELETE” \|<br />“TRACE” \|<br />“CONNECT”
Headers | 5.3 Request Header Fields | 40 | 🟢 ***Content Negotiation***:<br /> 🟡 Expanded from 5 fields (see ‘†’) to 19; Request-Header = <br />Accept \|<br />Accept-Charset \|<br />Accept-Encoding \|<br />Accept-Language \|<br />†Authorization \|<br />Expect \|<br />†From \|<br />Host \|<br />If-Match \|<br />†If-Modified-Since \|<br />If-None-Match \|<br />If-Range \|<br />If-Unmodified-Since \|<br />Max-Forwards \|<br />Proxy-Authorization \|<br />Range \|<br />†Referer \|<br />TE \|<br />†User-Agent
Status | 6.1 Status-Line | 41 | eg first line is *“HTTP/1.0 200 ”*; This allows the client to differentiate a http/0 response from a http/1 response.
Status Codes | 6.1.1 Status Code and Reason Phrase | 41 |  🟡 Expanded from 15 codes (see ‘‡’) to 39; Status-Code =<br />“100”; Continue \|<br />“101”; Switching Protocols \|<br />‡“200”; OK \|<br />‡“201”; Created \|<br />‡“202”; Accepted \|<br />“203”; Non-Authoritative Information \|<br />‡“204”; No Content \|<br />“205”; Reset Content \|<br />“206”; Partial Content \|<br />“300”; Multiple Choices \|<br />‡“301”; Moved Permanently \|<br />‡“302”; Moved Temporarily \|<br />“303”; See Other \|<br />‡“304”; Not Modified \|<br />“305”; Use Proxy \|<br />“307”; Temporary Redirect \|<br />‡“400”; Bad Request \|<br />‡“401”; Unauthorized \|<br />“402”; Payment Required \|<br />‡“403”; Forbidden \|<br />‡“404”; Not Found \|<br />“405”; Method Not Allowed \|<br />“407”; Proxy Authentication Required \|<br />“408”; Request Time-out \|<br />“409”; Conflict \|<br />“410”; Gone \|<br />“411”; Length Required \|<br />“412”; Precondition Failed \|<br />“413”; Request Entity Too Large \|<br />“414”; Request-URI Too Large \|<br />“415”; Unsupported Media Type \|<br />“416”; Requested range not satisfiable \|<br />“417”; Expectation Failed \|<br />‡“500”; Internal Server Error \|<br />‡“501”; Not Implemented \|<br />‡“502”; Bad Gateway \|<br />‡“503”; Service Unavailable \|<br />“504”; Gateway Time-out \|<br />“505”; HTTP Version not supported
