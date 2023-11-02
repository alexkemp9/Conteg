# Conteg
A Content-Negotiation + Cache-Control Class for PHP-produced HTTP web-output

Web Servers such as Apache by default will auto-negotiate web-page delivery when getting a Request. All of that is lost if you use PHP to produce web-pages on-the-fly from a database (a web Forum being the classic case). If you use PHP you have to manage the entire process of Cache-Control and Content-Negotiation yourself, which is more than a bit daunting.

Conteg is a pre-built toolset to make it possible to conduct Content-Negotiation + Cache-Control. If implemented, then pages should be delivered both more quickly and consume less bandwidth. The include file is a monolithic class. It attempts to make the entire span of HTTP/1.1 Content-Negotiation & Cache-Control available for use. Some Negotiation is auto-conducted internally, but most is external, and is operated by values within the single setup parameter.
