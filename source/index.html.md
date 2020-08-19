---
title: ZBG Future API Documentation

language_tabs: # must be one of https://git.io/vQNgJ
  - json

toc_footers:
  - <a href="https://www.zbg.com/" class="createApi">ZBG Exchange</a>

includes:

search: true
---

# Update Log


## 2020-05-27 

Modify [Place an Order](#place-an-order) interface: Add field clientOrderId


## 2020-04-20 

Add [Place an Order](#place-an-order) interface and etc.:

* Add interface [[/exchange/api/v1/future/common/contracts](#public-get-contracts)]
* Add interface [[/exchange/api/v1/future/common/currencies](#public-get-contract-currencies)]
* Add interface [[/exchange/api/v1/future/place](#place-an-order)]
* Add interface [[/exchange/api/v1/future/cancel](#cancel-an-contract-order)]
* Add interface [[/exchange/api/v1/future/cancel-all](#cancel-all-contract-orders)]
* Add interface [[/exchange/api/v1/future/positions](#get-contract-position)]
* Add interface [[/exchange/api/v1/future/orders](#get-activity-orders)]
* Add interface [[/exchange/api/v1/future/orders/his](#get-historical-orders)]
* Add interface [[/exchange/api/v1/future/assets/flow](#get-contract-asset-flow)]
* Add interface [[/exchange/api/v1/future/assets/available](#get-available-assets)]
* Add interface [[/exchange/api/v1/future/assets/profits](#get-contract-loss-and-benefit-history)]
* Add interface [[/exchange/api/v1/future/assets/transfer](#transfer-margin-between-spot-account-and-future-account)]
* Add interface [[/exchange/api/v1/future/assets/transfer-history](#get-transfer-history)]
* Add interface [[/exchange/api/v1/future/adjust-margin-rate](#adjust-margin-rate)]
* Add interface [[/exchange/api/v1/future/adjust-margin](#adjust-margin)]
* Add interface [[/exchange/api/v1/future/florders](#get-contract-force-close-history)]
* Add interface [[/exchange/api/v1/future/fcorders](#get-contract-force-deduction-history)]
* Add interface [[/exchange/api/v1/future/market/klines](#public-contract-kline)]
* Add interface [[/exchange/api/v1/future/market/ticker](#public-single-contract-market)]
* Add interface [[/exchange/api/v1/future/market/tickers](#public-all-contract-market)]
* Add interface [[/exchange/api/v1/future/market/depth](#public-contract-depth)]
* Add interface [[/exchange/api/v1/future/market/trades](#public-contract-trading-history)]
* Remove interface [/exchange/api/v1/account/future/auth]
* Remove interface [/exchange/api/v1/common/future/symbol]
* Remove interface [/exchange/api/v1/common/future/currency]
* Remove interface [/exchange/api/v1/common/future/kline]
* Remove interface [/exchange/api/v1/common/future/tickers]
* Remove interface [/exchange/api/v1/common/future/ticker]
* Remove interface [/exchange/api/v1/common/future/depth]
* Remove interface [/exchange/api/v1/common/future/trades]


## 2020-03-31

Add Contract Interfaces



# Introduction

## API Introduction


Welcome to the ZBG API!     

You can use this API to access the contract's market data, trade, and manage your account.

You can view code examples in the dark area to the right.

<aside class="notice">
Any problems during use, please contact us: QQ group:829230107
</aside>


# Access Instructions

## Access URLs

REST API

<code>https://www.zbg.com</code>


## Interface Type

In this section we divides interfaces into two types：

- Public interface
- Private interface

**Public Interface**

The public interface can be used to obtain configuration information and market data and the public request can be invoked without authentication

**Private Interface**

The private interface can be used for order management and account management.Each private request must be signed using a canonical form of authentication.

The private interface needs to be validated using your API key.You can generate API key <a href="https://www.zbg.com/login?path=https://www.zbg.com/u/api">here</a>.


## Request Format
   
**All API requests are made as GET or POST.**
   
**For GET requests, all parameters are in the path parameters;For POST requests, all parameters are sent as JSON in the request body.**



## Response Format

All the interface returns are in JSON format and at the top of the JSON there are several fields that represent the response status and properties :
"code" and "message" In the "datas" field, the return code of "1" means that the request was successful, while the rest means that the request failed. 
Please refer to the error code list for details.


```json
{
"datas":null,                 // The body data in response
"resMsg":
    {
        "code":"1",          // error code
        "message":"success!" // prompt message
    }
    
}
```

## Authentication

> Java signatures are used for example：

```java
package io.at.exchange.base.util;

import com.alibaba.fastjson.JSON;
import com.example.kits.coder.DigestKit;
import io.at.exchange.base.exceptions.BuzException;
import lombok.extern.slf4j.Slf4j;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.http.*;
import org.springframework.stereotype.Component;
import org.springframework.web.client.RestTemplate;

import javax.annotation.Resource;
import java.util.HashMap;
import java.util.Map;
import java.util.stream.Collectors;

/**
 * @author zhangzp
 */
@Slf4j
@Component
public class HttpUtil {
    @Value("${exchange.user.api-id}")
    private String apiId;

    @Value("${exchange.user.api-secret}")
    private String apiSecret;
    
    @Value("${exchange.user.api-passphrase}")
    private String apiPassphrase;

    /**
     * Spring's native Http request facility is used here, as are other open source projects.
     */
    @Resource
    private RestTemplate restTemplate;

    public String get(String url, Map<String, Object> param) throws BuzException {
        Long time = System.currentTimeMillis();

        if (param == null) {
            param = new HashMap<>(5);
        }

        String content = param.entrySet().stream()
                .filter(e -> e != null && !isEmpty(e.getKey()))
                .sorted(Map.Entry.comparingByKey())
                .map(e -> e.getKey() + (e.getValue() == null ? "" : e.getValue()))
                .collect(Collectors.joining(""));

        url = extendVariables(url, param);

        HttpHeaders headers = new HttpHeaders();
        headers.set("Apiid", this.apiId);
        headers.set("Timestamp", time.toString());
        // If the user sets the API Key when creating it, pass the parameter:
        header.put("Passphrase", EncryptUtils.md5(time + apiPassphrase));
        headers.set("Sign", DigestKit.md5(apiId + time + content + apiSecret).toLowerCase());

        System.out.println("sign ==>" + headers.get("Sign"));
        HttpEntity<?> entity = new HttpEntity<>(headers);

        try {
            ResponseEntity<String> result = this.restTemplate.exchange(url, HttpMethod.GET, entity, String.class);

            if (result.getStatusCode() == HttpStatus.OK) {
                return result.getBody();
            }
        } catch (Exception e) {
            log.error("request url [" + url + "] error", e);
        }

        throw new BuzException("1001", "request api error");
    }

    private String extendVariables(String url, Map<String, Object> param) {
        if (param == null || param.isEmpty()) {
            return url;
        }

        String query = param.entrySet().stream()
                .map(e -> e.getKey() + "=" + (e.getValue() == null ? "" : e.getValue()))
                .collect(Collectors.joining("&"));
        return String.join(url.indexOf('?') > 0 ? "&" : "?", url, query);
    }
    
    /**
     * Not null, not empty string, not double quotes, not {}
     */
    private boolean isEmpty(String source) {
        return source == null || source.isEmpty() || source.equals("\"\"") || source.trim().equals("{}");
    }

    /**
     * post request interface，ContentType : appliation/json
     *
     * @param url   request path
     * @param param required parameter map
     * @return repsonse string
     * @throws BuzException If the request fails
     */
    public String post(String url, Map<String, Object> param) throws BuzException {
        Long time = System.currentTimeMillis();

        if (param == null) {
            param = new HashMap<>(5);
        }

        String content = JSON.toJSONString(param);

        HttpHeaders headers = new HttpHeaders();
        headers.setContentType(MediaType.APPLICATION_JSON);

        headers.set("Apiid", this.apiId);
        headers.set("Timestamp", time.toString());
        // If the user sets the API Key when creating it，this parameter must be required
        header.put("Passphrase", EncryptUtils.md5(time + apiPassphrase));
        headers.set("Sign", DigestKit.md5(apiId + time + content + apiSecret).toLowerCase());

        System.out.println("sign ==>" + headers.get("Sign"));
        HttpEntity<?> entity = new HttpEntity<>(content, headers);

        try {
            ResponseEntity<String> result = this.restTemplate.exchange(url, HttpMethod.POST, entity, String.class);

            if (result.getStatusCode() == HttpStatus.OK) {
                return result.getBody();
            }
        } catch (Exception e) {
            log.error("request url [" + url + "] error", e);
        }

        throw new BuzException("1001", "request api error");
    }
}
```

> md5 The abstract method is as follows：


```java
public static final String ALGORITHM_MD5 = "MD5";

/**
 * md5 encryption
 *
 * @param message the source string
 * @return encrypted string
 */
public static String md5(String message) {
    return StringKit.toHex(messageDigest(message, ALGORITHM_MD5));
}

/**
 * md5 encryption
 *
 * @param message   the source string
 * @param algorithm digest algorithm
 * @return summary byte array
 */
private static byte[] messageDigest(String message, String algorithm) {
    return messageDigest(message.getBytes(UTF_8), algorithm);
}

/**
 * generates a summary for the byte array
 *
 * @param message   bytecode array
 * @param algorithm digest algorithm
 * @return summary byte array
 */
private static byte[] messageDigest(byte[] message, String algorithm) {
    try {
        MessageDigest digest = MessageDigest.getInstance(algorithm);
        digest.update(message);
        return digest.digest();
    } catch (NoSuchAlgorithmException e) {
        throw new EncryptException(e);
    }
}
```

> StringKit.toHex() ：

```java
/**
 * initialize an array of characters to hold each hexadecimal character
 */
public static final char[] HEX_DIGITS = {'0', '1', '2', '3', '4', '5', '6', '7', '8', '9', 'A', 'B', 'C', 'D', 'E', 'F'};
/**
 * turn the hexadecimal string
 *
 * @param bytes bytecode array
 * @return hexadecimal string
 */
public static String toHex(byte[] bytes) {
    char[] chars = new char[bytes.length * 2];
    int index = 0;

    for (byte b : bytes) {
        chars[index++] = HEX_DIGITS[b >>> 4 & 0xf];
        chars[index++] = HEX_DIGITS[b & 0xf];
    }

    return new String(chars);
}
```

> JS Signature Call Example：

```js
// The GET method requests a signature, taking the interface for querying historical orders as an example.

//Import the axios module
const axios = require('axios')
//Import the MD5 module
const MD5 = require('MD5')

//request data
var data = {
    symbol: "zt_usdt",
    side: "buy",
    page: 1,
    size: 10
}
var timestamp = (new Date()).getTime();
//request headers
var headers = {
    Apiid: 'api key', // replace it with your own API key
    Clienttype: 5,
    Timestamp: timestamp,
    //If the user sets the API Key when creating it, the parameter must be passed
    // Replace passphrase in the signature
    Passphrase: MD5(timestamp.toString() + 'passphrase')
    // Signature order :Apiid+ timestamp + request parameter +Apisecret
    // Replace the API key and API secret in the signature
    Sign: MD5('api key' + timestamp.toString() + dataStringify(data) + 'api secret')
};

var config = {
    url: 'https://www.zbg.com/exchange/api/v1/order/orders',
    headers: headers,
    method: 'GET',
    timeout: 3000,
    params: data
};

//Parameter content string is used to sort all parameters by the first letter of key value before pressing
//Key1 + value1 + key2 + value2...Form string
function dataStringify(data = {}) {
    var keys = Object.keys(data);
    var str = ''
    keys.sort()
    keys.forEach(function (val) {
        str += val + data[val]
    })
    return str
}

axios(config).then(function (res) {
    console.log(res.data)
}).then(function (err) {
    console.log(err)
});
```

```js
// The POST method requests a signature, as shown in the following list and interface
// Import the axios module
const axios = require('axios')
// Import the MD5 module
const MD5 = require('MD5')

// Request datas
var data = {
    symbol: 'zt_usdt',
    side: 'buy',
    amount: 1,
    price: 0.038
}
var timestamp = (new Date()).getTime();
// Request headers
var headers = {
    Apiid: 'api key', // replace it with your own API key
    Clienttype: 5,
    Timestamp: timestamp,
    //If the user sets the API Key when creating it, the parameter must be passed
        // Replace passphrase in the signature
    Passphrase: MD5(timestamp.toString() + 'passphrase')
    // Signature order :Apiid+ timestamp + request parameter +Apisecret
    // Replace the API key and API secret in the signature
    Sign: MD5('api key' + timestamp.toString() + JSON.stringify(data) + 'api secret')
};
var config = {
    url: 'https://www.zb.com/exchange/api/v1/order/create',
    headers: headers,
    method: 'POST',
    timeout: 3000,
    data: data
};


axios(config).then(function (res) {
    console.log(res.data)
}).then(function (err) {
    console.log(err)
});
```

**Overview**

the API requests may be tampered during internet. In order to ensure that the request is not changed, all private API must be signed by your API Key (Secrete Key).

A valid request consists of below parts:

- Interface Address : https://www.zbg.com/exchange/api/v1/order/orders
- API Access Key : The Access Key in the API Key
- Signature Method : The Hash method that is used to sign, it uses md5
- Timestamp : The millisecond timestamp of the time you made the request, located in the Header
- Parameters : Each API method has a group of parameters, you can refer to detailed document for each of them..
- Signature : The value after signed, it is guarantee the signature is valid and the request is not be tempered.

That is, the request header must contain the following content：

*Apiid* : API Key of string type.

*Timestamp* : The timestamp of the request.

*Sign* :  Signature string (Sign according to the reference format).

*Passphrase* : The passphrase that you specified when you created the API key. This field is not required, depending on whether the user specified it when creating the API Key.

<aside class="notice">
Passphrase request headers are strings that are processed by *md5(timestamp+passphrase)* instead of directly using Passphrase to request a password
</aside>


**Create API Key** 

You can create API Key <a href="https://www.zbg.com/login?path=https://www.zbg.com/u/api">here</a>.

API key including the following three parts:

+ Access Key : API access key
+ Secret Key : The key used for signature authentication encryption (visible on application only)
+ Passphrase : API access command

The API key and secret Key will be randomly generated and provided by ZBG, and Passphrase will be provided by you to ensure the security of API access.

Passphrase for the optional field users can choose to provide the field according to their own needs, if the field is filled in, the request header must be put on when accessing the interface, the background system will carry out the verification.

<aside class="notice">
API key it has all operation rights including trading, depositing and withdrawing currency.
</aside>

<aside class="warning">
These keys are closely related to account security and should not be disclosed to others at any time.If you find your API Key is disposed, please remove it immediately.
</aside>

**Timestamp**

The request header is a long integer timestamp at the millisecond level, and requests that differ by more than 60 seconds between the timestamp and the server time are considered expired and rejected by the system. 
If you think there is a significant time bias between the server and the API server, we recommend that you use []the fetch server time interface](#) to query the API server time.


**Signature**

Take the example of querying an order list:

<p><code>https://www.zbg.com/exchange/api/v1/order/orders?symbol=zt_ust&side=buy&from=1&size=100</code></p>
1. Sort the parameters (keys) in the order of the ASCII code, such as:

<p><code>from=1</code></p>
<p><code>side=buy</code></p>
<p><code>size=100</code></p>
<p><code>symbol=zt_usdt</code></p>

2. Concatenate the strings in the order above.

<p><code>from1sizebuysize100sumbolzt_usdt</code></p>

3. Use the request string from the previous step and your key to generate a digital signature : MD5(key + timestamp + signature + secret).

<p><code>5fcbdb0862e10f9f6b885fdde42d58a1</code></p>

4.Add fields such as the generated digital signature App key timestamp to the Header


<p><code>header.put("Apiid",id);</code></p>
<p><code>header.put("Timestamp", String.valueOf(timestamp));</code></p>
<p><code>header.put("Passphrase", md5(timestamp + passphrase));</code></p>
<p><code>header.put("Sign", "5fcbdb0862e10f9f6b885fdde42d58a1"</code></p>

<aside class="warning">
Body mode *application/json* is slightly different for Post requests. In this case, there are no steps 1 or 2, so just enter step 3 using the body as a signature string.
</aside>


## Error Code

Code    | Description
--------|------------
1|success
6000|Parameters are missing
6001|General error prompt
6010|Can't find a market
6011|You have enable Google verification for account login, please enter Google verification code
6012|Unknown operation type!
6013|You cannot deposit and withdraw until you passed the mobile phone authentication and Google authentication, for the sake of your account security，Please pass the mobile phone authentication or Google authentication first
6014|SMS verification code error, please require it again
6019|Address requests are frequent. Please try again one hour later
6020|Network exception, please try again later
6021|Restrictions on withdrawal operations
6033|Please enter the correct Google verification code
6040|The account has been frozen and temporarily unavailable
6041|This account is not the main account, temporarily unable to operate
6043|The user has been blacklisted and cannot log in
6096 |Invalid parameter
6097 |Request too frequently
6098|The user has been listed on the transaction blacklist and cannot trade
6115|Failed to submit the withdrawal application！
6117|There is no withdrawal record selected to cancel！
6118|Withdrawal cancellation failed！
6119|This record cannot cancel withdrawal！
6125|An invalid currency type！
6133|Withdrawal address is illegal！
6150|Single withdrawal exceeds or falls below the limit
6151|The current withdrawal exceeds the limit
6152|Cash withdrawal exceeds user limit
6153|Insufficient funds
6154|The record is not need re-withdraw
2012|entrust not exists or on dealing with system！
6400|The market is currently closed
6402|Your order quantity exceeds the maximum limit :%s
6403|Your order price exceeds the limit :%s~%s
6601|Bitbank does not have this currency
6602|Bitbank has insufficient balance in this currency. Please recharge first
6603|Current currency is forbidden to be recharged 
6894|The API signature is no longer valid!
6895|Failed to verify API permissions. Interface is not an authorization API
6896|Failed to verify the API permission. The Userid is not matching with Apiid
6897|Failed to verify the API permission. Please confirm whether to enable API permission
6898|Failed to verify the API permission for multiple times. Please confirm whether to enable API permission
6899|The market is temporarily not open to API trading
6900|The exchange server temporarily not open to API trading
6991|Incorrect price accuracy, up to %s digits in decimal places
6992|The quantity accuracy of the order is wrong, and the number of decimal places is up to %s digits
6993|The minimum order quantity of the order is wrong, the minimum amount is %s"
6999|access forbidden



# Reference Data

## Public-Get Contracts

This endpoint returns all the contracts supported by the ZBG platform.。

**HTTP Request**

+ GET <code>/exchange/api/v1/future/common/contracts</code>

**Request Parameter**

None

**Response Content**

> Response:

```json
"datas":[
       {
            "symbol":"XRP_USDT",
            "lotSize":1,
            "contractId":1000010,
            "takerFeeRatio":"0.000750000000000000",
            "commodityId":"1000000",
            "currencyId":"7",
            "makerFeeRatio":"0.000250000000000000",
            "contractUnit": 10,
            "currencyName":"USDT",
            "commodityName":"XRP",
            "priceTick":"0.000100000000000000"
        },
        {
            "symbol":"BCH_USDT",
            "lotSize":1,
            "contractId":1000011,
            "takerFeeRatio":"0.000750000000000000",
            "commodityId":"6",
            "currencyId":"7",
            "contractUnit": 0.1,
            "currencyName":"USDT",
            "commodityName":"BCH",
            "makerFeeRatio":"0.000250000000000000",
            "priceTick":"0.050000000000000000"
        },
        {
            "symbol": "BTC_USD-R",
            "lotSize": "1.000000000000000000",
            "contractId": 1000001,
            "takerFeeRatio": "0.000750000000000000",
            "commodityId": 100001,
            "currencyId": 2,
            "contractUnit": 1,
            "currencyName":"BTC",
            "commodityName":"USD",
            "makerFeeRatio": "0.000250000000000000",
            "priceTick": "0.5"
          },
        ...
]
```

Field            | DataType | Description 
--------------------|-----------|--------
contractId       |	int  | contract ID 
symbol              |	string  | contract name 
commodityId       | int       |     base currency ID 
currencyId      |	int  | quote currency ID 
lotSize     |	string | Minimum trading unit 
priceTick    |	string | Minimum quoted unit 
contractUnit   |	decimal  | unit of contract 
makerFeeRatio   |	string  | maker trading fee 
takerFeeRatio   |	string  | taker  trading fee 



## Public-Get Contract Currencies

This endpoint returns all contract currencies supported by the ZBG platform.

**HTTP Request**

+ GET <code>/exchange/api/v1/future/common/currencies</code>

**Request Parameter**

None

**Response Content**

> Response:

```json
"datas":[
    "datas":[
        {
            "currencyId":7,
            "symbol":"USDT",
            "displayPrecision":2,
            "enabled":1
        },
        {
            "currencyId":999999,
            "symbol":"CUSD",
            "displayPrecision":2,
            "enabled":0
        }
]
```

Field        |  DataType  | Description 
----------------|------------|--------
currencyId            |   string   | contract currency id 
symbol            |   string   | currency 
displayPrecision       |	int  | Display decimals 
enabled        |	int   | withidrawal，1:able，0:unable 


# Contract Market

## Public-Contract KLine

This endpoint returns historical k-line data.。

**HTTP Request**

+ GET <code>/exchange/api/v1/future/market/klines</code>

**Request Parameter**


Parameter            |  DataType  |Required| Description 
----------------|------------|--------|--------
symbol      |   string   |  true  | trading pair 
range            |	string  |	true  | K line type,support1M; 3M; 5M; 15M; 30M; 1H; 2H; 4H; 6H; 12H; 1D; 1W; 
size        |	int   |	true  | data size



**Response Content**

> Response:

```json
"datas":[
        [
            1581325200000,
            "9893.5",
            "9893.5",
            "9775",
            "9833",
            "8249154"
        ],
        [
            1581328800000,
            "9833.5",
            "9864.5",
            "9820.5",
            "9844.5",
            "4166873"
        ],
        ...
]
```

lines data structure：List<List>

eg：

    [
        [${timestamp}, ${opening market price}, ${highest price}, ${lowest price}, ${closing prices}, ${trading volume}]
    ]





## Public-Single Contract Market

This endpoint returns a snapshot of a single contract market.


**HTTP Request**

+ GET <code>/exchange/api/v1/future/market/ticker</code>

**Request Parameter**


Parameter            |  Datatype  |Required| Description 
----------------|------------|--------|--------
symbol      |   string   |  true  | trading pairs 

**Response Content**

> Response:

```json
"datas":{
        "mt":4,
        "ai":2,
        "ci":999999,
        "sb":"BTC_USDT",
        "td":20200410,
        "te":1586505026969341,
        "lp":"6933",
        "mq":"116",
        "nt":0,
        "op":"7282.5",
        "ph":"7315.5",
        "pl":"6872",
        "hph":"13968.5",
        "hpl":"86",
        "tt":"555812827.765",
        "tv":"7780374",
        "tbv":"0",
        "tav":"0",
        "pp":"7282",
        "cp":"6934.03752",
        "pv":"8096351",
        "pcr":"-0.047991761071060762",
        "pc":"-349.5",
        "lui":0,
        "cs":2,
        "dp":"0",
        "fr":"0",
        "pfr":"0",
        "pi":"-0.000221735171689697",
        "ppi":"-0.000157435804943317",
        "fb":"0",
        "ts":0,
        "sl":0,
        "ip":"6934.03752",
        "w24pc":"-391.5",
        "w24pcr":"-0.053450747491296334",
        "volumeUsd24h": "293153245.992",
        "currencyName":"USDT",
        "commodityName":"BTC",
        "contractUnit": "1.000000000000000000",
        "bids":[
            ["6932.5","4556"],
            ["6932", "14112"],
            ...
        ],
        "asks":[
            ["6933", "15421"],
            ["6933.5","13339"],
            ...
        ]
    },
```

Field    | Datatype | Description 
------------|-----------|-----------
mt  | number  | messageType 
ai  | number  | application ID：applId 
ci  | number  | contractId 
sb  | string  | contract symbol 
td  | number  | tradeDate 
te  | number  | trading time 
lp  | string  | lastPrice 
mq  | stri  | matchQty 
nt  | numbe  | numTrades 
op  | string  | openPrice 
ph  | string  | priceHigh 
pl  | string  | priceLow 
hph  | string    | historyPriceHigh             
hpl  | string  | historyPriceLow 
tt  | string  | totalTurnover 
tv  | string  | totalVolume 
volumeUsd24h  | string  | total Turnover of 24h convert USD
currencyName  | string  | Name of settlement currency
commodityName  | string  | Name of commodity currency
contractUnit  | string  | unit of contract. e.g. 0.01btc
pv  | string  | The open interest in the last 24 hours in contracts
tbv  | string  | totalBidVol 
tav  | string  | totalAskVol 
pp  | string  | prevPrice 
cp  | string  | clearPrice 
pcr  | string  | priceChangeRadio 
pc  | string  | priceChange 
lui  | number  | lastUpdateId 
cs  | number  | contractStatus 
dp  | string  | deliveryPrice 
fr  | null  | fundingRate 
pfr  | null  | predictionFundingRate 
pi  | null  | premiumIndex 
ppi  | null  | predictionPremiumIndex 
fb  | string  | fairBasis 
ts  | number  | trading signal, 1=buy, 2=sell 
sl  | number  | 1~100, signalLevel 
ip  | string  | indexPrice 
bids  | string[]  | buy gear
asks  | string[]  | sell gear 


bids and asks structure：List<List>
eg：

    [
        [${price}, ${Qty}]
    ]



## Public-All Contract Market

The endpoint returns a snapshot of all contracts markets

**HTTP Request**

+ GET <code>/exchange/api/v1/future/market/tickers</code>

**Request Parameter**

None

**Response Content**

> Response:

```
"datas":[
    {}
]
```

Returns a list with the specific meaning of each field of the subitem. Please refer to the [Public-Single Contract Market](#public-single-contract-market) interface.



## Public-Contract Depth

This endpoint returns the current depth data for the specified contract.

**HTTP Request**

+ GET <code>/exchange/api/v1/future/market/depth</code>

**Request Parameter**

Parameter            |  DataType  |Required| Description 
----------------|------------|--------|--------
symbol      |   string   |  true  | trading pairs 
size        |	integer   |	true  | gears, default 20 


**Response Content**

> Response:

```json
"datas":{
        "bids":[
            [
                "9034",
                "1889"
            ],
            [
                "9033.5",
                "3126"
            ],
            [
                "9033",
                "11536"
            ]
        ],
        "asks":[
            [
                "9043.5",
                "8332"
            ],
            [
                "9044",
                "16162"
            ],
            [
                "9044.5",
                "10041"
            ]        ],
        "timestamp":1588754025882
    }   
}
```

字段名称            | 数据类型  |	描述
--------------------|-----------|--------
asks             |	list  |	sell gear, order by price asc
bids          |	list  |	buy gear, order by price desc
timestamp               |	long  |	Request timestamp, millisecond level




## Public-Contract Trading History

This endpoint returns the latest trading record for the specified contract market.


**HTTP Request**

+ GET <code>/exchange/api/v1/future/market/trades</code>

**Request Parameter**

Parameter            |  Data type  |Required| Description 
----------------|------------|--------|--------
symbol      |   string   |  true  | contract market


**Response Content**

> Response:

```json
"datas": [
        [1586504611360280, "6921.5", "12", -1],
        [1586504615863710, "6920.5", "79", 1],
        [1586504620979503, "6920.5", "16", 1],
    ......
]
```

trades structure：List<List>

Eg：
        
    [
      [${time stamp}, ${trading price}, ${trading volume}, ${direction}]
    ]


# Contract Trade Interface

## Place an Order

**HTTP Request**

+ POST <code>/exchange/api/v1/future/place</code>

**Request Parameter**

Parameter            |  DataType  |Required| Description 
----------------|------------|--------|--------
clientOrderId      |   string   |  no  | client order id (unique) 
symbol      |   string   |  yes  | contract name 
side  | integer  | yes | 1: buy, -1: sell
price  | string  | no | not required when order_type=3（market price) 
quantity  | string  | yes | Order Qty 
orderType  | integer  | yes | 1（price limit），3（market price） 
positionEffect  | number  | yes | 1: position，2: close position 
marginType  | number  | yes | 1: Cross，2: Isolated
marginRate  | string  | yes | Cross=0, Isolated>=0，margin rate 
orderSubType  | integer  | no | 0（default market），1（passive intrust），2（limit intrust），<br/>3（take Profit intrust），4（stop loss intrust） 
stopPrice  | string  | no | trigger price 
stopCondition  | integer  | no | 0 default，2：stop-loss 
minimalQuantity  | string  | no | stop-loss point 

Note：
    
Implemented order type：<br/>
passive intrust：<br/>
order_sub_type: 1
    
Contingent order：<br/>
order_sub_type: 2（limit intrust），3（take Profit intrust），4（stop loss intrust）<br/>
stop_price: trigger price
    
Tracking stop-loss：<br/>
order_sub_type: 2（limit intrust），3（take Profit intrust），4（stop loss intrust）<br/>
minimal_quantity：Stop-loss point<br/>
stop_condition: 2（Stop-loss）

**Response Content**

> Response:

OrderId is returned on success.

```json
"datas":"11585489047547210"
```




## Cancel an Contract Order

**HTTP Request**

+ POST <code>/exchange/api/v1/future/cancel</code>

**Request Parameter**

Parameter            |  DataType  |Required| Description 
----------------|------------|--------|--------
symbol      |   string   |  yes  | contract id 
orderId  | String  | yes      | contract order id 


**Response Content**

None
  


## Cancel All Contract Orders

**HTTP Request**

+ POST <code>/exchange/api/v1/future/cancel-all</code>

**Request Parameter**

None

**Response Content**

None



## Get Contract Position 


position > 0 means long，position <0 means short

contract_side ：usdt contract  or coin-base contract

value = PositionQty * LatestPrice* ContractUnit

openPositionPrice = position / (positionQty * ContractUnit)

IndexPrice： request market price

Forced Liquidation price：

    USDT long position：openPositionPrice + position * (initialMarginRate - maintenanceMarginRate) / positionQty.
    
    USDT short position：openPositionPrice - position * (initialMarginRate - maintenanceMarginRate) / positionQty.
    
    Reverse long position：openPositionPrice + position * (initialMarginRate - maintenanceMarginRate) / positionQty.
    
    Reverse short position：openPositionPrice + position * (initialMarginRate - maintenanceMarginRate) / positionQty.

Deposit：  initial margin

multiple： 1/initialMarginRate

Usdt contract：

    Unrealized profit and loss when long position = positionQty *（latestPrice - open price）* ContractUnit；
    
    Unrealized profit and loss when short position = positionQty *（latestPrice - open price）* ContractUnit；
    
Reverse contract：

    Unrealized profit and loss when short position = positionQty *（latestPrice - openPrice）* ContractUnit；
    
    Unrealized profit and loss when long position = positionQty *（openPrice - latestPrice）* ContractUnit；

return rate：(profit or loss)／ value

deduct position queue： inquiry through deduct position queue

Operation queue： when posiStatus=2，can not close position or close according market price

already closed position： Realized profit and loss，Read-Out Values： closeProfitLoss

**HTTP Request**

+ GET <code>/exchange/api/v1/future/positions</code>

**Request Parameter**

None

**Response Content**

> Response:

```json
"datas":[
    {
        "contractId":999999,
        "posiQty":"-100",
        "openAmt":"7296.7444444444444444",
        "initMargin":"72.967444444444444444",
        "posiStatus":0,
        "marginType":1,
        "closeProfitLoss":"17.75555555555555554",
        "initMarginRate":"0.01",
        "maintainMarginRate":"0.005",
        "frozenCloseQty":"0",
        "frozenOpenQty":"0",
        "extraMargin":"0",
        "contractUnit":"0.01"
    },
    ...
]
```

Field    | DataType | Description 
----------------|------------|--------
contractId  | integer  | trading pair 
posiQty  | string  | position 
openAmt  | string  | open position amount 
initMargin  | string  | initial margin 
posiStatus  | number  | 0: normal，1: forbid close position 2: settlement 
marginType  | integer  | margin type，1: Cross 2: Isolated
closeProfitLoss  | string  | already realized benefit and loss 
initMargiRate  | string  | original margin 
maintainMarginRate  | string  | maintenance margin rate 
frozenCloseQty  | string  | frozen Qty for close position entrust 
frozenOpenQty  | string  | frozen Qty for open position entrust 
contractUnit  | string  | contract unit 


## Get Activity Orders

**HTTP Request**

+ GET <code>/exchange/api/v1/future/orders</code>

**Request Parameter**

None

**Response Content**

> Response:

```json
"datas":[
    {
        "applId":2,
        "contractId":999999,
        "accountId":63513,
        "clOrderId":"9a9d444c6aa54eada3cbd5d42b2718b6",
        "side":1,
        "orderPrice":"7260",
        "orderQty":"100",
        "orderId":"11585489048447590",
        "orderTime":1586485163425147,
        "orderStatus":2,
        "matchQty":"0",
        "matchAmt":"0",
        "cancelQty":"0",
        "matchTime":0,
        "orderType":1,
        "timeInForce":0,
        "feeRate":"0",
        "markPrice":null,
        "avgPrice":"0",
        "positionEffect":1,
        "marginType":1,
        "initMarginRate":"0.01",
        "fcOrderId":"",
        "contractUnit":"0.01",
        "stopPrice":"0",
        "orderSubType":0,
        "stopCondition":0,
        "minimalQuantity":null
    },
    ...
]
```


FieldName    | DataType | Description 
----------------|------------|--------
contractId  | number  | trading pairs 
orderId  | string    |  order ID 
clOrderId  | string   | customer order ID 
price  | string    | entrust price 
quantity  | string    | entrust Qty. 
leftQuantity  | string    | left Qty. 
side  | number    | sell or buy，1: buy，-1: sell 
placeTimestamp  | number    | entrust time 
cancelQty  | string    | volume of trade 
matchAmt  | string    | match amount 
positionEffect  | integer    | open/close position sign，1: open position 2: close position 
marginType  | integer    | margin type，1: Cross 2: Isolated
fcOrderId  | string   | force close position ，blank means force close position 
orderType  | integer  | entrust type（1：price limit，2：market price limit） 
stopPrice  | string  | condition oder triggered price 
orderSubType  | string  | Order type，0：default，1：passive intrust，2：limit intrust，<br/>3 ：take Profit intrust，4：stop loss intrust 
stopCondition  | integer  | 0：default，2：stop loss 
minimalQuantity  | string  | stop-loss price 

## Get Historical Orders

**HTTP Request**

+ GET <code>/exchange/api/v1/future/orders/his</code>

**Request Parameter**


 Parameters | DataType | Required | Description 
----------------|------------|--------|--------
symbol      |   string   |  yes  | contract name 
side  | int  | no | -1:last page，1：next page 
timestamp  | long  | no | Microsecond level 
pageSize  | int  | no | default 100 

       
Note<br/>
when users choose the enquiry conditions：<br/>
when users choose last page，next page，timestamp which must have a value<br/>

Last Page：<br/>
   timestamp = the first data’s timestamp in the current page<br/>
Next Page：<br/>
   timestamp = the last data’s timestamp in the current page<br/>

**Response Content**

> Response:

```json
"datas":[
    {
        "applId":2,
        "timestamp":1586485163425147,
        "userId":63513,
        "contractId":999999,
        "uuid":"11585489048447590",
        "side":1,
        "price":"7260",
        "quantity":"100",
        "orderType":1,
        "orderSubType":0,
        "timeInForce":1,
        "minimalQuantity":"0",
        "stopPrice":"0",
        "stopCondition":0,
        "orderStatus":4,
        "makerFeeRatio":"0",
        "takerFeeRatio":"0",
        "clOrderId":"9a9d444c6aa54eada3cbd5d42b2718b6",
        "filledCurrency":"7260",
        "filledQuantity":"100",
        "canceledQuantity":"0",
        "matchTime":1586485266453913,
        "positionEffect":1,
        "marginType":1,
        "marginRate":"0.01",
        "fcOrderId":"",
        "deltaPrice":"0",
        "frozenPrice":"7260"
    },
    ...
]
```

FieldName    | DataType | Description 
----------------|------------|--------
applId  | number  | 2：future 
timestamp  | number  | order timestamp，microsecond level 
userId  | number  | contract user ID 
contractId  | number  | trading pairs 
uuid  | string  | Unique identifier 
side  | number  | buy or sell，1: buy，-1: sell 
price  | string  | entrust price 
quantity  | string  | entrust volume 
orderType  | number  | entrust type（1：limit price，2：market price） 
orderSubType  | number  | order entrust type，0：default，1：passive intrust，2：limit intrust，<br/>3 ：take Profit intrust，4：stop loss intrust 
timeInForce  | number  | order validity type：1: valid before cancel，2:Immediate orders are complete，otherwise cancelled, not enabled，<br/>3:All orders are completed, otherwise cancelled, not enabled，4:five gears’ oders completed，others cancell，not enabled，5:five gears’ oders completed，others turns into price  limit order，not enabled 
minimalQuantity  | string  | stop-loss 
stopPrice  | string  | condition order trigger price 
stopCondition  | number  | Stop-loss ，stop-profit sign 1（stop profit，not enabled），2（stop loss，not enabled），3（only deduct position，not enabled） 
orderStatus  | number  | intrust status 
makerFeeRatio  | string  | Maker fee rate 
takerFeeRatio  | string  | Taker fee rate 
clOrderId  | string  | users’order number 
filledCurrency  | string  | trading amount 
filledQuantity  | string  | trading Qty 
canceledQuantity  | string  | cancelled Qty 
matchTime  | number  | match timestamp 
positionEffect  | number  | Open position sign，1：open position，2：close position 
marginType  | number  | margin type，1: Cross 2: Isolated
marginRate  | string  | margin rate，when full position=0，when position by position>=0， 
fcOrderId  | string  | force close position sign，if it's not empty，then means force close position entrust 
deltaPrice  | string  | spread between index price and intrust price 
frozenPrice  | string  | asset accumulate price 


orderStatus：

status | Description
---|---
 0|not been declared
 1|declaring
 2|declared but unsettled
 3|settled partly
 4|all settled
 5|cancel partly
 6|all cancel
 7|cancelling
 8|invalid
 11|save the cache of the intrust order which higher than condition
 12|save the cache of the intrust order which lower than condition



## Get Contract Force Close History

**HTTP Request**

+ GET <code>/exchange/api/v1/future/fcorders</code>

**Request Parameter**
       
Parameter            |  Data type  |Required| Description 
----------------|------------|--------|--------
symbol      |   string   |  no  |	contract anme
side  | int  | no | buy or sell 1: buy，-1: sell 
startDate  | long  | no | start time, microsecond 
endDate  | long  | no | end time, microsecond 
pageNum  | int  | no | current page：default：1 
pageSize  | int  | no | default 10 

**Response Content**


Field    | DataType | Description 
----------------|------------|--------
totalRow  | number  | total record 
totalPage  | number  | toral pages 
pageSize  | number  | page returned records 
pageNum  | number  | current page 
list  | object []  | 
├─timestamp  | number  | entrust time 
├─userId  | number  | user ID 
├─contractId  | number  | trading pairs 
├─uuid  | string  | entrust No. 
├─applId  | number  | apply id 
├─side  | number  | buy and sell direction 
├─bankruptcyPrice  | number  | bankruptcy price 
├─closeQty  | number  | entrust qty. 
├─filledCurrency  | number  | deal settled qty 
├─filledQuantity  | number  | deal settled qty 
├─canceledQuantity  | number  | cancelled qty 
├─orderStatus  | number  | entrust status 
├─feeRatio  | number  | trading fee ratio 
├─marginType  | number  | margin type 

    orderStatus
    0: undeclared；1:declaring；2:declared but not settled yet；3:partly settled；4:all settled；5部partly cancelled；6:all cancelled；7:cancelling；8:invalid


## Get Contract Force Deduction History

**HTTP Request**

+ GET <code>/exchange/api/v1/future/florders</code>

**Request Parameter**


Parameter            |  Data type  |Required| Description 
----------------|------------|--------|--------
symbol      |   string   |  no  | contract name 
side  | int  | no | buy and sell 1: buy，-1: sell 
startDate  | long  | no | Start time, microsecond 
endDate  | long  | no | end time ,microsecond 
pageNum  | int  | no | current page：default：1 
pageSize  | int  | no | default 10     


**Response Content**

Field    | DataType | Description 
----------------|------------|--------
totalRow  | number  | total record 
totalPage  | number  | total pages 
pageSize  | number  | pages returned record numbers 
pageNum  | number  | current page 
list  | object []  | 
├─contractId  | number  | trading pairs ID 
├─uuid  | string  | entrust order No. 
├─applId  | number  | apply sign 
├─lossUserId  | number  | Force deduction user（loss side） 
├─profitUserId  | number  | Force deduction user（profit side） 
├─execId  | string  | Deal No. 
├─side  | number  | sell and buy 
├─timestamp  | number  | entrust time 
├─lossMarginType  | number  | force deduction user（loss side）margin type 
├─profitMarginType  | number  | force deduction user（profit side）margin type 
├─closePrice  | number  | force deduction price 
├─closeQty  | number  | force deduction qty 
├─closeAmt  | number  | force deduction amount 
├─lossSubsidy  | number  | loss side subsidy 
├─profitSubsidy  | number  | profit side subsidy 



## Adjust Margin Rate

**HTTP Request**

+ POST <code>/exchange/api/v1/future/adjust-margin-rate</code>

**Request Parameter**

Parameter            |  Data type  |Required| Description 
----------------|------------|--------|--------
symbol      |   string   |  yes  | contract name 
initMarginRate  | string  | yes | original margin 
marginType  | int  | yes | margin type，1: Cross 2: Isolated
       


**Response Content**

None


## Adjust Margin

**HTTP Request**

+ POST <code>/exchange/api/v1/future/adjust-margin</code>

**Request Parameter**

Parameter            |  DataType  |Required| Description 
----------------|------------|--------|--------
symbol      |   string   |  yes  | contract name 
margin  | string  | yes | margin 

**Response Content**

None



# Contract Asset

## Get Contract Asset Flow

**HTTP Request**

+ GET <code>/exchange/api/v1/future/assets/flow</code>

**Request Parameter**

Parameter            |  Data type  | Required | Description 
----------------|------------|--------|--------
currencyName      |   string   |  no  | currency name 
flowId  | long  | no | flowID 
flowType  | long  | no | business type  
side  | int  | no | -1: last page，1：next page 
startTime  | long  | no | start time，microseconds 
endTime  | long  | no | end up time，microseconds 
pageSize  | int  | no | default 10 
   

when last page：side = -1  flowId is the first data of this page flow_id
when next page：side =1  flowId is the last data of this page flow_id

flowType：

 * 3001：future trading fees，
 * 3002：future trading fees deduct refund，
 * 3003：future trading fees deduct coupon consumption，
 * 3004：loss and benifits of future closing position，
 * 3005：The profit and loss of futures in regular settlement ，
 * 3006：contract settlement trading fee，
 * 3007：rate & profit and loss of future settlement，
 * 3008：risk fund
 * 8001：asset transfer   

**Response Content**

> Response:

```json
"datas":[
    {
        "flowId":1586329715655317,
        "userId":"7eOUtLBFXTU",
        "accountId":63513,
        "currencyId":999999,
        "currencyName":"zusd",
        "flowType":3004,
        "num":"36.74444444444444445",
        "occureTime":1586485266453705,
        "createTime":1586485266485605,
        "uuid":"11585489048448709",
        "note":"999999"
    },
    {
        "flowId":1586329715643559,
        "userId":"7eOUtLBFXTU",
        "accountId":63513,
        "currencyId":999999,
        "currencyName":"zusd",
        "flowType":3004,
        "num":"-12.255555555555555567",
        "occureTime":1586429992480700,
        "createTime":1586429992507735,
        "uuid":"11585489047547536",
        "note":"999999"
    },
    ...
]
```

FieldName    | DataType | Description 
----------------|------------|--------
flowId  | number  | flow ID 
userId  | number  | user ID 
applId  | number  | 2
currencyId  | number  | currency ID 
flowType  | number  | flow type 
num  | number  | amount 
occureTime  | number  | time of transfer 
createTime  | number  | record create time                                  
uuid  | string  | corresponding record id（transfer id、deal id eg.） 
note  | string  | notes 


## Get Available assets

**HTTP Request**

+ GET <code>/exchange/api/v1/future/assets/available</code>

**Request Parameter**

None

**Response Content**

> Response:

```json
"datas":[
    {
        "currencyId":999999,
        "totalBalance":"10016.25555555555555554",
        "available":"9870.538111111111111096",
        "frozenForTrade":"72.75",
        "initMargin":"72.967444444444444444",
        "frozenInitMargin":"72.75",
        "closeProfitLoss":"16.25555555555555554",
        "currencyName":"zusd"
    },
    ...
]
```

FieldName    | DataType | Description 
----------------|------------|--------
currencyId  | number  | currency ID 
currencyName  | String  | currency 
totalBalance  | string  | total assets 
available  | string  | available assets 
frozenForTrade  | string  | frozen assets of entrust 
initMargin  | string  | assigned margin 
frozenInitMargin  | string  | frozen margin for intrust 
closeProfitLoss  | string  | profit and loss of the close position 
createTime  | number  | create record time 



## Get contract Loss and benefit history

**HTTP Request**

+ GET <code>/exchange/api/v1/future/assets/profits</code>

**Request Parameter**
       
Parameter            |  DataType  |Required| Description 
----------------|------------|--------|--------
currencyName      |   string   | no       | currency name 
startDate  | string  | no | start time, eg. ‘2018-01-01’ 
endDate  | string  | no | end time, eg. ‘2018-01-01’ 
pageNum  | int  | no | currency page：default 1
pageSize  | int  | no | default 10 


**Response Content**

> Response:

```json
"datas":{
    "totalRow":3,
    "totalPage":1,
    "pageSize":10,
    "pageNum":1,
    "list":[
        {
            "userId":63513,
            "currencyId":999999,
            "totalMoney":"10000.000000000000000000",
            "orderFrozenMoney":"0E-18",
            "closeProfitLoss":"-20.488888888888888910",
            "startDate":1586361600000,
            "endDate":253402185600000
        },
        {
            "userId":63513,
            "currencyId":999999,
            "totalMoney":"10000.000000000000000000",
            "orderFrozenMoney":"0E-18",
            "closeProfitLoss":"-8.233333333333333343",
            "startDate":1586275200000,
            "endDate":1586275200000
        }
    ]
}
```

Field    | Data type | Description 
----------------|------------|--------
totalRow  | number  | total record amount 
totalPage  | number  | total page 
pageSize  | number  | page return number 
pageNum  | number  | current page 
list  | object []  | 
├─userId  | number  | user ID 
├─currencyId  | number  | currency ID 
├─totalMoney  | number  | total asset 
├─orderFrozenMoney  | number  | frozen asset 
├─closeProfitLoss  | number  | realized profit and loss 
├─startDate  | number  | start time 
├─endDate  | number  | end time 

## Transfer margin between Spot account and Future account

This interface is used to transfer assets between Spot account and Future account.

**HTTP Request**

+ POST <code>/exchange/api/v1/future/assets/transfer</code>

**Request Parameter**

Parameter       |  DataType  |Required| Description 
----------------|------------|--------|--------
currency      |   string   |  yes  |	currency name eg. btc
amount  | string  | yes  | transfer qty
side  | int  | yes  | transfer side，0: Transfer from Spot account to Future account 1: Transfer from Future account to Spot account


**Response Content**

> Response:

```json
"datas":"4b79ca9e72294e3898999af23769f05e"
```

return clientId

## Get Transfer History

The endpoint returns the record of asset transfer between spot account and future account.

**HTTP Request**

+ POST <code>/exchange/api/v1/future/assets/transfer-history</code>

**Request Parameter**

Parameter            |  DataType  |Required| Description 
----------------|------------|--------|--------
clientId      |   string   |  no  |	transfer id
currency      |   string   |  no  |	currency name
side  | int  | no  | transfer side，0: Transfer from Spot account to Future account 1: Transfer from Future account to Spot account
pageNum  | int  | no  | current page default 1
pageSize  | int  | no  | default 10
       
**Response Content**

> Response:

```json
"datas":{
    "totalRow":30,
    "pageNum":1,
    "pageSize":10,
    "pagePage":3,
    "list":[
        {
            "createdAt":"2020-04-23 21:11:03",
            "side":1,
            "amount":"1",
            "clientId":"2b8566ff7f274330a798dae4fcd1d494",
            "failMsg":"101080704:sub account does not exist",
            "currency":"ZUSD",
            "confirmTime":null,
            "state":"failure",
            "userId":"7eICyn8c5yq"
        },
        {
            "createdAt":"2020-04-23 21:10:12",
            "side":1,
            "amount":"1",
            "clientId":"03b4545e184f475ab16cbc528131a131",
            "failMsg":"101080704:sub account does not exist",
            "currency":"ZUSD",
            "confirmTime":null,
            "state":"failure",
            "userId":"7eICyn8c5yq"
        }
    ]
}
```

Field    | Data type | Description 
----------------|------------|--------
totalRow  | number  | total record amount 
totalPage  | number  | total page
pageSize  | number  | page return number 
pageNum  | number  | current page
list  | object []  | 
├─clientId  | string  | transfer id
├─userId  | number  | user id
├─currency  | string  | currency name
├─amount  | number  | transfer qty
├─side  | int  | transfer side，0: Transfer from Spot account to Future account 1: Transfer from Future account to Spot account
├─state  | string  | transfer status， confirming，completed，failure
├─createdAt  | string  | create time
├─confirmTime  | string  | transfer success time

<div style="text-align: right;font-size:0"> created by zhangzp at 2019-09-21 </div>
