Affiliboard API
===============

Dieses Dokument spezifiziert eine Webservice-Schnittstelle für den Abruf von Statistiken durch Affiliboard bei einem Netzwerk.


Übertragung und Authentifizierung
---------------------------------

Für die Authentifizierung stehen zwei Wege zur Verfügung:

* **Token-Authentifizierung**: Der Nutzer erhält von dem Netzwerk ein Token, welches er beim Verknüpfen des Netzwerks mit seinem Account in Affiliboard einträgt. Dieser Token wird beim Datenabruf im HTTP-Header als `X-Authentication-Token: <token>` übertragen.
* **OAuth2** Gemäß der [OAuth2-Spezifikation](http://tools.ietf.org/html/rfc6749) erhält Affilinet ein "dreibeiniges OAuth-Token", um die Daten des Nutzers beim Netzwerk abzurufen. Hierfür muss Affiliboard ein ApplicationKey und ein Application-Secret mitgeteilt werden.

Die Token-Authentifizierung lässt sich mit wenig Aufwand implementieren.
OAuth2 hingegeben bietet ein höheres Sicherheitsniveau und mehr Komfort für den Nutzer.

### HTTP Ressourcen

Für den Abruf der Ressourcen müssen Affiliboard die URIs zu den Ressurcen mitgeteilt werden.
Diese können beispielsweise sein:

* https://example.com/api/affiliboard/channels
* https://example.com/api/affiliboard/stats

oder

* https://example.com/api/affiliboard.php?resource=channels
* https://example.com/api/affiliboard.php?resource=stats

oder

* https://example.com/cgi-bin/export.cgi?provider=affiliboard&type=channels
* https://example.com/cgi-bin/export.cgi?provider=affiliboard&type=stats

oder…

### Beispiel-Request

```
GET /api/affiliboard/stats?date_begin=2013-09-01&date_end=2013-09-30
Accept: text/csv
Accept-Charset: utf-8
X-Authentication-Token: <Token>
```

### Beispiel-Responses

#### Authentifizierung erfolgreich

Bei erfolgreicher Authentifizierung die Antwort einen korrekten `Content-Type` enthalten.

```http
HTTP/1.1 200 OK
Content-Type: text/csv; charset=utf-8

date;channel_key;clicks,earnings_confirmed
2013-09-01;141;521;5.13
2013-09-01;144;32;0.63
2013-09-02;141;612;6.57
```

#### Authentifizierung fehlgeschlagen

Ist der Token ungültig, kann im Body eine Fehlermeldung zurückgegeben werden, welche dem Nutzer im Affiliboard angezeigt wird.

```http
HTTP/1.1 401 Unauthorized
Content-Type: text/plain; charset=utf-8

Invalid authentication token
```

#### Wartungsarbeiten

Über das optionale [`Retry-After`](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.37)-Feld kann festgelegt werden, wann der Request wiederholt werden soll. Ist das Feld nicht gesetzt, unternimmt Affiliboard nach frühstens 15 Minuten einen erneuten Versuch.

```http
HTTP/1.1 503 Service Unavailable
Retry-After: 120
```


Datenformat
-----------

Die Daten werden UTF-8 codiert im CSV-Format übertragen.
Die verwendeten Spalten müssen als Header in der ersten Zeile enthalten sein.
Als Spaltentrennzeichen wird ein Semikolon (`;`) verwendet.
Als Dezimaltrennzeichen wird ein Punkt (z.B. `4.99`) benutzt.

Die folgenden Spalten stehen je nach Ressource zur Verfügung:

### Channels

* `key` eindeutige (interne) Bezeichnung des Channels (z.B. der Primärschlüssel aus der Datenbank des Netzwerks) _[`String`, erforderlich]_
* `name` angezeigter Name - wenn nicht vorhanden, wird der key angezeigt _[`String`]_

### Statistiken

Der Zeitraum für den Abruf der Statistiken wird von Affiliboard mit den Query-Parametern `data_begin` und `date_end` festgelegt.

Der Primärschlüssel für einen Statistik-Datensatz setzt sich aus `date`, `channel_key` und einem optionalen `area_key` zusammen.

* `date`                Datum im Format JJJJ-MM-TT _[Date, erforderlich]_
* `channel_key`         _[`String`, erforderlich]_
* `area_key`            weitere Dimension, wie beispielsweise eine Sub-ID _[`String`]_
* `ad_views`            Anzahl Ad-Impressions   _[`int`]_
* `clicks`              Anzahl Klicks           _[`int`]_
* `earnings_confirmed`  bestätigte Umsätze      _[`decimal`]_
* `earnings_open`       unbestätigte Umsätze    _[`decimal`]_
* `earnings_canceled`   stornierte Umsätze      _[`decimal`]_
* `sales_confirmed`     bestätigte Sales        _[`int`]_
* `sales_open`          unbestätigte Sales      _[`int`]_
* `sales_canceled`      stornierte Sales        _[`int`]_
* `leads_confirmed`     bestätigte Leads        _[`int`]_
* `leads_open`          unbestätigte Leads      _[`int`]_
* `leads_canceled`      stornierte Leads        _[`int`]_


Credits
-------

© 2013 [Digineo GmbH](http://www.digineo.de/)
|
[affiliboard.de](https://affiliboard.de/) · [affiliboard.com](https://affiliboard.com/)
