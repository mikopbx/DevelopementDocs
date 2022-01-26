# REST API

## Authorization in REST

If requests are made from localhost, then authorization is not required.

{% tabs %}
{% tab title="BASH" %}
```bash
curl 'http://172.16.156.223/admin-cabinet/session/start' \
-X 'POST' --cookie-jar auth-cookies.txt \
-H 'Content-Type: application/x-www-form-urlencoded; charset=UTF-8' \
-H 'X-Requested-With: XMLHttpRequest' \
--data 'login=admin&password=adminpassword'
```
{% endtab %}

{% tab title="PHP" %}
```php
$host     = '172.16.156.223';
$login    = 'admin';
$password = 'admin';

$jar    = new \GuzzleHttp\Cookie\CookieJar;
$client = new \GuzzleHttp\Client(['cookies' => $jar]);
$options = [
    'headers' => [
        "Content-Type" => "application/x-www-form-urlencoded; charset=UTF-8",
        "X-Requested-With" => "XMLHttpRequest"
    ],
    'body'   => http_build_query(['login' => $login,'password' => $password])
];
$resultHttp = $client->request('POST', "http://$host/admin-cabinet/session/start", $options);
$code       = $resultHttp->getStatusCode();
if($code === 200) {
    $content = $resultHttp->getBody()->getContents();
    $authId  = $jar->getCookieByName('PHPSESSID');
    print_r($authId->getValue());
}
```

Use **$authId** for other API requests.
{% endtab %}
{% endtabs %}

JSON response:

```json
{
  "success": true,
  "reload": "index/index",
  "message": []
}
```





In this example:

* "**172.16.156.223**" - MikoPBX address
* "**admin**" - Web interface user name
* "**adminpassword**" - Web interface password
* "**auth-cookies.txt**" - file for storing authorization data

## Get peer statuses&#x20;

{% tabs %}
{% tab title="BASH" %}
```bash
curl -b auth-cookies.txt \
'http://172.16.156.223/pbxcore/api/sip/getPeersStatuses'
```
{% endtab %}

{% tab title="PHP" %}
```php
$host   = '172.16.156.223';
$authIdHP = 'SET AUTH ID';
$jar = \GuzzleHttp\Cookie\CookieJar::fromArray(
    [
        'PHPSESSID' => $authId
    ],
    $host
);

$client = new \GuzzleHttp\Client(['cookies' => $jar]);
$resultHttp = $client->request('GET', "http://$host/pbxcore/api/sip/getPeersStatuses");
$code       = $resultHttp->getStatusCode();
if($code === 200) {
    $content = $resultHttp->getBody()->getContents();
    print_r($content);
}
```
{% endtab %}
{% endtabs %}

JSON Response:

```json
{
  "jsonapi": {
    "version": "1.0"
  },
  "result": true,
  "data": [
    {
      "id": "201",
      "state": "OK"
    },
    {
      "id": "241",
      "state": "UNKNOWN"
    },
    {
      "id": "240",
      "state": "UNKNOWN"
    }
  ],
  "messages": [],
  "function": "getPeersStatuses",
  "processor": "MikoPBX\\PBXCoreREST\\Lib\\SIPStackProcessor::getPeersStatuses",
  "pid": 16729,
  "meta": {
    "timestamp": "2022-01-25T08:23:02-03:00",
    "hash": "68fa513e851d9452aeed7f8f16ad61cb8705c4e5"
  }
}
```

## Get peer status

{% tabs %}
{% tab title="BASH" %}
```bash
curl -b auth-cookies.txt \
http://172.16.156.223/pbxcore/api/sip/getSipPeer \
--data '{"peer":"201"}'
```
{% endtab %}

{% tab title="PHP" %}
```php
$host   = '172.16.156.223';
$authId = 'SET AUTH ID';
$number = '203';
$jar = \GuzzleHttp\Cookie\CookieJar::fromArray(
    [
        'PHPSESSID' => $authId
    ],
    $host
);

$options = [
    'body'   => json_encode(['peer' => $number])
];
$client = new \GuzzleHttp\Client(['cookies' => $jar]);
$resultHttp = $client->request('POST', "http://$host/pbxcore/api/sip/getSipPeer");
$code       = $resultHttp->getStatusCode();
if($code === 200) {
    $content = $resultHttp->getBody()->getContents();
    print_r($content);
}
```
{% endtab %}
{% endtabs %}

JSON Response:

```json
{
  "jsonapi": {
    "version": "1.son0"
  },
  "result": true,
  "data": {
    "Event": "ContactStatusDetail",
    "ActionID": "PJSIPShowEndpoint_16729",
    "AOR": "201",
    "URI": "sip:201@172.16.156.1:60939;ob",
    "UserAgent": "Telephone-PT1C 1.1",
    "RegExpire": "1643110170",
    "ViaAddress": "172.16.156.1:60939",
    "CallID": "Z4Ys3hohywlIF6wn7K6I27EM0740nYVw",
    "Status": "Reachable",
    "RoundtripUsec": "5871",
    "EndpointName": "201",
    "ID": "201;@3002860e3ac957a2c2e13136226089d0",
    "AuthenticateQualify": "0",
    "OutboundProxy": "",
    "Path": "",
    "QualifyFrequency": "60",
    "QualifyTimeout": "5.000",
    "state": "OK"
  },
  "messages": [],
  "function": "getSipPeer",
  "processor": "MikoPBX\\PBXCoreREST\\Lib\\SIPStackProcessor::getPeerStatus",
  "pid": 16729,
  "meta": {
    "timestamp": "2022-01-25T08:28:19-03:00",
    "hash": "d31a2a7bce9864c4a85a774ddf22e7149196a531"
  }
}
```

## Get provider statuses

{% tabs %}
{% tab title="SHELL" %}
```bash
curl -b auth-cookies.txt \
'http://172.16.156.223/pbxcore/api/sip/getRegistry'
```
{% endtab %}

{% tab title="PHP" %}
```php
$host   = '172.16.156.223';
$authId = 'SET AUTH ID';
$jar = \GuzzleHttp\Cookie\CookieJar::fromArray(
    [
        'PHPSESSID' => $authId
    ],
    $host
);

$client = new \GuzzleHttp\Client(['cookies' => $jar]);
$resultHttp = $client->request('GET', "http://$host/pbxcore/api/sip/getRegistry");
$code       = $resultHttp->getStatusCode();
if($code === 200) {
    $content = $resultHttp->getBody()->getContents();
    print_r($content);
}
```
{% endtab %}
{% endtabs %}

JSON Response:

```json
{
  "jsonapi": {
    "version": "1.0"
  },
  "result": true,
  "data": [
    {
      "state": "OFF",
      "id": "SIP-1601534775",
      "username": "316811",
      "host": "sip.zadarma.com"
    },
    {
      "state": "OK",
      "id": "SIP-1601534845",
      "username": "316",
      "host": "sip.zadarma.com"
    }
  ],
  "messages": [],
  "function": "getRegistry",
  "processor": "MikoPBX\\PBXCoreREST\\Lib\\SIPStackProcessor::getRegistry",
  "pid": 16731,
  "meta": {
    "timestamp": "2022-01-25T08:31:18-03:00",
    "hash": "a86bb88a16ff59a544f1d519a29a929dc42f6d4b"
  }
}
```

