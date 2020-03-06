This file contains a short comparison of the WARC format specification versions 1.0 and 1.1.

Only the important parts that are directly relevant for porting a software from 1.0 to 1.1 are included here.
For the full version with all changes (including things like grammar fixes), see diff1.0-full.md.

~~Strikethrough~~ are deleted parts, **bold** are added. Note that in the grammar blocks and WARC record examples, the codes are preserved literally as `~~deleted~~` and `**added**`.

> 
>     version      = ~~"WARC/1.0"~~**"WARC/1.1"** CRLF
> 

Version bump in WARC headers

>     uri           =~~"<"~~ <'URI' per RFC3986>~~">"~~

> **<small>NOTE: in WARC 1.0 standard (ISO 28500:2009), uri was defined as "<"**
> **<'URI' per RFC 3986> ">". This rule has been changed to meet requests**
> **from implementers.</small>**
> 

`uri` is now defined without surrounding angle brackets. It was previously used in these header fields: WARC-Record-ID, WARC-Concurrent-To, WARC-Refers-To, WARC-Target-URI, WARC-Warcinfo-ID, WARC-Profile, WARC-Segment-Origin-ID. All of these except WARC-Target-URI and WARC-Profile now have the angle brackets directly in those header field definitions instead.

>     defined-field  = WARC-Type
>                    | [...]
>                    | **WARC-Refers-To-Target-URI**
>                    | **WARC-Refers-To-Date**
>                    | [...]

Two new fields, details see 1.1 spec.

> All ~~‘response’, ‘resource’, ‘request’, ‘conversion’~~**'response', 'resource', 'request', 'revisit', 'conversion'** and
> ~~‘continuation’~~**'continuation'** records may have a payload. All ~~‘warcinfo’, ‘metadata’~~**'warcinfo'** and ~~‘revisit’~~**'metadata'**
> records shall not have a payload.

`revisit` records are now allowed to have a payload.

> **New named fields and new records types may be defined in extensions of**
> **the core format. However, it is strongly recommended to discuss any**
> **addition to verify that a suitable field or type doesn't already exist**
> **to avoid collision. Discussion should notably be held within the IIPC.**
> **More information on <http://bibnum.bnf.fr/warc/>.**

A new explicit mention that extensions can define their own fields and records but that is discouraged.

>     WARC-Record-ID   = "WARC-Record-ID" ":" **"<"** uri **">"**

Explicit addition of the angle brackets since `uri` no longer includes them (see above)

> WARC-Date (mandatory)
> ---------------------
> 
> ~~A 14-digit~~**The WARC-Date is a** UTC timestamp ~~formatted according to YYYY-MM-DDThh:mm:ssZ,~~**as** described in the W3C profile of ~~ISO8601 \[W3CDTF\].~~**ISO**
> **8601:1988 \[W3CDTF\], for example YYYY-MM-DDThh:mm:ssZ.** The timestamp
> shall represent the instant that data capture for record creation began.
> Multiple records written as part of a single capture event (see section
> 5.7) shall use the same WARC-Date, even though the times of their
> writing will not be exactly synchronized.
> 
> **WARC-Date may be specified at any of the levels of granularity described**
> **in \[W3CDTF\]. If WARC-Date includes a decimal fraction of a second, the**
> **decimal fraction of a second shall have a minimum of 1 digit and a**
> **maximum of 9 digits. WARC-Date should be specified with as much**
> **precision as is accurately known. This document recommends no particular**
> **algorithm for access software to choose a record by date when an exact**
> **match is not available.**
> 
>     WARC-Date = "WARC-Date" ":" w3c-iso8601
>     w3c-iso8601 = ~~<YYYY-MM-DDThh:mm:ssZ>~~**<a UTC timestamp formatted according to [W3CDTF]>**
> 
> All records shall have a WARC-Date field.
> 
> **See Annex A for examples on usage of WARC-Date fields.**
> 

WARC-Date values are much more versatile now, allowing anything between just a year and down to nanoseconds. The WARC/1.0 date format is still valid in 1.1.

> WARC-Type (mandatory)
> ---------------------
> 
> [...]
> 
>     WARC-Type   = "WARC-Type" ":" record-type
>     record-type = "warcinfo" | "response" | "resource"
>                 | "request" | "metadata" | "revisit"
>                 | "conversion" | "continuation"~~| future-type~~
>     ~~future-type = token~~

`future-type` is no longer defined but this is covered in the extension note above instead.

>     WARC-Concurrent-To = "WARC-Concurrent-To" ":" **"<"** uri **">"**

Explicit inclusion of the angle brackets due to the `uri` redefinition

>     WARC-Refers-To  = "WARC-Refers-To" ":" **"<"** uri **">"**

Explicit inclusion of the angle brackets due to `uri` redefinition

>     WARC-Truncated = "WARC-Truncated" ":" reason-token
>     reason-token   = "length"         ; exceeds configured max
>                                       ; length
>                    | "time"           ; exceeds configured max time
>                    | "disconnect"     ; network disconnect
>                    | "unspecified"    ; other/unknown reason
>                  ~~| future-reason~~
>     ~~future-reason  = token~~
>
> **Other reasons may be defined in extensions of the core format.**

`future-reason` is now covered by the general note above about extensions.

>     WARC-Warcinfo-ID = "WARC-Warcinfo-ID" ":" **"<"** uri **">"**

Explicit inclusion of the angle brackets due to the `uri` redefinition

>     WARC-Segment-Origin-ID = "WARC-Segment-Origin-ID" ":" **"<"** uri **">"**

Explicit inclusion of the angle brackets due to the `uri` redefinition

> ~~When a 'response' is known to have been truncated, this shall be noted~~
> ~~using the WARC-Truncated field.~~

WARC 1.1 does not require use of WARC-Truncated, but it also doesn't disallow it, so nothing changes effectively for using this header.

https://github.com/iipc/warc-specifications/issues/62

> [...]
>
> The payload of a 'request' record with a target-URI of scheme 'http' or
> 'https' is defined as its ~~'entity-body' (per \[RFC2616\]), with any~~
> ~~transfer-encoding removed.~~**'entity- body' (as specified in \[RFC 2616\]).**

The `entity-body` always has the transfer encoding removed, so this is not a change, only a clarification. https://github.com/iipc/warc-specifications/issues/22

Note that many WARC-writing tools do not handle this situation correctly, both in the context of requests and responses: https://github.com/webrecorder/warcio/issues/74

> ### Profile: Identical Payload Digest
> 
> [...] **It is**
> **not necessary that the URI of the original capture and the revisit be**
> **identical.**

WARC 1.0 did not require that either but it was technically unspecified.

> To indicate this profile, use the URI:
> 
> ~~<http://netpreserve.org/warc/1.0/revisit/identical-payload-digest>~~**http://netpreserve.org/warc/1.1/revisit/identical-payload-digest**

Version bump

> [...]
>
> [...] A WARC-Truncated header with reason 'length' shall be used for any
> identical-digest truncation. **It is recommended that server response**
> **headers be preserved in the revisit record in this manner.**

There was no such recommendation in the 1.0 specs, but it was allowed by the spec (and used by some implementations).

> For records using this profile, the payload is defined as the original
> payload content whose digest value was unchanged.
> 
> Using a WARC-Refers-To header to identify a specific prior record from
> which the matching content can be retrieved is recommended, to minimize
> the risk of misinterpreting the 'revisit' record. **The following two**
> **optional fields can also be used to associate the revisit record with**
> **the original record. Their use is recommended:**
> 
> **- WARC-Refers-To-Target-URI. Its value should be equal to the**
> **  WARC-Target-URI in the WARC record that the current record is**
> **  considered a duplicate of.**
> **- WARC-Refers-To-Date. Its value should be equal to the WARC-Date in the**
> **  WARC record that the current record is considered a duplicate of.**

New header fields

> To indicate this [Server Not Modified] profile, use the URI:
> 
> ~~<http://netpreserve.org/warc/1.0/revisit/server-not-modified>~~**http://netpreserve.org/warc/1.1/revisit/server-not-modified**

Version bump

> Using a WARC-Refers-To header to identify a specific prior record from
> which the unmodified content can be retrieved is recommended, to
> minimize the risk of misinterpreting the 'revisit' record. **WARC-**
> **Refers-To-Date may be optionally used to help resolve the original**
> **capture. Its value should be equal to the WARC-Date in the WARC record**
> **that the current record is considered a duplicate of. Its use is**
> **recommended.**

New header field

Annexes are included here in the 1.1 order.

> Annex A. *(informative)* Use cases for writing WARC records
> ===========================================================
> 
> [...]
>
> **## Table A.6**
> 
> **Different use cases for record timestamps, featuring different levels of**
> **precision**
> 
> <div class="table-responsive">
> <table class="table table-bordered">
> <tbody>
> <tr>
> <th>Description of use case</th>
> <th>Record generated</th>
> </tr>
> <tr>
> <td>
> A web page has been captured by a WARC 1.1 compliant crawler
> </td>
> <td>
> <b>WARC record created:</b>
> <pre>
> WARC-Date: 2016-01-11T23:24:25.412030Z
> </pre>
> </td>
> </tr>
> <tr>
> <td>
> A web page has been captured by a legacy crawler
> </td>
> <td>
> <b>WARC record created:</b>
> <pre>
> WARC-Date: 2016-01-11T23:24:25Z
> </pre>
> </td>
> </tr>
> <tr>
> <td>
> A video has been donated by a website owner and converted to the WARC
> format
> </td>
> <td>
> <b>WARC record created:</b>
> <pre>
> WARC-Date: 2016-01
> </pre>
> </td>
> </tr>
> </tbody>
> </table>
> </div>

New example showcasing the new WARC-Date flexibility

> Annex B: *(informative)* Examples of WARC records
> =================================================
> 
> 
> Example of 'warcinfo' record 
> ----------------------------
> 
>     ~~WARC/1.0~~**WARC/1.1**
>     WARC-Type: warcinfo
>
> [...]
>
>     format: WARC file version ~~1.0~~**1.1**
>     conformsTo:
>      ~~http://www.archive.org/documents/WarcFileFormat-1.0.html~~**http://iipc.github.io/warc-specifications/specifications/warc-format/warc-1.1/**
> 

Version bump

> Example of 'revisit' record
> ---------------------------
> 
>     ~~WARC/1.0~~**WARC/1.1**
>     WARC-Type: revisit
>     WARC-Target-URI: http://www.archive.org/images/logoc.jpg
>     WARC-Date: ~~2007-03-06T00:43:35Z~~**2017-06-23T12:43:35Z**
>     WARC-Profile: ~~http://netpreserve.org/warc/1.0/server-not-modified~~**http://netpreserve.org/warc/1.1/server-not-modified**
>     WARC-Record-ID: <urn:uuid:16da6da0-bcdc-49c3-927e-57494593bbbb>
>     WARC-Refers-To: <urn:uuid:92283950-ef2f-4d72-b224-f54c6ec90bb0>
>     **WARC-Refers-To-Target-URI: http://www.archive.org/images/logoc.jpg**
>     **WARC-Refers-To-Date: 2016-09-19T17:20:24Z**
>     Content-Type: message/http
>     Content-Length: 226
> 
>     HTTP/1.x 304 Not Modified
>     Date: Tue, 06 Mar ~~2007~~**2017** 00:43:35 GMT
>     Server: Apache/2.0.54 (Ubuntu) PHP/5.0.5-2ubuntu1.4 Connection: Keep-Alive
>     Keep-Alive: timeout=15, max=100
>     ~~Etag:~~**ETag:** "3e45-67e-2ed02ec0"

Example showing the new header fields (and some minor changes)

> Annex C: *(informative)* WARC file size and name recommendations
> ================================================================
> 
> [...]
>
> ~~IIPC member institutions have expressed an interest in adopting a common~~
> ~~naming strategy, with per-institution unique identifiers to assist in~~
> ~~marking WARC files with their institution of origin. It is proposed that~~
> ~~all such WARC file names adhering to this future convention begin~~
> ~~"iipc".~~
> 
> ~~This specification does not require any particular WARC file naming~~
> ~~practice, but conventions similar to the above are recommended within~~
> ~~WARC-creating institutions. The file name prefix "iipc" should not be~~
> ~~used unless participating in a future IIPC naming registry.~~

Dropped proposal/recommendation
