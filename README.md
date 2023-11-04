# *Conteg*
A Content‑Negotiation + Cache‑Control Class for PHP‑produced HTTP web‑output

Web Servers such as Apache by default will auto‑negotiate web‑page delivery when getting a Request for static files. All of that is lost if you stream web‑pages from a database (the classic case here being a web Forum). If you use PHP you have to manage the entire process of Cache‑Control and Content‑Negotiation yourself, which is more than a bit daunting.

Conteg is a tool‑set to make it possible to conduct Content‑Negotiation + Cache‑Control for web‑page delivery. Once implemented, most pages will be delivered both more quickly and consume less bandwidth. The *Conteg* include file is a single PHP class. It attempts to make the entire span of HTTP/1.1 Content‑Negotiation & Cache‑Control available for use. Some Negotiation is auto‑conducted internally, but most is external, and is operated by values provided within the single setup parameter.

## *History & Background*
See [background.md](https://github.com/alexkemp9/Conteg/blob/main/background.md) for a fuller background to the development of the HTTP protocol that is relevant to *Conteg*. The most important was the emergence of HTTP/1.0, formalised in May 1996 (see [rfc‑1945 (pdf)](https://github.com/alexkemp9/Conteg/blob/main/RFC/rfc‑1945_HTTP‑1.0.pdf)).

Sir Tim Berners‑Lee had written the software for pretty much all of what became the World Wide Web in the 1980s. He packaged together all of the software & documentation as a practical implementation whole and made it available FoC, with HTTP/0.9. His initiative was enthusiastically taken up by most of his peers, but that highlighted the deficiencies & quickly led to HTTP/1.0. This second version allowed interaction between *Client* & *Server* which could lead to considerable reductions in bandwidth + thus speed of delivery. The mechanism to get to that was the introduction of *Headers* (see *“4. HTTP Message”* on p20 of the rfc‑1945 pdf), and thus a split between the *Entity* that constitutes the web‑body of a data transfer, and the *Header* that constitutes the metadata of that transfer.

This introduction of *“headers”* & *“bodies”* allows content negotiation between client & server. Introduced first at HTTP/1.0 it was basic but already remarkably complete. Caching is introduced as an important feature, and this is relevant both for Clients *and* proxy‑servers. The fundamental feature of this is a method for cooperation between client, server & proxies to allow substantial reduction in network bandwidth & subsequent increase in speed. My own testing showed typical values of +70% at level 8/9 (reduction to one‑third of original size), with some pages better than 80% (reduction to one‑fifth). On admin‑edit pages (masses of duplicated &lt;select> drop‑down boxes) reductions exceed 90%. So, most effective.

## *Class Help*
The previous paragraph covered reductions due to activating compression (on by default, and the only item auto-negotiated *inside* the Class) (`Accept‑Encoding`, activated by `'use_accept_encode' => TRUE` in setup). There are a great many others that can be activated. First, here is the Constructor parameter‑array default settings:

### *Class Setup Array*

```php
/*
 * Constructor parameter array
 * ===========================
 *  These are all the possible array values within the single parameter supplied to
 *+ the constructor, and acted upon within setup(), with defaults.
 *  Note: none of the following is *required* - these are the program defaults:
 *
 array(
   '404'                => FALSE,                           // higher precedence than 'http_status'
   '404_to_410'         => TRUE,                            // see sendStatusHeader()
   'cache_control'      => array('macro' => 'cache-all'),   // see setup()
   'charset'            => 'ISO-8859-1',
   'cpu_number'         => 1,                               // number of CPUs in server
   'dupe_status_header' => TRUE,                            // see sendStatusHeader()
   'encodings'          => array('gzip','deflate','compress'),
   'etag'               => '',
   'expiry'             => 3600,                            // secs after time()
   'filename'           => '',                              // for download; currently, must be US-ASCII
   'http_status'        => NULL,                            // preferred to program-decided status
   'input'              => 'instream',                      // Apache-Notes
   'lang'               => 'en',
   'min_compression'    => 3,                               // sets $_minCompression if pages encoded
   'modified'           => NULL,                            // sets $last_modified to time()
   'msie_error_fix'     => TRUE,                            // avoid MSIE `friendly` error pages
   'noprint'            => FALSE,
   'other_var'          => '',                              // extra string to affect (weak) ETag
   'output'             => 'outstream',                     // Apache-Notes
   'prefer'             => array(),
   'ratio'              => 'ratio',                         // Apache-Notes
   'referer_lower_case' => TRUE,
   'search'             => array(),
   'type'               => 'text/html',
   'use_accept'         => FALSE,
   'use_accept_charset' => FALSE,
   'use_accept_encode'  => TRUE,
   'use_accept_lang'    => FALSE,
   'use_accept_ranges'  => FALSE,
   'use_apache_notes'   => FALSE,
   'use_content_lang'   => TRUE,
   'use_content_type'   => TRUE,
   'use_etag'           => FALSE,
   'use_expires'        => TRUE,
   'use_last_modified'  => TRUE,                            // TRUE = negotiate If-Modified-Since + If-Unmodified-Since
   'use_user_agent'     => TRUE,
   'use_vary'           => TRUE,
   'weak_etag'          => TRUE
 )
 */
```
### *Class Requirements*
 
```php
/*
 *    PHP 4.3.0+: $file + $line bug-catching; file_get_contents()
 *    zlib:       Needed for compression encoding (odds are you have it).
 *    `ExtendedStatus On' in httpd.conf (Apache) (re: $_SERVER variables)
*/
```
