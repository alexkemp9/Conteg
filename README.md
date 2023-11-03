# Conteg
A Content-Negotiation + Cache-Control Class for PHP-produced HTTP web-output

Web Servers such as Apache by default will auto-negotiate web-page delivery when getting a Request. All of that is lost if you use PHP to produce web-pages on-the-fly from a database (a web Forum being the classic case). If you use PHP you have to manage the entire process of Cache-Control and Content-Negotiation yourself, which is more than a bit daunting.

Conteg is a pre-built toolset to make it possible to conduct Content-Negotiation + Cache-Control. If implemented, then pages should be delivered both more quickly and consume less bandwidth. The include file is a monolithic class. It attempts to make the entire span of HTTP/1.1 Content-Negotiation & Cache-Control available for use. Some Negotiation is auto-conducted internally, but most is external, and is operated by values provided within the single setup parameter.

## *History*
- HTTP/0.9 :: 1989 (begun by TimBL) (zero content negotiation nor cache-control)
- HTTP/1.0 :: 1996 (see [rfc-1945 (pdf)](https://github.com/alexkemp9/Conteg/blob/main/RFC/rfc-1945_HTTP-1.0.pdf))    
(Accept-Encoding + Cached responses: 304 response)
- HTTP/1.1 :: 1997 (Updated 1999, 2014, and 2022) (see [rfc-2616 (pdf)](https://github.com/alexkemp9/Conteg/blob/main/RFC/rfc-2616_HTTP-1.1.pdf) + [rfc-7232 (pdf)](https://github.com/alexkemp9/Conteg/blob/main/RFC/rfc-7232_HTTP-1.1.pdf))    
(full content negotiation + cache-control responses available)

## *Informative Background*
Pretty much all of the WWW (World Wide Web), HTTP & Web Browsers was initiated, implemented & provided as a complete package FOC by Sir Tim Berners-Lee during the 1980s whilst employed at CERN. Like so many things, it started extremely simple and then increasingly became more complex across the following decades.
 
HTTP is a protocol universally agreed upon & used for communicating across the WWW. Thesw words are written in 2023 & the idea of computer networking is now a commonplace affair for consumers as much as it has always been for Business, Government and so on. That was not the case in 1980.

The WWW is a network of networks. The premise, broadly, is that electronic servers are embedded within the WWW, and electronic clients are able to interact with those servers. That does include aspects of remote setup, service, etc., but we are going to concentrate upon the aspect of content delivery from Server to Client. The fundamental premise is that Clients make electronic *Requests* and Servers give electronic *Responses*. Wikipedia lists [9 current Request methods](https://en.wikipedia.org/wiki/HTTP#Request_methods) and the only one available in the 1st WWW was *“GET”*, which translates simply as *“send me this file”*.

***HTTP/0.9*** specified only a simple one-line *“GET”* from Client to Server. The specification was listed on a single sheet of paper, and no RFC (Request for Comments) was ever produced. After it was up & running, and proving both popular, effective & useful, work began immediately on the next iteration.

***HTTP/1.0*** was a formal specification within [RFC-1945 (pdf)](https://github.com/alexkemp9/Conteg/blob/main/RFC/rfc-1945_HTTP-1.0.pdf). From our perspective, the important aspect of http/1 over http/0.9 is the introduction of Web-Headers (header fields, also known as *“headers”*) & Web-Bodies (an entity body) (see *“4.HTTP Message”* on p20 of the pdf).

The introduction of *“headers”* & *“bodies”* allows the introduction of content negotiation, even if it is at this early beginning only in a very basic format. Caching is introduced as an important feature, and this is relevant both for Clients *and* proxy-servers. These are relevant features introduced in the spec:

| Name                   | PDF Page                               | Info                 |
|:---------------------------------|:------------------------------------|:---------------------------------|
URI | 3.2 Uniform Resource Identifiers; p13 | 
URL | 3.2.2 http URL; p14 | http_URL= "http:" "//" host [ ":" port ] [ abs_path ]
Compression | 3.5 Content Codings; p17 | 1st content negotiation; content-coding = "gzip" \| "compress"
Methods | 5.1.1 Method; p23 | Method = "GET" \| "HEAD" \| "POST"
Headers | 5.2 Request Header Fields; p24 | 2nd content negotiation; Request-Header = Authorization \| From \| If-Modified-Since \| Referer \| User-Agent
Status | 6.1 Status-Line | eg first line is *“HTTP/1.0 200 ”*; This allows the client to differentiate a http/0 response from a http/1 response.
Status Codes | 6.1.1 Status Code and Reason Phrase; p26 | 1st 15 codes were introduced
