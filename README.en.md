Affiliboard API
===============

This document specified an Web Service interface (API) the Affiliboard can use.


Transfer and authentication
---------------------------

You may provide two ways for the authentication:


* **Token-based authentication**: The user receives a token from your network, which he or she enters into the Affiliboard web interface. This token will be submitted by the Affiliboard background processor as HTTP header `X-Authentication-Token: <token>`.

* **OAuth2**: As of [OAuth2 spec](http://tools.ietf.org/html/rfc6749), Affiliboard receives an *application key* und a *application secret* by the user.

While the token authentication is much easier to implement, we prefer OAuth2 for both a higher level of security and comfort for the user.


### HTTP Resources

To gather statistical information, Affiliboard needs to know the URIs for the resources.
Possible URIs could be

* https://example.com/api/affiliboard/channels
* https://example.com/api/affiliboard/stats

or

* https://example.com/api/affiliboard.php?resource=channels
* https://example.com/api/affiliboard.php?resource=stats

or

* https://example.com/cgi-bin/export.cgi?provider=affiliboard&type=channels
* https://example.com/cgi-bin/export.cgi?provider=affiliboard&type=stats

or…

### Example request

```
GET /api/affiliboard/stats?date_begin=2013-09-01&date_end=2013-09-30
Accept: text/csv
Accept-Charset: utf-8
X-Authentication-Token: <Token>
```

### Exemplary responses

#### Successful authentication

After a successful authentication, the response's `Content-Type` must be valid.

```http
HTTP/1.1 200 OK
Content-Type: text/csv; charset=utf-8

date;channel_key;clicks,earnings_confirmed
2013-09-01;141;521;5.13
2013-09-01;144;32;0.63
2013-09-02;141;612;6.57
```

#### Failed authentication

If the token in invalid, you may provide an error message which will be displayed to the user.

```http
HTTP/1.1 401 Unauthorized
Content-Type: text/plain; charset=utf-8

Invalid authentication token
```

#### Maintenance

You may (optionally) define a [`Retry-After`](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.37) HTTP header, to delay Affiliboard's information gathering. If this header is not present, Affiliboard will try again in a 15-minute interval.


```http
HTTP/1.1 503 Service Unavailable
Retry-After: 120
```


Data format
-----------

The data is UTF-8 encoded and CSV formatted.
All column headers are present in the first line of the response.
You should use a semicolon (`;`) a column seperator.
You must use a dot as decimal separator (e.g. `4.99`, not `4,99`).
Do not use any thousands delimiter (`10000.42`, not `10,000.42`).

The following columns are available per resource:

### Channel

* `key` unique (internal) channel identification (e.g. the database tables primary key) _[`String`, required]_
* `name` displayed name - if not set, the key will be used _[`String`]_

### Statistics

The time period will be defined by the Affiliboard via HTTP query parameters `date_begin` and `date_end` (format `YYYY-MM-DD`).

The primary key for a single statistic record is defined by the tripel `date`, `channel_key` and `area_key`. The `area_key` is optional.

* `date`                in ISO8601 format (`YYYY-MM-DD`) _[Date, required]_
* `channel_key`         _[`String`, required]_
* `area_key`            additional dimension (e.g. "SubID") _[`String`]_
* `ad_views`            number of ad impressions  _[`int`]_
* `clicks`              number of clicks          _[`int`]_
* `earnings_confirmed`  confirmed earnings        _[`decimal`]_
* `earnings_open`       open earnings             _[`decimal`]_
* `earnings_canceled`   canceled earnings         _[`decimal`]_
* `sales_confirmed`     confirmed sales           _[`int`]_
* `sales_open`          open sales                _[`int`]_
* `sales_canceled`      canceled sales            _[`int`]_
* `leads_confirmed`     confirmed leads           _[`int`]_
* `leads_open`          open leads                _[`int`]_
* `leads_canceled`      canceled leads            _[`int`]_


Credits
-------

© 2013 [Digineo GmbH](http://www.digineo.de/)
|
[affiliboard.de](https://affiliboard.de/) · [affiliboard.com](https://affiliboard.com/)
