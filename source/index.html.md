---
title: ZBG API Documentation

language_tabs: # must be one of https://git.io/vQNgJ
  - json

toc_footers:
  - <a href="https://www.zbg.fun" class="createApi">ZBG Exchange</a>

includes:

search: true
---

# Change Log


## 2020-03-31

Add [SDK](#sdk-and-demo) 

## 2019-12-09 

Modify the market interfaces：

* Add interface [[/exchange/api/v1/commom/trade-history](#public-get-the-historical-trades)], Add query historical transaction records
* Interface [[/api/data/v1/entrusts](#public-market-depth)], Enlarge the depth of the trading page
* Interface [[/api/data/v1/trades](#public-get-the-last-trade)], Increases the number of queries available

## 2019-11-21 

* Interface [[/exchange/api/v1/account/balance](#get-account-balance)], Add the balance return field
* Add interface [[/exchange/api/v1/account/balance/{currency}](#get-account-balance-for-a-single-currency)], Get the account balance of the specified currency
* Modify the signature，Add [API Passphrase](#signature-authentication) in signature rule
* Add JS signature invoking code

## 2019-10-09 

* Add interface [[/exchange/api/v1/account/search](#search-account)], Add account search
* Add websocket module
* Interface [[/exchange/api/v1/common/symbols][symbols]] adds return field ID
* Interface [[/exchange/api/v1/common/currencys][currencies]] adds return field ID

## 2019-09-21 

Create the document


# Introduction

## API Introduction

Welcome to the ZBG API!     

You can use this API to access the platform's market data, trade, and manage your account.

The interface path and field naming in this API document fit the industry's mainstream usage,that makes it more easier for programs to switch among the other header exchanges.       

[Previous version](/help/restApi) All the interfaces were covered,but somewhat were inconsistent with the industry's mainstream usage.

You can view code examples in the dark area to the right.

<aside class="notice">
Any problems during use, please contact us: QQ group:829230107
</aside>

## Sub-account

Sub-accounts can be used to isolate assets and transactions, and assets can be transferred between the main-account and sub-accounts.Sub user can only trade with the sub account, and assets between sub-accounts cannot be transferred directly, only the main account has the transfer authority.

The sub-account has its own login password and API Key, they are managed under main-account in website.


***Sub-account Function Instruction and Usage***

1.The main account can transfer part of the funds to the sub-account (no commission), and the assets of the sub-account can be managed independently.       
2.Users may authorize other agencies or personnel to manage the sub-accounts.       
3.The user can trade with the sub-account through the API (the API can only be opened and closed through the main account).      
4.The following operations cannot be carried out in the sub-account: OTC, withdraw, setting and changing passwords, bind email and phone number,etc.

The sub-account can access all public interfaces, including basic information and market conditions. The private interfaces that the sub-account can access are as follows:

Interface | Instruction
----------| ------------
[GET /exchange/api/v1/account/balance](#get-account-balance)|account balance
[POST /exchange/api/v1/order/create](#place-a-new-order)|place an order
[POST /exchange/api/v1/order/cancel](#submit-cancel-for-an-order)|Submit Cancel for an Order
[POST /exchange/api/v1/order/batch-cancel](#submit-cancel-for-multiple-orders-by-criteria)|Submit Cancel for Multiple Orders by Criteria
[GET /exchange/api/v1/order/open-orders](#get-open-orders)| Query open orders
[GET /exchange/api/v1/order/orders](#search-historical-orders)|Search Historical Orders
[GET  /exchange/api/v1/order/detail](#get-the-order-detail-of-an-order)| Get the Order Detail of an Order
[GET /exchange/api/v1/order/trades](#get-the-match-result-of-an-order)| Get the Match Result of an Order

<aside class="notice">
All other APIs couldn't be accessed by sub user, otherwise the API will return "code 6041".
</aside>


# Access Instructions

## Access URLs

REST API

<code>https://www.zbg.com</code>

Kline API

<code>https://kline.zbg.com</code>

WebSocket 

<code>wss://kline.zbg.com/websocket</code>


## SDK and Demo

SDK (Suggested)

[Java](https://github.com/zbgapi/zbg-api-v1-sdk/tree/master/zbg-java-sdk-api)
| 
[Python](https://github.com/zbgapi/zbg-api-v1-sdk/tree/master/zbg-python-sdk-api)
| 
[ccxt](https://github.com/zbgapi/ccxt)


## Interface Type
In this section we divides interfaces into two types：

- Public interface
- Private interface

**Public Interface**

The public interface can be used to obtain configuration information and market data and the public request can be invoked without authentication

**Private Interface**

The private interface can be used for order management and account management.Each private request must be signed using a canonical form of authentication.

The private interface needs to be validated using your API key.You can generate API key <a href="/u/api" class="createApi">here</a>.


## Request Format

**All API requests are made as GET or POST**

**For GET requests, all parameters are in the path parameters;For POST requests, all parameters are sent as JSON in the request body**



## Response Format

```json
{
"datas":,// The body data in response
"resMsg":
    {
        "code":"1",          // error code
        "message":"success!" // prompt message
    }
    
}
```

All the interface returns are in JSON format and at the top of the JSON there are several fields that represent the response status and properties :"code" and "message" In the "datas" field, the return code of "1" means that the request was successful, while the rest means that the request failed. Please refer to the [error code list](#error-code) for details.



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
        // If the user sets the API Key when creating it, pass the parameters:
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
     * post request interface，ContentType ,It's all in the appliation/json way
     *
     * @param url   request interface
     * @param param required parameter
     * @return repsonse information
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
 * Generates a summary for the byte array
 *
 * @param message   string
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

> StringKit.toHex() The method is as follows：

```java
/**
 */ initialize an array of characters to hold each hexadecimal character
 */
public static final char[] HEX_DIGITS = {'0', '1', '2', '3', '4', '5', '6', '7', '8', '9', 'A', 'B', 'C', 'D', 'E', 'F'};
/**
 * turn the hexadecimal string
 *
 * @param bytes bytecode array
 * @return 16   return hexadecimal string
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

> **JS Signature Call Example：**

```js
// The GET method requests a signature, taking the interface for querying historical orders as an example.

//Introduce the axios module
const axios = require('axios')
//Introduce the MD5 module
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
    url: 'https://www.zbgpro.net/exchange/api/v1/order/orders',
    headers: headers,
    method: 'GET',
    timeout: 3000,
    params: data
};

//Parameter content string is used to sort all parameters by the first letter of key value before pressing
Key1 + value1 + key2 + value2...Form string
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
//Introduce the axios module
const axios = require('axios')
// Introduce the MD5 module
const MD5 = require('MD5')

//request data
var data = {
    symbol: 'zt_usdt',
    side: 'buy',
    amount: 1,
    price: 0.038
}
var timestamp = (new Date()).getTime();
//request header
var headers = {
    Apiid: 'api key', // 替换成自己的 api key
    Clienttype: 5,
    Timestamp: timestamp,
    // If the user sets the API Key when creating it, it must be passed
    // Replace passphrase in the signature
    Passphrase: MD5(timestamp.toString() + 'passphrase')
    // Signature order :Apiid+ timestamp + request parameter +Apisecret
    // Replace the API key and API secret in the signature
    Sign: MD5('api key' + timestamp.toString() + JSON.stringify(data) + 'api secret')
};
var config = {
    url: 'https://www.zbgpro.net/exchange/api/v1/order/create',
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

+ *Interface Address*       : https://www.zbg.com/exchange/api/v1/order/orders
+ *API Access Key*          : The Access Key in the API Key 
+ *Signature Method*        : The Hash method that is used to sign, it uses md5
+ *Timestamp*               : The millisecond timestamp of the time you made the request, located in the Header
+ *Parameters*              : Each API method has a group of parameters, you can refer to detailed document for each of them.. 
+ *Signature*               : The value after signed, it is guarantee the signature is valid and the request is not be tempered.

That is, the request header must contain the following content：

*Apiid* : API Key of string type

*Timestamp* : The timestamp of the request

*Sign* : Signature string (Sign according to the reference format)

*Passphrase* : The passphrase that you specified when you created the API key. This field is not required, depending on whether the user specified it when creating the API Key.

<aside class="notice">
Passphrase request headers are strings that are processed by *md5(timestamp+passphrase)* instead of directly using Passphrase to request a password
</aside>

 
**Create API Key** 

You can create API Key <a href="/u/api" class="createApi">here</a>.

API key including the following three parts:

+ Access Key : API Access key
+ Secret Key : The key used for signature authentication encryption (visible on application only)
+ Passphrase : API Access command

The API key and secret Key will be randomly generated and provided by ZBG, and Passphrase will be provided by you to ensure the security of API access.

Passphrase for the optional field users can choose to provide the field according to their own needs, if the field is filled in, the request header must be put on when accessing the interface, the background system will carry out the verification.

<aside class="notice">
API key it has all operation rights including trading, depositing and withdrawing currency.
</aside>

<aside class="warning">
These two keys are closely related to account security and should not be disclosed to others at any time.If you find your API Key is disposed, please remove it immediately.
</aside>

**Timestamp**

The request header is a long integer timestamp at the millisecond level, and requests that differ by more than 60 seconds between the timestamp and the server time are considered expired and rejected by the system. If you think there is a significant time bias between the server and the API server, we recommend that you use the [fetch server time interface](#public-get-current-system-time)  to query the API server time 

**Signature**

Take the example of querying an order list

<p><code>https://www.zbg.com/exchange/api/v1/order/orders?symbol=zt_ust&side=buy&from=1&size=100</code></p>

1.Sort the parameters (keys) in the order of the ASCII code, such as:
<p><code>from=1</code></p>
<p><code>side=buy</code></p>
<p><code>size=100</code></p>
<p><code>symbol=zt_usdt</code></p>

2.Concatenate the strings in the order above.

<p><code>from1sizebuysize100sumbolzt_usdt</code></p>

3.Use the request string from the previous step and your key to generate a digital signature : `MD5(key + timestamp + signature + secret)`

<p><code>5fcbdb0862e10f9f6b885fdde42d58a1</code></p>

4.Add fields such as the generated digital signature App key timestamp to the Header

<p><code>header.put("Apiid",id);</code></p>
<p><code>header.put("Timestamp", String.valueOf(timestamp));</code></p>
<p><code>header.put("Passphrase", md5(timestamp + passphrase));</code></p>
<p><code>header.put("Sign", "5fcbdb0862e10f9f6b885fdde42d58a1");</code></p>

<aside class="warning">
Body mode *application/json* is slightly different for Post requests. In this case, there are no steps 1 or 2, so just enter step 3 using the body as a signature string
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
6400|The market is currently closed"|This market is not open!
6402|Your order quantity exceeds the maximum limit :%s
6403|Your order price exceeds the limit :%s~%s
6601|Bitbank does not have this currency
6602|Bitbank has insufficient balance in this currency. Please recharge first
6603|Current currency is forbidden to be recharged 
6894|The API signature is no longer valid!
6895|Failed to verify API permissions. Interface is not an authorization API6896|Failed to verify the API permission. The Userid is not matching with Apiid6897|Failed to verify the API permission. Please confirm whether to enable API permission
6898|Failed to verify the API permission for multiple times. Please confirm whether to enable API permission
6899|The market is temporarily not open to API trading
6900|The exchange server temporarily not open to API trading
6991|Incorrect price accuracy, up to %s digits in decimal places
6992|The quantity accuracy of the order is wrong, and the number of decimal places is up to %s digits
6993|The minimum order quantity of the order is wrong, the minimum amount is %s"
6999|access forbidden
    


# Reference Data

## Public-Get all Supported Trading Symbols

This endpoint returns all ZBG's supported trading symbols.

**HTTP Request**

+ GET <code>/exchange/api/v1/common/symbols</code>

**Request Parameter**

No parameter is needed for this endpoint.

**Response Content**

> Response:

```json
"datas":[
    {
        "id":"329",
        "symbol":"btc_usdt",
        "price-precision":1,
        "min-order-amt":"0.0001",
        "state":"online",
        "base-currency":"btc",
        "amount-precision":4,
        "max-order-amt":"",
        "quote-currency":"usdt",
        "symbol-partition":"main"
    },
    {
        "id":"330",
        "symbol":"eth_usdt",
        "price-precision":8,
        "min-order-amt":"0.99995",
        "state":"online",
        "base-currency":"eth",
        "amount-precision":8,
        "max-order-amt":"",
        "quote-currency":"usdt",
        "symbol-partition":"main"
    }
]
```

 Field          | Data Type |   Description
--------------------|----------|--------------
id                  |	string  |	Trading pair ID
base-currency       |	string  |	Base currency in trading pairs
quote-currency      |	string  |	The currencies quoted in the trading pair
price-precision     |	integer |   Quote currency precision when quote price(decimal places)
amount-precision    |	integer |	Base currency precision when quote amount(decimal places)
symbol-partition    |	string  |	Trade zone, possible values: [main, innovation]
symbol              |	string  |	Trading pairs
state               |	string  |   The status of the symbol; Possible values[online,offline,suspend] <br/>online - Has been launched；<br>offline - Trading pair has been offline, can not be traded；<br/>suspend -- Trading suspension 
min-order-amt       |	decimal  |	Trade on a minimum order 
max-order-amt       |	decimal  |	There is no limit on the maximum order quantity when the transaction is empty


## Public-Get all Supported Currencies

This endpoint returns all ZBG's supported trading currencies.

**HTTP Request**

+ GET <code>/exchange/api/v1/common/currencys</code>

**Required Parameter**

None

**Response Content**

> Response:

```json
"datas":[
    {
        "name":"zb",
        "daily-draw-limit":1000000,
        "draw-fee":"10.0",
        "draw-flag":true,
        "once-draw-limit":100000,
        "min-draw-limit":100
    },
    {
        "name":"usdt",
        "daily-draw-limit":200000,
        "draw-fee":"5.0",
        "draw-flag":true,
        "once-draw-limit":20000,
        "min-draw-limit":100
    }
]
```

Field       |  Data Type | Description
----------------|------------|--------
id              |   string   |  Currency ID
name            |   string   |	Name of the currency
draw-flag       |   boolean  |  Whether can withdraw
draw-fee        |   string   |	Withdraw Commission
once-draw-limit |   integer  |	Maximum withdrawal limit
daily-draw-limit|   integer  |	Maximum daily withdrawal limit
min-draw-limit  |   decimal  |	Minimum withdrawal limit





## Public-Get Current System Time

This endpoint returns the current system time in milliseconds adjusted to Beijing time zone.

**HTTP Request**

+ GET <code>/exchange/api/v1/common/timestamp</code>

**Request Parameter**

None

**Response Content**

> Response:

```json
"datas":1568812709229
```

Millisecond timestamp


## Public-Get Current Auxiliary Price

This endpoint returns the discount price of the specified currency in usd, qc and btc.

**HTTP Request**

+ GET <code>/exchange/api/v1/common/assist-price</code>

**Request Parameters**

Parameter      |  Data Type | Required |Description
----------------|------------|--------------|--------------
currencys      |   string   |    false     |Currencies，Multiple currencies to separate such as : zt,usdt,btc

**Response Content**

> Response:

```json
"datas":{
    "cny":{
        "usdt":"7.06",
        "eth":"705.6841"
    },
    "usd":{
        "usdt":"1.00000000",
        "eth":"99.95525496"
    },
    "btc":{
        "usdt":"0.0000983747",
        "eth":"0.0098330641"
    }
}
```

Return cny, usd, btc for key three maps, map content value：`<currency, price>`


## Public-Get the Historical Trades

This endpoint return the latest 80 transaction records.

**HTTP Request**

+ GET <code>/exchange/api/v1/common/trade-history/{symbol}</code>

> curl https://www.zbg.com/exchange/api/v1/common/trade-history/zt_usdt

Returns the maximum 1000 historical transaction records from [trade-id] onwards

> curl https://www.zbg.com/exchange/api/v1/common/trade-history/zt_usdt/T6609287828167733248

+ GET <code>/exchange/api/v1/common/trade-history/{symbol}/{trade-id}</code>

**Request Parameter**

Parameter      |  Data Type | Required |Description
----------------|------------|------------|--------
symbol          |   string   |  true   |    The name of the market
trade-id        |   string   |  false  |    The unique trade id


**Response Content**

> Response:

```json
"datas":[
    {
        "trade-id":"T6609287826112524288",
        "date":"2019-12-08 11:50:12",
        "side":"buy",
        "amount":"4377.2",
        "total":"166.77132",
        "price":"0.0381",
        "created-at":1575777012375
    },
    {
        "trade-id":"T6609287828167733248",
        "date":"2019-12-08 11:50:12",
        "side":"buy",
        "amount":"3500.2956",
        "total":"133.36126236",
        "price":"0.0381",
        "created-at":1575777012865
    }
]   
```

The data returned is a list, list item field：

Field  | Data Type |	Description
------------|-----------|-----------
trade-id    |   string   |	The unique trade id
price       |	decimal  |	The trading price in quote currency
side        |	string   |	Business type, buy/sell
amount      |	decimal  |	The trading volume in base currency
total       |	decimal  |	The trading volume in quote currency
created-at  |	long     |	Initiate timestamp, milliseconds
date        |	string   |	the starting time yyyy-MM-dd HH:mm:ss


# Market Data

## Public-Get Klines(Candles)

This endpoint retrieves all klines in a specific range.

**HTTP Request**

+ GET <code>https://kline.zbg.com/api/data/v1/klines</code>

**Request Parameter**

Parameter       |  Data Type  | Required | Description
----------------|------------|--------|--------
marketName      |   string   |  true  |	trading pair(symbol)
type            |	string  |	true  | Kline type, support 1M,5M,15M,30M,1H,1D,1W<br/> Seven types, representing 1-30 minutes,1 hour,1 day, and 1 week
dataSize        |	integer   |	true  |Returns the number of Kline data,[1,100


**Response Content**

Returns a double-layer list and a list in the inner layer is a piece of data

> Response:

```json
"datas":[
    [
        "K",            // Data type,K for Kline
        "90",           // Market ID, negligible
        "etc_usdt",     // trading pair (symbol)
        "1532181600",   // Timestamp, second level
        "826.7",        // Opening Price (open)
        "827.68",       // maximum price (high)
        "826.68",       // minimum price (low)
        "826.07",       // closing price (close)
        "492261",       // The trading volume in base currency (vol) 
        "0.04",         // amount of increase and amount of decrease
        "6.8",          // dollar currency rate
        "1H",           // Kline cycle
        "false"         // Whether or not the conversion
        "407073731.01"  // The trading volume in quote currency (amount) 
    ],
    ..........
]
```


## Public-Get Last Aggregate Ticker

This endpoint retrieves the latest ticker with some important 24h aggregated market data.

This data server is updated once every 10 seconds, so it does not need to get too frequently

**HTTP Request**

+ GET <code>https://kline.zbg.com/api/data/v1/ticker</code>

**Request Parameter**

Parameter       |  Data Type  |Required|Description
----------------|------------|--------|--------
marketName      |   string   |  true  |	trading pair

**Response Content**

> Response:

```json
"datas":[
    "386",              // market ID，negligible
    "0.291",            // close, Latest trading price
    "0.2972",           // high, top price
    "0.2848",           // low, minimum price
    "295240028.896",    // vol, 24-hour volume (in base currency)
    "-0.75",            // change, Rise and fall in 24 hours
    "[                  
        [
            1,          // serial number 
            0.2932      // closing price
        ], 
        [2, 0.2928], [3, 0.2923], [4, 0.292], [5, 0.2897], [6, 0.291]
    ]",
    "0.2899",           // bid, Buy 1，
    "0.2912",           // ask, Sell 1,
    "86114684.5935"     // amount, 24-hour turnover(in quote currency),
]
```

return string list，data declaration 

[ 
    marketId, 
    close, 
    high，
    low, 
    volume，
    change, 
    Latest 6H closing price list,
    bid，
    ask,
    amount
]

amount : Aggregated trading value during the interval (in quote currency)

volume : Aggregated trading volume during the interval (in base currency)
    
Latest 6H closing price listSort in chronological order，data declaration ：

[[serial number , closing price], [serial number , closing price], [serial number , closing price]]


## Public-Get Last Aggregate Tickers for All Pairs

This endpoint retrieves the latest tickers for all supported pairs.

The update speed of the data server is 10 seconds once, so it is unnecessary to get too frequently

**HTTP Request**

+ GET <code>https://kline.zbg.com/api/data/v1/tickers</code>

**Request Parameter**

The data format of turn is different when isUseMarketName is true and false. IsUseMarketName is recommended to pass true

Parameter       |  Data Type  |Required|Description
----------------|------------|--------|--------
isUseMarketName  |   boolean   |  true  |Parameters must be uploaded，true，turntrading pair

**Response Content**

> curl https://kline.zbg.fun/api/data/v1/tickers

> Response:

```json
"datas":[
    [
        "5162",
        "0.00000666",
        "0.00000706",
        "0.00000661",
        "19148563.46",
        "-5.53",
        "[[1, 0.00000662], [2, 0.00000662], [3, 0.00000662], [4, 0.00000663], [5, 0.00000664], [6, 0.00000666]]",
        "0.0000066",
        "0.00000707",
        "130.2387"
    ],
    [
        "5042",
        "0.000008",
        "0",
        "0",
        "0",
        "0.0",
        "[]",
        "0.000008",
        "0.000097",
        "0"
    ]
]
```

> curl https://kline.zbg.com/api/data/v1/tickers?isUseMarketName=true

> Response:

```json
"datas":{
    "XRP_USDT":[
        "386",              // market ID，negligible
        "0.291",            // close, Latest trading price
        "0.2972",           // high, top price
        "0.2848",           // high, top price
        "295240028.896",    // vol, 24-hour volume (in base currency)
        "-0.75",            // change, Rise and fall in 24 hours
        "[                  
            [
                1,          // serial number 
                0.2932      // closing price
            ], 
            [2, 0.2928], [3, 0.2923], [4, 0.292], [5, 0.2897], [6, 0.291]
        ]",
        "0.2899",           // bid, Buy 1，
        "0.2912",           // ask, Sell 1,
        "86114684.5935"     // amount, 24-hour turnover   (in quote currency)
    ],
    "GBC_QC":[
        "5031",
        "0.025",
        "0.026",
        "0.025",
        "955.2938",
        "-3.85",
        "[[1, 0.025]]",
        "0.025",
        "0.0394",
        "23.9463"
    ]
}
```

return list，The element in the list is a string list，

return string list，data declaration 

[ 
    marketId, 
    close, 
    high，
    low, 
    volume，
    change, 
    Latest 6H closing price list,
    bid，
    ask,
    amount
]

amount : Aggregated trading value during the interval (in quote currency)

volume : Aggregated trading volume during the interval (in base currency)

Latest 6H closing price listSort in chronological order，data declaration ：

[[serial number , closing price], [serial number , closing price], [serial number , closing price]]


## Public-Market Depth

This endpoint retrieves the current order book of a specific pair.

**HTTP Request**

+ GET <code>https://kline.zbg.com/api/data/v1/entrusts</code>

**Request Parameter**

Parameter       |  Data Type  |Required|Description
----------------|------------|--------|--------
marketName      |   string   |  true  |	trading pair
dataSize        |	integer   |	true  |The number of gears represents 5 gears for each trade, and the maximum is 200

**Response Content**

> Response:

```json
"datas":{
    "asks": [               // The selling
        [
            "824.898",      // The third gear The selling price
            "305.95"        // The third gear The selling quantity
        ],
        [
            "741.435",      // The second gear The selling price
            "400.88"        // The second gear The selling quantity
        ],
        [
            "741.47",       // The first gear The selling price
            "392.99"        // The first gear The selling quantity
        ] 
    ],
    "bids": [               // buy order  
        [
            "294",          // The first gear buy order  price
            "240.32"        // The first gear buy order  quantity
        ],
        [
            "247",          // The second gear buy order  price
            "242.064"       // The second gear buy order  quantity
        ],
        [
            "216",          // The third gear buy order  price
            "174.043"       // The third gear buy order  quantity
        ]
    ],
    "timestamp": "1532183394"   // Request timestamp, second level
}
```

Field  | Data Type |	Description
--------------------|-----------|--------
asks            |	list  |	The sell order list，order by price asc
bids            |	list  |	The buy order list，order by price desc
timestamp       |	long  |	Request timestamp, second level


## Public-Get the Last Trade

This endpoint returns the latest transaction records for trading pair.

**HTTP Request**

+ GET <code>https://kline.zbg.com/api/data/v1/trades</code>

**Request Parameter**

Parameter       |  Data Type  | Required | Description
----------------|------------|--------|--------
marketName      |   string   |  true  |	trading pair
dataSize        |	integer   |	false  | Data quantity, default 80, maximum 1000

**Response Content**


> Response:

```json
"datas": [
    [
        "T",            // data type       
        "90",           // market ID
        "1532183063",   // The timestamp
        "ETC_USDT",     // The name of the market
        "bid",          // ask and bid type 
        "827.90",       // deal price
        "515.50"        // deal quantity
    ],
    ......
]
```

return string list，data declaration 

    [ 
        DataType, 
        MarketID, 
        Timestamp，
        MarketName, 
        ask/bid,
        Price, 
        Volume
    ]


# Account

Accessing Accounts related interfaces such as None specifically requires signature authentication

## Get Account Information

This endpoint returns the current user information and its list of sub-accounts

**HTTP Request**

+ GET <code>/exchange/api/v1/account/accounts</code>

**Request Parameter**

None


**Response Content**

> Response:

```json
"datas":{
        "user-id":"7eJnwvugsCm",
        "login-name":"pzz_shangshi@126.com",
        "phone":null,
        "email":"pzz_shangshi@126.com",
        "type":"main",
        "certification":false,
        "mobile-auth":false,
        "email-auth":true,
        "google-auth":true,
        "safe-pwd-auth":true,
        "subs":[
            "7oAGIzveiwK",
            "7oAFrDdeD68",
            "7oAFp3hXCoi"
        ]       
}
```

Field  | Data Type |	Description
--------------------|-----------|--------
user-id             |	string  |	user ID
login-name          |	string  |	login name
phone               |	string  |	phone number
email               |	string  |	email
type                |	string  |	account type，main，sub，virtual
certification       |	boolean |	Whether real name authentication has been conducted
mobile-auth         |	boolean |	Whether or not the phone has been verified
email-auth          |	boolean |	Whether or not email validation has been performed
google-auth         |	boolean |	Whether or not Google validation is performed
safe-pwd-auth       |	boolean |	Whether a security password is set
subs                |   list    |   List of sub-account ID


## Search Account

This endpoint returns user information by account

**HTTP Request**

+ GET <code>/exchange/api/v1/account/search</code>

**Request Parameter**

Parameter       |  Data Type  | Required | Description
----------------|------------|--------|--------
account     |   string   |  true  |	require account
account-type     |	int   |	true  |	account，0:phone number，1:email，2:login name，3:wechat account
google-code     |	string   |	true  |	Google verification code
country-code     |	string   |	false  | when account-type=0 pass parameters

**Response Content**

> Response:

```json
"datas":{
    "auth":true,
    "email-auth":true,
    "safe-pwd-auth":false,
    "user-id":"7eU4wsh3XLE",
    "mobile-auth":true,
    "type":"main",
    "login-name":"18100275874",
    "google-auth":true
}
```

Field  | Data Type |	Description
--------------------|-----------|--------
user-id                  |	string  |	user ID
login-name          |	string  |	login name
phone               |	string  |	phone number
email               |	string  |	email
type                |	string |	account type，main，sub，virtual
certification       |	boolean |	Whether real name authentication has been conducted
mobile-auth         |	boolean |	Whether or not the phone has been verified
email-auth          |	boolean |	Whether or not email validation has been performed
google-auth         |	boolean |	Whether or not Google validation is performed
safe-pwd-auth       |	boolean |	Whether a security password is set


## Get Account Balance

This endpoint returns the asset information of the current user

**HTTP Request**

+ GET <code>/exchange/api/v1/account/balance</code>

**Request Parameter**

None


**Response Content**

> Response:

```json
"datas":[
    {
        "balance":"14454.5616102632",
        "freeze":"413",
        "available":"14041.5616102632",
        "user-id":"7eU4wsh3XLE",
        "currency":"zt"
    },
    {
        "freeze":"4224.4187921662601158",
        "available":"18255.174060294635375085",
        "user-id":"7eU4wsh3XLE",
        "currency":"usdt"
    },
    {
        "balance":"2.09390540356",
        "freeze":"0",
        "available":"2.09390540356",
        "user-id":"7eU4wsh3XLE",
        "currency":"etc"
    }
]
```

The data for turn is a list, field description

Field           | Data Type |	Description
----------------|--------------|--------
user-id         |	string  |	user ID
currency        |	string  |	currency name
balance         |	decimal |	balance
available       |	decimal |	availablebalance
freeze          |	decimal |	frozen(not available)


## Get Account Balance for a Single Currency

This endpoint returns the balance of an account specified by currency name.

**HTTP Request**

+ GET <code>/exchange/api/v1/account/balance/{currency}</code>

**Request Parameter**

Parameter       |  Data Type  | Required | Description
----------------|------------|--------|--------
currency     |	string   |	true  |	currency name


**Response Content**

> Response:

```json
"datas":{
    "freeze":"4224.4187921662601158",
    "available":"18255.174060294635375085",
    "user-id":"7eU4wsh3XLE",
    "currency":"usdt"
}

```

Field           | Data Type |	Description
----------------|-----------|--------
user-id         |	string  |	user ID
currency        |	string  |	currency name
balance         |	decimal |	balance
available       |	decimal |	availablebalance
freeze          |	decimal |	frozen(not available)

## Transfer Asset between Main and Sub Account

The main account shall perform the Asset transfer between the main and sub account.

**HTTP Request**

+ POST <code>/exchange/api/v1/account/sub/transfer</code>

**Request Parameter**

Parameter       |  Data Type  | Required | Description
----------------|------------|--------|--------
sub-uid      |   string   |  true  |	sub accountID
currency     |	string   |	true  |	currency name
amount     |	decimal   |	true  |	quantity
type     |	string   |	true  | transfer type：<br/>master-transfer-in:sub account transfer to main account currency<br/> master-transfer-out :main account transfer to sub account

**Response Content**

None



## Get the Aggregated Balance of all Sub-accounts of the Current User

The main account queries the various currencies of all sub accounts under it.

**HTTP Request**

+ GET <code>/exchange/api/v1/account/sub/aggregate-balance</code>

**Request Parameter**

None

**Response Content**

> Response:

```json
"datas":[
    {
        "balance":"111",
        "currency":"zb"
    },
    {
        "balance":"40",
        "currency":"zt"
    }
]
```

The data for turn is a list, field description

Field           | Data Type |	Description
--------------------|-----------|--------
currency            |	string  | Currency name
balance             |	decimal | All balance of this currency under sub account（available balance and frozen balance)
freeze              |	decimal | All frozen balance of this currency under sub account
available           |	decimal | All available balance of this currency under sub account


## Get Account Balance of a Sub-Account

The main account checks the currency balance of the specified sub-account

**HTTP Request**

+ GET <code>/exchange/api/v1/account/sub/balance/{sub-uid}</code>

**Request Parameter**

Parameter       |  Data Type  | Required | Description
----------------|------------|--------|--------
sub-uid      |   string   |  true  |	Sub-Account Id


**Response Content**

> Response:

```json
"datas":[
    {
        "freeze":"0",
        "user-id":"7mucaa0JNa4",
        "balance":"40",
        "available":"40",
        "currency":"zt"
    },
    {
        "freeze":"0",
        "user-id":"7mucaa0JNa4",
        "balance":"111",
        "available":"111",
        "currency":"zb"
    }
]
```

The data for turn is a list, field description

Field           | Data Type |	Description
--------------------|-----------|--------
currency        |	string  |	currency name
balance         |	decimal |	balance
available       |	decimal |	available balance
freeze          |	decimal |	frozen(not available)


# Deposit and Withdraw

## Qeury Deposit Address

Generate and get the currency address in this platform.

**HTTP Request**

+ GET <code>/exchange/api/v1/account/deposit/address</code>

**Request Parameter**

Parameter       |  Data Type  | Required | Description
----------------|------------|--------|--------
currency      |   string   |  true  |	currency，btc,ltc...(ZBG supports [currencies][currencies])


**Response Content**


> Response:

```json
"datas":
{
    "address": "19cdJwd3j6ArHNhiYoWpN8cJq9ash7WDDC",
    "currency":"zt"
}
```

Field    | Data Type  |Required|	Description
------------|-----------|--------|-----------
currency    |	string  | true   |	Currency name
address     |	string  | true   |	Deposit address	
memo         |	string  | false   |	Deposit address memo

    Note：about memo
    
    1.If your address is a personal wallet address, feel free to fill it in.
    
    2.If your address is the recharge address of the exchange, a withdrawal address and a Tag (also known as Tag/ Tag/ Memo/ note, etc.) are required when withdrawing coins. The Tag ensures that your withdrawal address is unique and corresponds to the receipt address.
    
    3.Please be sure to follow the correct coin withdrawal steps and input the complete information when withdrawing the coin, otherwise you will face losing the coin.ZBG platform will not bear the risk of retrieving the service.  
  
    4.If you have any questions, please contact ZBG customer service


## Search for Existed Deposits

This endpoint searches for all existed deposits and return their latest status.

**HTTP Request**

+ GET <code>/exchange/api/v1/account/deposit/history</code>

**Request Parameter**

Parameter       |  Data Type  | Required | Description
----------|------------|--------|--------
currency  |   string   |  false |	Currency name: btc,ltc...(zbg support [currencies])
direct    |   string   |  false |	Turn records the sort direction， “prev” or “next”default is“next” 
page      |   int      |  false |	Page，default 1
size      |   int      |  false |	Query record size, default 100, value [1,500]


**Response Content**

> Response:

```json
"datas": {
    "rows": 2,  
    "page": 1,
    "size": 20, 
    "list": [
       {
            "amount":"1525.9974",
            "address":"1b2SkX62jPnMrD8dp3mdr1ZRSaYSbgpu7",
            "tx-hash":"846ed6e2e7f53d2732670254020749b810aeccb3180f875d4bdecd9b0241b9ea",
            "currency":"usdt",
            "deposit-id":"D6568102836918697984",
            "state":"comleted",
            "type":"blockchain",
            "confirm-times":6,
            "created-at":1567594618000,
            "confirmed-at":1567594618000
       },
       {
            "amount":"1000000",
            "address":"",
            "tx-hash":"I6540486090229694464",
            "currency":"krw",
            "deposit-id":"D6540486090254860290",
            "state":"confirming",
            "type":"system",
            "confirm-times":0,
            "created-at":1566905897000,
            "confirmed-at":1566905897000
       }
    ],
    
}
```

The response data is a page object, field description

Field    | Data Type  |	Description
------------|-----------|-----------
page        |	int     |	Pages
size        |	int     |	Query record size	
rows        |	int     |	Total number of records for the query
list        |	array   |	Data list, fields below

+ Charged currency record field：

Field    | Data Type  |	Description
------------|-----------|-----------
deposit-id |	string  |	deposit ID
currency    |	string  |	currency	
amount      |	decimal  |	deposit amount
address     |	string  |	The deposit source address
tx-hash     |	string  |	The on-chain transaction hash
confirm-times     |	int  |	confirm-times
state       |	string  |	deposit status：[confirming, completed]
type        |	string  |	The type of this deposit (see below for details)
created-at  |	long  |	created time 
confirmed-at|	long  |	The time of entry is confirmed 6 times

+ Deposit type definition：

Type       | Description
-----------|----------
blockchain | Blockchain roll-in
system     | system deposit
recharge   | fiat recharge
transfer   | Merchants transfer money to each other


## Query Withdraw Address

Get the withdrawal address of currency filled in on this platform

**HTTP Request**

+ GET <code>/exchange/api/v1/account/withdraw/address</code>

**Request Parameter**

Parameter       |  Data Type  | Required | Description
--------------|------------|--------|--------
currency      |   string   |  true  |	currency，btc,ltc...(ZBGsupport [currencies])


**Response Content**

> Response:

```json
"datas":
{
    "address": "19cdJwd3j6ArHNhiYoWpN8cJq9ash7WDDC",
    "currency":"zt"
}
```

The data for turn is a list, field description

Field       |  Data Type  | Required | Description
------------|-----------|--------|-----------
currency    |	string  | true   |	currency name
address     |	string  | true   |	withdrawal address
remark      |	string  | false   |	remark



## Create a Withdraw Request

**HTTP Request**

+ POST <code>/exchange/api/v1/account/withdraw/create</code>

**Request Parameter**

Parameter       |  Data Type  | Required | Description
------------|------------|--------|--------
currency    |   string   |  true  |	currency，btc,ltc...(ZBG support [currencies])
address     |   string   |  true  |	withdrawal address，Need to advance in the <a href = "/u/payout" id = "payout"> website </a> Settings
amount      |   decimal  |  true  |	withdrawal quantity


**Response Content**


> Response:

```json
"data":"W6574581089036550144"
```

The data for turn is the withdrawal ID of a string type


## Cancel a Withdraw Request


**HTTP Request**

+ POST <code>/exchange/api/v1/account/withdraw/cancel/{withdraw-id}</code>

**Request Parameter**

Parameter       |  Data Type  | Required | Description
----------------|------------|--------|--------
withdraw-id     |   string   |  true  | Withdraw ID, fill in the path



**Response Content**

> Response:

```json
"datas":"W6574581089036550144"
```

Field    | Data Type  |	Description
------------|-----------|-----------
withdraw-id |	string  |	withdraw ID


## Search for Existed Withdraws

This endpoint searches for all existed withdraws and return their latest status.


**HTTP Request**

+ GET <code>/exchange/api/v1/account/withraw/history</code>

**Request Parameter**

Parameter       |  Data Type  | Required | Description
----------|------------|--------|--------
currency  |   string   |  false |	currency，btc,ltc...(ZBG support [currency][currencies])
direct    |   string   |  false |	turn record sort direction， “prev”or “next” default as“next” 
page      |   int      |  false |	page，default 1
size      |   int      |  false |	enquire records the size, by default 100, and values [1,500]


**Response Content**

> Response:

```json
"datas": {
    "rows": 1,  
    "page": 1,
    "size": 20, 
    "list": [
       {
            "withdraw-id":"W6574494550289956864",
            "currency":"usdt",
            "amount":"6",
            "address":"17EmSA2JNmrsy9mcFqHiMZztVUXxXF343",
            "tx-hash":null,
            "fee":"0.001",
            "audited-at":1568896676000,
            "created-at":1567481648000,
            "state":"pass"
       }
    ],
    
}
```

The data for turn is a page object, field description

Field    | Data Type  |	Description
------------|-----------|-----------
page        |	int     |	page
size        |	int     |	enquire size	
rows        |	int     |	enquire rows
list        |	array   |	Data list, fields below

+ Charged currency record field：

Field    | Data Type  |	Description
------------|------------|-----------
withdraw-id |	string   |	deposit ID
currency    |	string   |	currency	
amount      |	decimal  |	withdrawal amount
address     |	string   |	The withdraw source address
tx-hash     |	string   |	The on-chain transaction hash
state       |	string   |	The state of this transfer (see below for details)
fee         |	decimal  |	withdraw fee
created-at  |	long     |	The timestamp in milliseconds for the transfer creation
audited-at  |	long     |	The timestamp in milliseconds for the transfer audition

+ withdrawal status definition：

Status     | Description
-----------|----------
reexamine  | Under examination for withdraw validation 
canceled   | Withdraw canceled by user
pass       | Withdraw validation passed
reject     | Withdraw validation rejected
transferred   | On-chain transfer initiated
confirmed   | On-chain transfer completed with one confirmation 


# Spot Trading 
    
Access to the spot transactions interface such as None specifically requires signature authentication.

## Place a New Order

Send a new order to ZBG for matching

**Waring:**
In order to prevent users from misoperation, the price of purchase order should not be three times higher than the recent deal price, and the price of selling order should not be less than one third of the recent deal price

**HTTP Request**

+ POST <code>/exchange/api/v1/order/create</code>

**Request Parameter**

> Request Body:

```json
{
"symbol": "zt_usdt",
"side": "buy",
"amount": "1000",
"price": "0.034",
}
```

Parameter       |  Data Type  | Required | Description
----------|------------|--------|--------
symbol   |   string   |  true |	trading pair，example：btc_usdt, eth_usdt...(zbg supported [symbols])
side    |   string   |  true |	order type，include buy，sell
amount      |   decimal      |  true |	quantity
price      |   decimal      |  true |	The price of the order


**Response Content**

> Response:

```json
"datas": "E6580725150600146944"
```

The data for turn is the order number of string type

- Get this order number does not mean that the order was placed successfully. Users can place an order through [Get Order Details](#get-the-order-detail-of-an-order) enquire order status.

- The order will be rejected if the precision of quantity and price exceeds the preset value of currency.



## Submit Cancel for an Order

This endpoint submits a request for cancel order.

This only submit the cancel request, the actual result of the canel request needs to be checked by order status or match result endpoints.

**HTTP Request**

+ POST <code>/exchange/api/v1/order/cancel</code>

**Request Parameter**

Parameter       |  Data Type  | Required | Description
----------|------------|--------|--------
symbol   |   string   |  true |	trading pair，例如：btc_usdt,eth_usdt
order-id    |   string   |  true |	order号

**Response Content**

None


## Submit Cancel for Multiple Orders by Criteria

This endpoint submit cancellation for multiple orders at once with given criteria.

This only submit the cancel request, the actual result of the canel request needs to be checked by order status or match result endpoints.

**HTTP Request**

+ POST <code>/exchange/api/v1/order/batch-cancel</code>

**Request Parameter**

Parameter       |  Data Type  | Required | Description
----------|------------|--------|--------
symbol    |   string   |  true  |	trading pair，such as：btc_usdt,eth_usdt
side    |   string   |  false | Active trading direction，“buy”or“sell”， 
order-ids     |   list      |  false |	Order ID
price-from    |   decimal   |  false |	Delegate price interval cancel: cancel the delegate of unit price >=price-from
price-to    |   decimal   |  false |	Delegate price interval cancellation: cancel unit price <=price-to


**Response Content**

The number of orders cancelled by turn

> Response:

```json
"datas": 2
```



## Get Open Orders

This endpoint returns all open orders which have not been filled completely.

**HTTP Request**

+ GET <code>/exchange/api/v1/order/open-orders</code>

**Request Parameter**

Parameter       |  Data Type  | Required | Description
---------------|-------------|------------|------------
symbol   |   string   |  true |	trading pair，such as：btc_usdt,eth_usdt
page    |   int   |  false |	Page number, default 1
size    |   int   |  false |	Turn order quantity, default 20 Max 100

**Response Content**

> Response:

```json
"datas": {
    "rows": 1,  
    "page": 1,
    "size": 20, 
    "list": [
       {
        "symbol":"eth_usdt",
        "side":"buy",
        "amount":"8.29713999",
        "available-amount":"8.29713999",
        "filled-amount":"0",
        "price":"99.054901",
        "filled-cash-amount":"0",
        "created-at":1568980634849,
        "order-id":"E6580781752669708288",
        "state":"created"
       }
    ],
    
}
```

The reponse data is a page object

Field       | Data Type |	Description
------------|-----------|-----------
page        |	int      |	page
size        |	int      |	enquire record size	
rows        |	int      |	Total number of records for enquire
list        |	array    |	Data list, fields below

+ Charged currency record field：

Field       | Data Type |	Description
------------|-----------|-----------
order-id    |	string   |	order ID
symbol      |	string   |	trading pair	
price       |	decimal  |	Entrusted price
side               |	string  |	order type， buy/sell
amount    |	string  |	Total amount
available-amount       |	string  |	Outstanding quantity
filled-amount      |	string  |	The amount which has been filled
filled-cash-amount |	string  |	The filled total in quote currency
state        |	string  | order status:<br/>submitted：Order application (not in storage), <br/>partial-filled：Part of the deal <br/>cancelling：Is removed from the single, <br/>created：created (in storage)
created-at  |	long  |	The timestamp in milliseconds when the order was created


## Search Historical Orders

This endpoint returns orders based on a specific searching criteria. 

**HTTP Request**

+ GET <code>/exchange/api/v1/order/orders</code>

**Request Parameter**

Parameter       |  Data Type  | Required | Description
----------|------------|--------|--------
symbol   |   string   |  true |	trading pair，example：btc_usdt,eth_usdt
side   |   string   |  false |	order type，buy/sell
state   |   string   |  false | status，valid status：<br/>partial-filled: portion deal, <br/> partial-canceled: portion deal withdrawal,<br/> filled: completely deal, <br/> canceled: Had withdrawn，<br/> created: created (in storage)
start-date   |   string   |  false | enquire Start date, date format yyyy-mm-dd 
end-date   |   string   |  false | enquire end date, date format yyyy-mm-dd 
history  |   boolean   |  false | For enquire history, default false
page    |   int   |  false |	Page number, default 1
size    |   int   |  false |	Turn order quantity, default 20 Max 100

Historical orders that have been completed or withdrawn are removed to the backup table by setting history=true to search


**Response Content**

> Response:

```json
"datas": {
    "rows": 1,  
    "page": 1,
    "size": 20, 
    "list": [
       {
        "symbol":"eth_usdt",
        "side":"buy",
        "amount":"8.29713999",
        "available-amount":"8.29713999",
        "filled-amount":"0",
        "price":"99.054901",
        "filled-cash-amount":"0",
        "created-at":1568980634849,
        "order-id":"E6580781752669708288",
        "state":"created"
       }
    ],
    
}
```

The response data is a page object

Field    | Data Type  |	Description
------------|-----------|-----------
page        |	int     |	page
size        |	int     |	enquire size	
rows        |	int     |	enquire rows
list        |	array   |	Data list, fields below

+ Record field：

Field    | Data Type  |	Description
------------|-----------|-----------
order-id |	string  |	order ID
symbol    |	string  |	trading pair	
price      |	decimal  |	Entrusted price
side     |	string  |	order type， buy/sell
amount    |	string  |	Total amount
available-amount       |	string  |	Outstanding quantity
filled-amount      |	string  |	The amount which has been filled
filled-cash-amount |	string  |	The filled total in quote currency
state        |	string  | order status，include：<br/>partial-filled: portion deal,<br/> partial-canceled: portion deal withdrawal,<br/> filled: complete deal,<br/> canceled: cancel，<br/> created: created (in storage)
created-at  |	long  |	The timestamp in milliseconds when the order was created


## Get the Order Detail of an Order

This endpoint returns the latest status and details of one order.

**HTTP Request**

+ GET <code>/exchange/api/v1/order/detail</code>

**Request Parameter**

Parameter       |  Data Type  | Required | Description
----------|------------|--------|--------
symbol   |   string   |  true |	trading pair，example：btc_usdt,eth_usdt
order-id   |   string   |  true |	order


**Response Content**

> Response:

```json
"datas": {
    "symbol":"eth_usdt",
    "side":"buy",
    "amount":"8.29713999",
    "available-amount":"8.29713999",
    "filled-amount":"0",
    "price":"99.054901",
    "filled-cash-amount":"0",
    "created-at":1568980634849,
    "order-id":"E6580781752669708288",
    "state":"created"
}
```

Field    | Data Type  |	Description
------------|-----------|-----------
order-id |	string  |	order ID
symbol    |	string  |	trading pair	
price      |	decimal  |	Entrusted price
side     |	string  |	ordertype， buy/sell
amount    |	string  |	Total amount
available-amount       |	string  |	Outstanding quantity
filled-amount      |	string  |	The amount which has been filled
filled-cash-amount |	string  |	The filled total in quote currency
state        |	string  | order status， include：[partial-filled, partial-canceled, filled, canceled, created]
created-at  |	long  |	The timestamp in milliseconds when the order was created



## Get the Match Result of an Order

This endpoint returns the match result of an order.

**HTTP Request**

+ GET <code>/exchange/api/v1/order/trades</code>

**Request Parameter**

Parameter       |  Data Type  | Required | Description
----------|------------|--------|--------
symbol   |   string   |  true |	trading pair，example：btc_usdt,eth_usdt
order-id   |   string   |  true |	order


**Response Content**

> Response:

```json
"datas": [
    {
        "trade-id":"T6580794335153889280",
        "symbol":"eth_usdt",
        "match-id":"E6580794334986117120",
        "side":"buy",
        "filled-amount":"0.62933",
        "role":"taker",
        "price":"99.955268",
        "filled-fees":"0.12580969762088",
        "created-at":1568983634747,
        "order-id":"E6561542094882611200"
    }
]
```

The response data is a list object

+ Record field：

Field    | Data Type  |	Description
------------|-----------|-----------
trade-id |	string  |	Unique trade ID
order-id |	string  |	order ID
match-id |	string  |	The match order id of this match
symbol    |	string  |	trading pair	
price      |	decimal  |	The limit price of limit order
side     |	string  |	initiative order type，buy/sell
filled-amount    |	string  |	The amount which has been filled
filled-fees       |	string  |	Transaction fee paid so far
role        |	string  |	the role in the transaction: taker or maker
created-at  |	long  |	The timestamp in milliseconds when the match and fill is done

# WebSocket Market Data

## General

**URL**

<code>wss://kline.zbg.com/websocket</code>


**Heartbeat and Connection**

When the user's Websocket client is connected to Websocket server, if there is no data push for a long time, the connection may be broken. The client can send ping messages regularly to ensure continuous connection.

    {"action":"PING"}

When the user's Websocket client receives this heartbeat message, it should turnpong message:

    {"dataType":null,"action":"PING","msg":"action not support","code":"5021"}


**Subscribe to Topic**

After successfully establishing a connection with the Websocket server, the Websocket client sends the following request to subscribe to a specific topic:

    {"action":"ADD", "dataType":"message topic", "dataSize":1}

- *action*: request action type，ADD:Increase data subscription，DEL:Remove data subscription
- *dataType*: The data type of the request is detailed in the following sections
- *dataSize*: Request's quantity, determines the quantity of the first full quantity data, and turns a piece of data if it is not passed or 0.

Example:

    {"action":"ADD", "dataType":"336_ENTRUST_ADD_ZT_USDT", "dataSize":1}

After successful subscription, if it is the first subscription, the Websocket client will receive the full quantity message:


    [[
        "AE",
        "336",
        "ZT_USDT",
        "1569311844",
        {
            "asks":[
                ["0.0485","2000"],
                ["0.048","32000"],
                ["0.0478","12584.2001"],
                ...
             ]
        },{
            "bids":[
                ["0.0404","45.1537"],
                ["0.0403","338.0193"],
                ["0.0401","3831.9868"],
                ["0.04","2086.9575"],
                ...
            ]
        }
    ]]

After that, the Websocket client will receive an update pushed by the server as soon as the subscribed topic is updated.


    ["E","336","1569311848","ZT_USDT","BID","0.0399","7897.5386"]


**Unsubscribe**

The unsubscribe format is as follows：

{"action":"DEL", "dataType":"message topic"}


Example:

{"action":"DEL", "dataType":"336_ENTRUST_ADD_ZT_USDT"}


## Market Candlestick

**Topic**

Once the k-line data is generated, the Websocket server will push it to the client via this subscription topic interface.

<code>symbol-id_KLINE_period_symbol</code>

**Request Parameter**

> Request Data：

```json
{
    "action":"ADD", 
    "dataType":"336_KLINE_1M_ZT_USDT",
    "dataSize":1000
} 
```

Parameter       |  Data Type  | Required | Description
----------|------------|--------|--------
symbol-id   |   string   |  true |	trading pair ID，Refer to the [symbols] interface for the turn value ID.
period   |   string   |  true |	Kline period, support :1M, 5M, 15M, 30M, 1H, 1D
symbol   |   string   |  true |	trading pair，such as：btc_usdt,eth_usdt

**Response Content**

> Response:

```json
[
    ["K","336","zt_usdt","1569314760","0.0405","0.0405","0.0404","0.0404","42888.96","-0.2469","7.12","1M","false","1735.19"],
    ["K","336","zt_usdt","1569314700","0.0404","0.0405","0.0404","0.0404","63246.27","0","7.12","1M","false","2557"],
]
```


> Subsequent increase in quantity data:

```json
["K","336","zt_usdt","1569314940","0.0405","0.0405","0.0404","0.0405","50044.14","0","7.12","1M","false","2022.62"]
```

The response Data is a list,list of data definition

[K, symbol-id, symbol, timestamp, open, high, low, close, volume, change, dollar-rate, period, conversion, amount.

- amount : Aggregated trading value during the interval (in quote currency)
- volume : Aggregated trading volume during the interval (in base currency)

If it is the first time, the full quantity data is turned.


## Market Depth

**Topic**

When the market depth changes, this topic sends the latest market depth update data:

<code>symbol-id_**ENTRUST_ADD**_symbol</code>

**Request Parameter**

> Request Data：

```json
{
    "action":"ADD", 
    "dataType":"336_ENTRUST_ADD_ZT_USDT"
}
```

Parameter       |  Data Type  | Required | Description
----------|------------|--------|--------
symbol-id   |   string   |  true |	trading pairID，Refer to the [symbols]
symbol   |   string   |  true |	trading pair，example：btc_usdt,eth_usdt, Refer to the [symbols]

**Response Content**

> First Response data：

```json
[[
    "AE",
    "336",
    "ZT_USDT",
    "1569311844",
    {
        "asks":[
            ["0.0485","2000"],
            ["0.048","32000"],
            ["0.0478","12584.2001"],
            ...
         ]
    },{
        "bids":[
            ["0.0404","45.1537"],
            ["0.0403","338.0193"],
            ["0.0401","3831.9868"],
            ["0.04","2086.9575"],
            ...
        ]
    }
]]
```

> Subsequent increase in quantity data:

```json
 ["E","336","1569311848","ZT_USDT","BID","0.0399","7897.5386"]
```

The repsonse Data is a list, list of data definition：

Full data field description：

[AE, symbol-id, symbol, timestamp, asks:[[price, quantity]], bids[[price, quantity]]]

Increased quantity data field description：

[E, symbol-id, timestamp, symbol, ask/bid, price, quantity]


## Market Trade

**Topic**

This topic provides the latest deal details in the market:

<code>symbol-id_**TRADE**_symbol</code>

**Request Parameter**

> Request Data：

```json
{
    "action":"ADD", 
    "dataType":"336_TRADE_ZT_USDT", 
    "dataSize":2
}
```


Parameter       |  Data Type  | Required | Description
----------|------------|--------|--------
symbol-id   |   string   |  true |	trading pair ID，Refer to [symbols]
symbol   |   string   |  true |	trading pair，such as：btc_usdt,eth_usdt，Refer to [symbols]

data Size article 50 the most

**Response Content**

> First Response data:

```json
[
    ["T","336","1569316972","ZT_USDT","bid","0.0404","11895.3578"],
    ["T","336","1569316959","ZT_USDT","ask","0.0405","22546.8484"]
]
```

> Subsequent increase in quantity data:

```json
 ["T","336","1569316988","ZT_USDT","bid","0.0405","25038.6289"]
```

The repsonse Data is a list,list of data definition.

Data field description: 

[T, symbol-id, symbol, timestamp, ask/bid, price, quantity]



## Market Tickers

**Topic**

Topics provide the latest market overview within 24 hours.：

<code>symbol-id_**TRADE_STATISTIC_24H**</code>

> The type data is refreshed every 5 seconds.

**Request Parameter**

> Request Data:
> ALL Market:

```json
{"action":"ADD", "dataType":"ALL_TRADE_STATISTIC_24H"}
```

> Single Market:

```json
{"action":"ADD", "dataType":"5140_TRADE_STATISTIC_24H"}
```

Parameter       |  Data Type  | Required | Description
----------|------------|--------|--------
symbol-id   |   string   |  true |	ALL or trading pair id，Refer to the[symbols]

when symbol-id = ALL ，Turn the 24-hour market for all trading pairs

**Response Content**

> All Market Response:

```json
{"trade_statistic":[
    ["5140","0.0199","0.0199","0.0111","27597.0704","7.57","[[1, 0.0199]]","0.012","0.0457","504.7737"],
    ["5020","203823.8","219584","193014.1","21.8465","-4.83","[[1, 204168.8], [2, 207395.2], [3, 205103.6], [4, 206200.9], [5, 204334.9], [6, 203823.8]]","201002.4","206644.9","4524612.3764"],
    ["5021","28691.3","30639.6","27704.9","29.8016","-4.69","[[1, 28519.8], [2, 29109.5], [3, 28280], [4, 28667.7], [5, 28618], [6, 28691.3]]","28334.3","29047.8","864655.9334"],
]}
```

> Single Market Respons:

```json
{"trade_statistic":[
    ["5140","0.0199","0.0199","0.0111","27597.0704","7.57","[[1, 0.0199]]","0.012","0.0457","504.7737"]
]}
```


Data field description: 

{
    "trade_statistic":[
        [ symbol-id, close, high，low, volume，change, Latest 6H closing price list, bid，ask,amount],
        ....
    ]
}

- Latest 6H closing price list ：[[serialnumber, close], [serialnumber, close], [serialnumber, close]]
- amount : Aggregated trading value during the interval (in quote currency)
- volume : Aggregated trading volume during the interval (in base currency)


## Order Change

**Topic**

Topics provide user order updates

<code>symbol-id_**RECORD_ADD**_user-id_symbol</code>


**Request Parameter**

> Request Data:

```json
{"data Type":"336_RECORD_ADD_7eOUtLBFXTU_ZT_USDT","dataSize":50,"action":"ADD"}

```

Parameter       |  Data Type  | Required | Description
----------|------------|--------|--------
symbol-id   |   string   |  true |	ALL 或trading pair ID，Refer to the [symbols]
user-id|   string   |  true |	user ID
symbol   |   string   |  true |	trading pair，example：btc_usdt,eth_usdt，Refer to the [symbols]

> data Size Article 100 the most

**Response Content**

> Response:

```json
[[
    "AR",
    "336",
    "7eOUtLBFXTU",
    "1569291941",
    [
        ["E6580725150600146944",1,2,"0.0403","1","1","0.0403","0.0403","1568967139"],
        ["E6581867456476753920",1,1,"0.041","10","0","0","","1569239486"],
        ["E6577865758972325888",1,1,"0.0405","1","0","0","","1568285407"],
        ...
    ]
]]
```

> Subsequent increase in quantity data:

```json
["R","336","7eOUtLBFXTU","1569328042","E6582238884078301184",1,0,"0.039","10","0","0","","1569328042"]
```

Full data field description：[AR, market ID, user ID, The time stamp, [OrderNo, type, status, price, entrust quantity, complete quantity, complete amount, average price, establish The time stamp]]

Increased quantity data field description.：[R, market ID, user ID, The time stamp, OrderNo, type, status, price, entrust quantity, complete quantity, complete cash amount,Average price, create The time stamp

type：1:buy, 0:sell

status :  0:created 1:canceled 2: filled 3:partial-filled



[currencies]:#public-get-all-supported-currencies
[symbols]:#public-get-all-supported-trading-symbols

---------------------------------------------------------------------

<div style="text-align: right;font-size:0"> created by zhangzp at 2019-09-21 </div>
