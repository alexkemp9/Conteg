# Conteg
A Content-Negotiation + Cache-Control Class for PHP-produced HTTP web-output

Web Servers such as Apache by default will auto-negotiate web-page delivery with requesting clients as to optimise both content, size & speed of delivery. All of that is lost if you use PHP to produce web-pages on-the-fly from a database (a web Forum being the classic case for that method). If you use PHP you have to manage the entire process of Cache-Control and Content-Negotiation yourself, which is more than a little daunting.

Conteg is a pre-built toolset to make it possible to conduct Content-Negotiation + Cache-Control. If implemented then pages should be delivered both more quickly and with less bandwidth. The class attempts to make the entire span of HTTP/1.1 Content-Negotiation & Cache-Control available for use.
