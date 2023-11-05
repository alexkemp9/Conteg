# *Conteg*
A Content‑Negotiation + Cache‑Control Class for PHP‑produced HTTP web‑output

Web Servers such as Apache by default will auto‑negotiate web‑page delivery when getting a Request for static files. All of that is lost if you stream web‑pages from a database (the classic case here being a web Forum). If you use PHP you have to manage the entire process of Cache‑Control and Content‑Negotiation yourself, which is more than a bit daunting.

Conteg is a tool‑set to make it possible to conduct Content‑Negotiation + Cache‑Control for web‑page delivery. Once implemented, most pages will be delivered both more quickly and consume less bandwidth. The *Conteg* include file is a single PHP class. It attempts to make the entire span of HTTP/1.1 Content‑Negotiation & Cache‑Control available for use. Some Negotiation is auto‑conducted internally, but most is external, and is operated by values provided within the single setup parameter.

## *History & Background*
See [background.md](https://github.com/alexkemp9/Conteg/blob/main/background.md) for a fuller background to the development of the HTTP protocol that is relevant to *Conteg*. The most important was the emergence of HTTP/1.0, formalised in May 1996 (see [rfc‑1945 (pdf)](https://github.com/alexkemp9/Conteg/blob/main/RFC/rfc‑1945_HTTP‑1.0.pdf)).

Sir Tim Berners‑Lee had written the software for pretty much all of what became the World Wide Web in the 1980s. He packaged together all of the software & documentation as a practical implementation whole and made it available FoC, with HTTP/0.9. His initiative was enthusiastically taken up by most of his peers, but that highlighted the deficiencies & quickly led to HTTP/1.0. This second version allowed interaction between *Client* & *Server* which could lead to considerable reductions in bandwidth + thus speed of delivery. The mechanism to get to that was the introduction of *Headers* (see *“4. HTTP Message”* on p20 of the rfc‑1945 pdf), and thus a split between the *Entity* that constitutes the web‑body of a data transfer, and the *Header* that constitutes the metadata of that transfer.

This introduction of *“headers”* & *“bodies”* allows content negotiation between client & server. Introduced at HTTP/1.0 it was basic but already remarkably complete. Caching is introduced as an important feature, and this is relevant both for Clients *and* proxy‑servers. The fundamental feature of this is a method for cooperation between client, server & proxies to allow substantial reduction in network bandwidth & subsequent increase in speed. My own testing showed typical values of +70% at level 8/9 (reduction to one‑third of original size), with some pages better than 80% (reduction to one‑fifth). On admin‑edit pages (masses of duplicated &lt;select> drop‑down boxes) reductions exceed 90%. So, most effective.

## *Class Help*
The previous paragraph covered reductions due to activating compression (on by default, and the only item auto-negotiated *inside* the Class) (`Accept‑Encoding`, activated by `'use_accept_encode' => TRUE` in setup). Load-balanced compression is considered important enough that default is ON at min level 3. The routine takes < 0.002 secs on a twin Xeon 2.4 GHz, Linux 2.6.

There are a great many other Content Negotiation items that can be activated (though they are not auto-negotiated): search for *“Help, Advice + Other Stuff”* at the end of the Class. First, here is the Constructor parameter‑array default settings:

### *Setup Array*

```php
/*
 * Constructor parameter array
 * ===========================
 *  These are all the possible array values within the single parameter supplied to
 *+ the constructor, and acted upon within setup(), with defaults.
 *  Note: none of the following is *required* - these are the program defaults:
 */
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
```

### *Requirements*
 
```php
/*
 *    PHP 4.3.0+: $file + $line bug-catching; file_get_contents()
 *    zlib:       Needed for compression encoding (odds are you have it).
 *    `ExtendedStatus On' in httpd.conf (Apache) (re: $_SERVER variables)
 */
```

 ### *How to use*
 
```php
/*
 *  Note before: SMP machines: use the setup parameter:
 *    'cpu_number' => (number-of-cpus/cores)
 *
 *  Simplest possible usage requires 3 lines:
 *    1 Turn Output Buffering on.
 *    2 Include the Class file.
 *    3 On the *very* last line create an instance of the Class.
 *
 *    ------------Start of file---------------
 *    |<?php
 *    | ob_start();            // <==== line 1
 *    | include('Conteg.inc'); // <==== line 2
 *    |
 *    |... the page ...
 *    |
 *    | new Conteg();          // <==== line 3
 *    |?>
 *    -------------End of file----------------
 *
 *  Variations:
 *  ----------
 *    A `$param' array is available as a setup parameter to the Class constructor
 *+   (line 3). It is an array of `parameter-name' => `parameter-value' pairs.
 *
 *  Here is the simplest HTTP/1.0 usage:
 */
      $param   = array(
         'modified' => strtotime( $mdate ),     //  here, $mdate is string (default is time())
         'expiry'   => 864000                   //  set expiry date 10 days from now
      );                                        //+ default is 1 hour
      $Encode   = new Conteg( $param );
/*
 *  Here is the simplest HTTP/1.1 usage:
 */
      $param   = array(
         'use_etag'   => TRUE,                  // default is Weak ETags
         'modified'   => $mdate,                // here, $mdate is Unix timestamp
         'expiry'      => 3600                  // set expiry date 1 hour from time()
      );
      $Encode   = new Conteg( $param );
```
### *Content Negotiation*
Apart from *Load-balanced Compression* (on by default) there is almost no *Content Negotiation* in the setup as envisioned above. So get ready, we are about to dive into the deeper parts of the pool, starting with *Request Headers*.

The schema involved with external negotiation (that is, external to the Class) is below the briefs on each Request/Response Header below. This is what you need to know before diving into those:
1. The default for *Conteg* is to print out the web-page at instantiation (each line#3 above)
2. That behaviour needs to be switched OFF in order to conduct External Negotiation
3. The `'noprint' => TRUE` setup parameter is used to do that
4. Using *noprint* will then require an explicit `$Encode->show()` as the last line of the script
5. Status 304, 406, 412 (early exit) throw a spanner into the above, as they will require an immediate exit<br />(see below the Request/Response Header briefs for the method to do that)
 
```php
/*
 *  Content Negotiation:
 *  ===================
 *
 *  Request Headers:
 *  ---------------
 *    Accept:              (negotiate outside Class) (media-type)
 *      $accept['type/sub-type']     = quality-integer (0-10)
 *                         Defaults:  'use_accept'         => FALSE
 *                                    'type'               => 'text/html'
 *                         Functions: getAccept()
 *                                    mediaTypeAccepted()
 *                                    isResponseNoContent()
 *                         accept:    (normal action)
 *                         or:        406 Not Acceptable
 *
 *    Accept-Charset:      (negotiate outside Class)
 *      $accept_charset['charset']   = quality-integer (0-10)
 *                         Defaults:  'use_accept_charset' => FALSE
 *                                    'charset'            => 'ISO-8859-1'
 *                         Functions: getAcceptCharset()
 *                                    charsetAccepted()
 *                                    isResponseNoContent()
 *                         accept:    (normal action)
 *                         or:        406 Not Acceptable
 *
 *    Accept-Encoding:     Auto-negotiated inside Class if TRUE
 *      $accept_encoding['encoding'] = quality-integer (0-10)
 *                         Defaults:  'use_accept_encode'  => TRUE
 *                                    'use_user_agent'     => TRUE
 *                                    'min_compression'    => 3
 *                                    'encodings'          => array('gzip','deflate','compress')
 *                         Functions: compress()
 *                                    getCompLevel()
 *                                    getSize()
 *                                    negotiateEncoding()
 *                                    setSearch()
 *                         accept:    load-balanced compression
 *                         or:        `identity' (no compression)
 *
 *    Accept-Language:     (negotiate outside Class)
 *      $accept_language['lang']     = quality-integer (0-10)
 *                         Defaults:  'use_accept_lang'    => FALSE
 *                                    'lang'               => 'en'
 *                         Function:  getAcceptLang()
 *                         accept:    (user decided)
 *                         or:        (user decided)
 *
 *    Accept-Ranges:       Auto-negotiated inside Class if TRUE
 *      $range['begin']              = int (byte)
 *      $range['end']                = int (byte)
 *                         Defaults:  'use_accept_ranges'  => FALSE
 *                         accept:    206 Partial Content
 *                         or:        416 Requested Range Not Satisfiable
 *                         Notes:     multipart/byteranges not accepted
 *                                    cannot be used with weak eTags
 *                                    switches off compression if used
 *
 *    If-Modified-Since:   Auto-negotiated inside Class if TRUE
 *      $if_modified_since           = timestamp
 *                         Defaults:  'use_last_modified'  => TRUE
 *                                    'use_expires'        => TRUE
 *                                    'modified'           => NULL
 *                                    'expiry'             => 3600 (1 hour)
 *                         Function:  isResponseNoContent()
 *                         accept:    304 Not Modified
 *                         or:        (normal action)
 *
 *    If-None-Match:       Auto-negotiated inside Class if TRUE
 *      $if_none_match[]             = eTags
 *                         Defaults:  'use_etag'           => FALSE
 *                                    'weak_etag'          => TRUE
 *                                    'etag'               => ''
 *                                    'other_var'          => ''
 *                         Function:  isResponseNoContent()
 *                         accept:    304 Not Modified
 *                         or:        412 Precondition Failed
 *
 *    If-Match:            Auto-negotiated inside Class if TRUE
 *      $if_match[]                  = eTags
 *                         Defaults:  same as If-None-Match
 *                         Function:  isResponseNoContent()
 *                         accept:    (normal action)
 *                         or:        412 Precondition Failed
 *
 *    If-Range:            Auto-negotiated inside Class if TRUE
 *      $if_range                    = timestamp/string (modified / eTag)
 *                         Defaults:  same as If-None-Match / If-Unmodified-Since
 *                         if 412:    send 200 OK
 *                         if 200:    send 206 Partial content
 *                         Notes:     eTags are added to $if_match[]
 *                                    dates are added to $if_unmodified_since
 *
 *    If-Unmodified-Since: Auto-negotiated inside Class if TRUE
 *      $if_unmodified_since         = timestamp
 *                         Defaults:  same as If-Modified-Since
 *                         Function:  isResponseNoContent()
 *                         accept:    (normal action)
 *                         or:        412 Precondition Failed
 *
 *  (Reported - no negotiation)
 *
 *    Referer:
 *      $referer[]                   = parse_url( $referer )
 *                         Defaults: 'referer_lower_case' => TRUE
 *                         Functions: getReferer()
 *
 *    User-Agent:
 *      $user_agent                  = string
 *      $browserAgent                = string
 *      $browserOS                   = string
 *      $browserVersion              = string
 *
 */
```
### *Response Headers*
This is either language or mime:
 
```php
/*
 *  Response Headers:
 *  ----------------
 *    Content-Language:    Added if TRUE and not empty
 *                         Defaults:  'use_content_lang'   => TRUE
 *                                    'lang'               => 'en'
 *
 *    Content-Type:        Added if TRUE and not empty
 *                         Defaults:  'use_content_type'   => TRUE
 *                                    'type'               => 'text/html'
 *                                    'charset'            => 'ISO-8859-1'
 *
 *
 *  External Negotiation will require the 'noprint' parameter:
 *  --------------------
 */
      $Encode = new Conteg(
         array(
            'noprint'  => TRUE
         )
      );
/*    ...
 *    (negotiation, processing, etc.)
 *    ...
 */
      $Encode->show();
```
### *Status 304, 406, 412 (early exit)*
The best way to lower system resources, reduce bandwidth + increase page-delivery speed is to send headers with no content (!).
 
```php
/*
 *  Simplest possible HTTP/1.0 usage requires 5 lines:
 *    1 Turn Output Buffering on.
 *    2 Include the Class file.
 *    3 Create an instance of the Class:
 *      use `noprint' + `modified' parameters
 *    4 Check for `304' response-status:
 *      show+exit if so, else continue script
 *    5 On the *very* last line `show()' page
 *
 *    ------------Start of file---------------
 *    |<?php
 *    | ob_start();               // <= line 1
 *    | include('Conteg.inc');    // <= line 2
 *    | $Encode = new Conteg(     // <= line 3
 *    |   array(
 *    |     'modified' => $mdate, // timestamp
 *    |     'noprint'  => TRUE
 *    |   )
 *    | );
 *    | if( $Encode->isResponseNoContent()) {
 *    |   $Encode->show();
 *    |   exit();                 // <= line 4
 *    | }
 *    |
 *    |... the rest of page ...
 *    |
 *    | $Encode->show();          // <= line 5
 *    |?>
 *    -------------End of file----------------
 *
 *  Note after:  `modified' + other parameters can be delivered at any time after
 *               instantiation with setup(), as often as you wish. It makes no sense to
 *               use isResponseNoContent() until the complete set of relevant HTTP/1.0 or
 *               HTTP/1.1 control-parameters are set in Conteg.
 *               See accompanying text files, particularly for HTTP/1.1 usage.
 */
```
### *Cache-Control*
 
```php
/*
 *  HTTP/1.0: Governed by value of `Expires' + `Pragma: no-cache' headers.
 *            Expires: "If the date given is equal to or earlier than the value of the Date
 *+                    header, the recipient must not cache the enclosed entity."
 *+           http://www.w3.org/Protocols/rfc1945/rfc1945.txt sec 10.7
 *            Pragma:  Request header; sec 10.12 (also erroneously used in response headers)
 *
 *  HTTP/1.1: Cache-Control Request + Response headers.
 *            http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9 (see also sec 13.4)
 *            http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9 (see also sec 13.4)
 *
 *  Two functions are provided to be able to respond to request headers:
 *     requestNoCache()   - returns: TRUE : a fresh response has been requested
 *                                 (int): max requested age in secs of any cached-response
 *                                 FALSE: no such request
 *     requestNoStore()   - returns: TRUE : do not store (cache) this request
 *                                 FALSE: no such request
 *
 *  Setting Response Cache-Control headers:
 *  --------------------------------------
 *     As with all other responses, Conteg is controlled by an array key within the single
 *     parameter supplied to the constructor. In the case of Cache-Control headers this is:
 *
 *     'cache_control' => (array)
 *
 *     (note that this array key is, itself, an array of key => value pairs)
 */
      $Encode = new Conteg(
         array(
            'cache_control' => array(
               'macro' => 'cache-all'
            )
         )
      );
/*
 *     Any of the individual parts of the Cache-Control header may be set; see the comments
 *     to setup() for details. For your convenience, here are all of the sub-parts:
 */
      $Encode = new Conteg(
         array(
            'cache_control'   => array(
               'max-age'      => (int),        // secs; overrides the Expires header
               'must-revalidate',              // forces caches to validate every request with server
               'no-cache',
               'no-store',
               'no-transform',
               'post-check'   => (int),
               'pre-check'    => (int),
               'private',
               'proxy-revalidate',
               'public',
               's-maxage'     => (int),        // secs; for shared (not private) caches
               'pragma'       => (string),     // strictly, not Cache-Control

               'macro'        => 'cache-all',  // cache under all circumstances (default)
               'macro'        => 'cache-none'  // never cache
               'macro'        => 'download'    // download file rather than webpage
            )
         )
      );
/*
 *     Any `no-cache' value will cause the `Expires' value to be reset to a date in the past.
 */
```
### *Vary headers*
 
```php
/*
 *     By default, a `Vary ' header is sent if the response suffers any form of Content negotiation.
 *     By default, the response is negotiated for `Accept-Encoding', and this is itself varied by User-Agent.
 *     Thus, by default a `Vary: User-Agent, Accept-Encoding' header will ALWAYS be sent. This behaviour is
 *     controlled by the Constructor setup() parameters:
 *
 *       'use_accept'         => FALSE
 *       'use_accept_charset' => FALSE
 *       'use_accept_encode'  => TRUE
 *       'use_accept_lang'    => FALSE
 *       'use_user_agent'     => TRUE
 *       'use_vary'           => TRUE
 *
 *     Setting `use_vary' to FALSE will prevent any `Vary' headers from being sent.
 *     Setting any other of the parameters above to `FALSE' will switch off Content Negotiation for that
 *     specific Request header, although the value of the header is still reported.
 *
 *     Important note: MSIE 4.x throws a (false) error message with Vary headers.
 *                     MSIE 5 + 6 will refuse to cache content (no 304s) for any except `Vary: User-Agent'.
 *                     (see also http://forums.modem-help.co.uk/viewtopic.php?p=1236)
 */
