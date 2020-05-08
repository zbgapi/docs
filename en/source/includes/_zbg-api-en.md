# Change Log


## 2020-04-20 

Add and modify contract interface：

* 新增接口[[/exchange/api/v1/future/common/contracts](#daedf585f3)]，查询合约列表
* 新增接口[[/exchange/api/v1/future/common/currencies](#b22727fec6)]，获取合约可用币种
* 新增接口[[/exchange/api/v1/future/place](#9dc85ffb46)]，合约下单
* 新增接口[[/exchange/api/v1/future/cancel](#68a1fe7812)]，合约撤单
* 新增接口[[/exchange/api/v1/future/cancel-all](#5538919b9f)]，合约一键撤单
* 新增接口[[/exchange/api/v1/future/positions](#01106676d4)]，合约持仓查询
* 新增接口[[/exchange/api/v1/future/orders](#513eb615d1)]，查询合约当前委托
* 新增接口[[/exchange/api/v1/future/orders/his](#23889edae4)]，查询合约历史委托
* 新增接口[[/exchange/api/v1/future/assets/flow](#f300a78dba)]，查询合约资金流水
* 新增接口[[/exchange/api/v1/future/assets/available](#8da397f29a)]，查询合约可用资金
* 新增接口[[/exchange/api/v1/future/assets/profits](#1b4243a44a)]，查询合约盈亏历史
* 新增接口[[/exchange/api/v1/future/adjust-margin-rate](#bdac9bbb53)]，调整保证金率
* 新增接口[[/exchange/api/v1/future/adjust-margin](#9e3bc1e664)]，调整保证金
* 新增接口[[/exchange/api/v1/future/florders](#4c8fd7c9eb)]，查询合约历史强减
* 新增接口[[/exchange/api/v1/future/fcorders](#97ba66701c)]，查询合约历史强平
* 新增接口[[/exchange/api/v1/future/market/klines](#k-2)]，合约K线数据
* 新增接口[[/exchange/api/v1/future/market/ticker](#964054eba1)]，单个合约行情
* 新增接口[[/exchange/api/v1/future/market/tickers](#4802b68052)]，所有合约行情
* 新增接口[[/exchange/api/v1/future/market/depth](#470b9cf6ae)]，合约深度
* 新增接口[[/exchange/api/v1/future/market/trades](#540eaf650b)]，合约历史成交记录
* 移除接口[/exchange/api/v1/account/future/auth]
* 移除接口[/exchange/api/v1/common/future/symbol]
* 移除接口[/exchange/api/v1/common/future/currency]
* 移除接口[/exchange/api/v1/common/future/kline]
* 移除接口[/exchange/api/v1/common/future/tickers]
* 移除接口[ /exchange/api/v1/common/future/ticker]
* 移除接口[ /exchange/api/v1/common/future/depth]
* 移除接口[ /exchange/api/v1/common/future/trades]


## 2020-03-31

Add [SDK](#sdk-and-demo) and [Contract](#contract)

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


<br/>

## Authentication

**Overview**

the API requests may be tampered during internet. In order to ensure that the request is not changed, all private API must be signed by your API Key (Secrete Key).

A valid request consists of below parts:

+ *Interface Address*       : https://www.zbg.com/exchange/api/v1/order/orders
+ *API Access Key*          : The Access Key in the API Key 
+ *Signature Method*        : The Hash method that is used to sign, it uses md5
+ *Timestamp*               : The millisecond timestamp of the time you made the request, located in the Header
+ *Parameters*              : Each API Method has a group of parameters, you can refer to detailed document for each of them.. 
+ *Signature*               : The value after signed, it is guarantee the signature is valid and the request is not be tempered.

That is, the request header must contain the following content：

*Apiid* : API Key of string type

*Timestamp* : The timestamp of the request

*Sign* : Signature string (Sign according to the reference format)

*Passphrase* : The passphrase that you specified when you created the API key

>Passphrase request headers are strings that are processed by *md5(timestamp+passphrase)* instead of directly using Passphrase to request a password

> This field is not required, depending on whether the user specified it when creating the API Key.

**Create API Key** 

You can create API Key <a href="/u/api" class="createApi">here</a>.

API key including the following three parts:

+ Access Key : API Access key
+ Secret Key : The key used for signature authentication encryption (visible on application only)
+ Passphrase : API Access command

> API key it has all operation rights including trading, depositing and withdrawing currency.

> **These two keys are closely related to account security and should not be disclosed to others at any time.**

The API key and secret Key will be randomly generated and provided by ZBG, and Passphrase will be provided by you to ensure the security of API access.

Passphrase for the optional field users can choose to provide the field according to their own needs, if the field is filled in, the request header must be put on when accessing the interface, the background system will carry out the verification.

**Timestamp**

<code>Timestamp</code>  The request header is a long integer timestamp at the millisecond level, and requests that differ by more than 60 seconds between the timestamp and the server time are considered expired and rejected by the system. If you think there is a significant time bias between the server and the API server, we recommend that you use the [fetch server time interface](#public-get-current-system-time)  to query the API server time 

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

3.Use the request string from the previous step and your key to generate a digital signature : MD5(key + timestamp + signature + secret)

<p><code>5fcbdb0862e10f9f6b885fdde42d58a1</code></p>

4.Add fields such as the generated digital signature App key timestamp to the Header

<p><code>header.put("Apiid",id);</code></p>
<p><code>header.put("Timestamp", String.valueOf(timestamp));</code></p>
<p><code>header.put("Passphrase", md5(timestamp + passphrase));</code></p>
<p><code>header.put("Sign", "5fcbdb0862e10f9f6b885fdde42d58a1");</code></p>

> Body mode *application/json* is slightly different for Post requests. In this case, there are no steps 1 or 2, so just enter step 3 using the body as a signature string


**Java signatures are used for example：**

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

md5 The abstract method is as follows：

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

StringKit.toHex() The method is as follows：

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

**JS Signature Call Example：**

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

<br/>



## Error Code

    code_6000(6000, "Parameters are missing", "Param Missing"),
    code_6001(6001, "General error prompt", "General Error"),
    code_6010(6010, "Can't find a market", "Cannot find the selected market"),
    code_6011(6011, "You have enabled Google login verification, this login requires your Google verification code", "You have enable Google verification for account login, please enter Google verification code."),
    code_6012(6012, "Unknown operation type!", "Unknown operation type!"),
    code_6013(6013, "You cannot deposit and withdraw until you passed the mobile phone authentication and Google authentication, for the sake of your account security，Please pass the mobile phone authentication or Google authentication first", "Please activate phone authentication or Google authentication to proceed with deposit. "),
    code_6014(6014, "SMS verification code error, please require it again", "SMS verification code error, please acquired again"),
    code_6019(6019, "Address requests are frequent. Please try again one hour later", "Your IP address request frequently, please try again 1 hour later."),
    code_6020(6020, "Network exception, please try again later", "Network anomaly, please try again later"),
    code_6021(6021, "Restrictions on withdrawal operations","Limit the withdraw operation"),
    code_6033(6033, "Please enter the correct Google verification code", "please input correct google code"),
    code_6040(6040, "The account has been frozen and temporarily unavailable", "The account has been frozen and temporarily unavailable"),
    code_6041(6041, "This account is not the main account, temporarily unable to operate", "This account is not the main account, temporarily unable to operate"),
    code_6043(6043, "The user has been blacklisted and cannot log in", "The user has been blacklisted and cannot log in"),
    
    code_6071(6071, "Invalid parameter", "Invalid parameter"),
    code_6096(6096 , "Invalid parameter" , "Invalid parameter"),
    code_6097(6097 , "Request too frequently", "Request too frequently"),
    code_6098(6098, "The user has been listed on the transaction blacklist and cannot trade", "The user has been listed on the transaction blacklist and cannot trade"),
    code_6114(6114, "Please select a currency address!", "Please select a currency address!"),
    code_6115(6115, "Failed to submit the withdrawal application！", "Failed to submit the withdrawal application！"),
    code_6117(6117, "There is no withdrawal record selected to cancel！", "There is no withdrawal record selected to cancel!"),
    code_6118(6118, "Withdrawal cancellation failed！", "Withdrawal cancellation failed!"),
    code_6119(6119, "This record cannot cancel withdrawal！", "This record cannot cancel withdrawal!"),
    code_6125(6125, "An invalid currency type！", "An invalid currency type!"),
    code_6133(6133, "Withdrawal address is illegal！", "Withdrawal address is illegal!"),
    
    code_6150(6150, "Single withdrawal exceeds or falls below the limit", "Single withdrawal exceeds or falls below the limit"),
    code_6151(6151, "The current withdrawal exceeds the limit", "The current withdrawal exceeds the limit"),
    code_6152(6152, "Cash withdrawal exceeds user limit", "Cash withdrawal exceeds user limit"),
    code_6153(6153, "Insufficient funds", "Insufficient funds"),
    code_6154(6154, "The record is not need re-withdraw", "The record is not need re-withdraw"),
    
    code_2012(2012, "entrust not exists or on dealing with system！", "entrust not exists or on dealing with system!"),
    
    code_6400(6400, "The market is currently closed", "This market is not open!"),
    code_6401(6401, "The current market has reached the threshold of price rise and drop, and has stopped trading, The next open time : %s", "The current market has reached the threshold of price rise and drop, and has stopped trading, The next open time : %s"),
    code_6402(6402, "Your order quantity exceeds the maximum limit :%s", "Your order quantity exceeds the maximum limit :%s"),
    code_6403(6403, "Your order price exceeds the limit :%s~%s", "Your order price of entrust exceeds limit: %s~%s"),
    
    code_6600(6600,"Failed to get bitbank currency balance","Failed to get bitbank currency balance"),
    code_6601(6601,"Bitbank does not have this currency","Bitbank does not have this currency"),
    code_6602(6602,"Bitbank has insufficient balance in this currency. Please recharge first","Bitbank has insufficient balance in this currency. Please recharge first"),
    code_6603(6603,"Current currency is forbidden to be recharged","Current currency is forbidden to be recharged"),
    
    code_6894(6894, "The API signature is no longer valid!", "The API signature is no longer valid!"),
    code_6895(6895, "Failed to verify API permissions. Interface is not an authorization API", "Failed to verify API permissions. Interface is not an authorization API"),
    code_6896(6896, "Failed to verify the API permission. The Userid is not matching with Apiid", "Failed to verify the API permission. The Userid is not matching with Apiid!"),
    code_6897(6897, "Failed to verify the API permission. Please confirm whether to enable API permission", "Failed to verify the API permission. Please confirm whether to enable API permission!"),
    code_6898(6898, "Failed to verify the API permission for multiple times. Please confirm whether to enable API permission", "Failed to verify the API permission for multiple times. Please confirm whether to enable API permission!"),
    code_6899(6899, "The market is temporarily not open to API trading", "The market is temporarily not open to API trading!"),
    code_6900(6900, "The exchange server temporarily not open to API trading", "The exchange server temporarily not open to API trading!"),
    code_6991(6991, "Incorrect price accuracy, up to %s digits in decimal places", "Incorrect price accuracy, up to %s digits in decimal places"),
    code_6992(6992, "The quantity accuracy of the order is wrong, and the number of decimal places is up to %s digits", "The quantity accuracy of the order is wrong, and the number of decimal places is up to %s digits"),
    code_6993(6993, "The minimum order quantity of the order is wrong, the minimum amount is %s", "The minimum order quantity of the order is wrong, the minimum amount is %s"),
    
    code_6999(6999, "access forbidden", "access forbidden!"),
    
    code_0000(1, "success", "success");





# Reference Data

## Public-Get all Supported Trading Symbols

This endpoint returns all ZBG's supported trading symbols.

**HTTP Request**

+ GET <code>/exchange/api/v1/common/symbols</code>

**Request Parameter**

No parameter is needed for this endpoint.

**Response Content**

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

**Use Case**

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

<br/>



## Public-Get all Supported Currencies

This endpoint returns all ZBG's supported trading currencies.

**HTTP Request**

+ GET <code>/exchange/api/v1/common/currencys</code>

**Required Parameter**

None

**Response Content**

Field       |  Data Type | Description
----------------|------------|--------
id              |   string   |  Currency ID
name            |   string   |	Name of the currency
draw-flag       |   boolean  |  Whether can withdraw
draw-fee        |   string   |	Withdraw Commission
once-draw-limit |   integer  |	Maximum withdrawal limit
daily-draw-limit|   integer  |	Maximum daily withdrawal limit
min-draw-limit  |   decimal  |	Minimum withdrawal limit


**Use Case**

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

<br/>



## Public-Get Current System Time

This endpoint returns the current system time in milliseconds adjusted to Beijing time zone.

**HTTP Request**

+ GET <code>/exchange/api/v1/common/timestamp</code>

**Request Parameter**

None

**Response Content**

Millisecond timestamp

**Use Case**

```json
"datas":1568812709229
```

<br/>


## Public-Get Current Auxiliary Price

This endpoint returns the discount price of the specified currency in usd, qc and btc.

**HTTP Request**

+ GET <code>/exchange/api/v1/common/assist-price</code>

**Request Parameters**

Parameter      |  Data Type | Required |	Description
----------------|------------|--------------|--------------
currencys      |   string   |    false     |	Currencies，Multiple currencies to separate such as : zt,usdt,btc

**Response Content**

Return cny, usd, btc for key three maps, map content value：<currency, price>


*Use Case**

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

<br/>

## Public-Get the Historical Trades

This endpoint return the latest 80 transaction records.

**HTTP Request**

+ GET <code>/exchange/api/v1/common/trade-history/{symbol}</code>

Returns the maximum 1000 historical transaction records from [trade-id] onwards

+ GET <code>/exchange/api/v1/common/trade-history/{symbol}/{trade-id}</code>

**Request Parameter**

Parameter      |  Data Type | Required |	Description
----------------|------------|------------|--------
symbol          |   string   |  true   |	The name of the market
trade-id        |   string   |  false  |    The unique trade id


**Response Content**

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


**Use Case**


```
# Request
https://www.zbg.com/exchange/api/v1/common/trade-history/zt_usdt

# Response
{
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
        },
        ......
    ],
    "resMsg":{
        "message":"success !",
        "method":null,
        "code":"1"
    }
}
    
    
# Request
https://www.zbg.com/exchange/api/v1/common/trade-history/zt_usdt/T6609287828167733248

# Response
{
    "datas":[
        {
            "trade-id":"T6609287828167733248",
            "date":"2019-12-08 11:50:12",
            "side":"buy",
            "amount":"3500.2956",
            "total":"133.36126236",
            "price":"0.0381",
            "created-at":1575777012865
        },
        {
            "trade-id":"T6609287852654080000",
            "date":"2019-12-08 11:50:18",
            "side":"sell",
            "amount":"876.9044",
            "total":"33.41005764",
            "price":"0.0381",
            "created-at":1575777018703
        },
        ......
    ],
    "resMsg":{
        "message":"success !",
        "method":null,
        "code":"1"
    }
}
```


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
dataSize        |	integer   |	true  |	Returns the number of Kline data,[1,100


**Response Content**

Returns a double-layer list and a list in the inner layer is a piece of data

**Use Case**

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

<br/>


## Public-Get Last Aggregate Ticker

This endpoint retrieves the latest ticker with some important 24h aggregated market data.

> This data server is updated once every 10 seconds, so it does not need to get too frequently

**HTTP Request**

+ GET <code>https://kline.zbg.com/api/data/v1/ticker</code>

**Request Parameter**

Parameter       |  Data Type  |Required|	Description
----------------|------------|--------|--------
marketName      |   string   |  true  |	trading pair

**Response Content**

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

**Use Case**

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

<br/>


## Public-Get Last Aggregate Tickers for All Pairs

This endpoint retrieves the latest tickers for all supported pairs.

>. The update speed of the data server is 10 seconds once, so it is unnecessary to get too frequently

**HTTP Request**

+ GET <code>https://kline.zbg.com/api/data/v1/tickers</code>

**Request Parameter**

Parameter       |  Data Type  |Required|	Description
----------------|------------|--------|--------
isUseMarketName  |   boolean   |  true  |	Parameters must be uploaded，true，turntrading pair

> The data format of turn is different when isUseMarketName is true and false. IsUseMarketName is recommended to pass true

**Response Content**

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

**Use Case**

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
    ],
    "ONT_USDT":[
        "5086",
        "0.8246",
        "0.8287",
        "0.7935",
        "2019.0654",
        "3.7",
        "[[1, 0.8246]]",
        "0.8162",
        "0.8163",
        "1625.9428"
    ]
    ...
}
```

<br/>


## Public-Market Depth

This endpoint retrieves the current order book of a specific pair.

**HTTP Request**

+ GET <code>https://kline.zbg.com/api/data/v1/entrusts</code>

**Request Parameter**

Parameter       |  Data Type  |Required|	Description
----------------|------------|--------|--------
marketName      |   string   |  true  |	trading pair
dataSize        |	integer   |	true  |	The number of gears represents 5 gears for each trade, and the maximum is 200

**Response Content**


**Use Case**

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

<br/>


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


**Use Case**

```json
"datas": [
    [
        "T",            // data type       
        "90",           // market ID
        "1532183063",   // The time stamp
        "ETC_USDT",     // The name of the market
        "bid",          // ask and bid type 
        "827.90",       // deal price
        "515.50"        // deal quantity
    ],
    ......
]
```

--------------------------------------------------------------------------------

<br/>
<br/>


# Account

    Accessing Accounts related interfaces such as None specifically requires signature authentication

## Get Account Information

Query the current user information and its list of sub-accounts

**HTTP Request**

+ GET <code>/exchange/api/v1/account/accounts</code>

**Request Parameter**

None


**Response Content**

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

**Use Case**

```json
"datas":{
    "subs":["7nT3xWzJxNA","7mucaa0JNa4"],
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

<br/>

## Search Account

Search user information

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


**Use Case**

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

<br/>

## Get Account Balance

Query the asset information of the current user

**HTTP Request**

+ GET <code>/exchange/api/v1/account/balance</code>

**Request Parameter**

None


**Response Content**

The data for turn is a list, field description

Field           | Data Type |	Description
----------------|--------------|--------
user-id         |	string  |	user ID
currency        |	string  |	currency name
balance         |	decimal |	balance
available       |	decimal |	availablebalance
freeze          |	decimal |	frozen(not available)

**Use Case**

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

<br/>

## Get Account Balance for a Single Currency

This endpoint returns the balance of an account specified by currency name.

**HTTP Request**

+ GET <code>/exchange/api/v1/account/balance/{currency}</code>

**Request Parameter**

Parameter       |  Data Type  | Required | Description
----------------|------------|--------|--------
currency     |	string   |	true  |	currency name


**Response Content**

Field           | Data Type |	Description
----------------|-----------|--------
user-id         |	string  |	user ID
currency        |	string  |	currency name
balance         |	decimal |	balance
available       |	decimal |	availablebalance
freeze          |	decimal |	frozen(not available)

**Use Case**

```json
"datas":{
    "freeze":"4224.4187921662601158",
    "available":"18255.174060294635375085",
    "user-id":"7eU4wsh3XLE",
    "currency":"usdt"
}

```

<br/>

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

<br/>

## Get the Aggregated Balance of all Sub-accounts of the Current User

The main account queries the various currencies of all sub accounts under it.

**HTTP Request**

+ GET <code>/exchange/api/v1/account/sub/aggregate-balance</code>

**Request Parameter**

None

**Response Content**

The data for turn is a list, field description

Field           | Data Type |	Description
--------------------|-----------|--------
currency            |	string  | Currency name
balance             |	decimal | All balance of this currency under sub account（available balance and frozen balance)
freeze              |	decimal | All frozen balance of this currency under sub account
available           |	decimal | All available balance of this currency under sub account

**Use Case**

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

<br/>

## Get Account Balance of a Sub-Account

The main account checks the currency balance of the specified sub-account

**HTTP Request**

+ GET <code>/exchange/api/v1/account/sub/balance/{sub-uid}</code>

**Request Parameter**

Parameter       |  Data Type  | Required | Description
----------------|------------|--------|--------
sub-uid      |   string   |  true  |	Sub-Account Id


**Response Content**

The data for turn is a list, field description

Field           | Data Type |	Description
--------------------|-----------|--------
currency        |	string  |	currency name
balance         |	decimal |	balance
available       |	decimal |	available balance
freeze          |	decimal |	frozen(not available)


**Use Case**

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

------------------------------------------------------------------------------

<br/>

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

**Use Case**

```json
"datas":
{
    "address": "19cdJwd3j6ArHNhiYoWpN8cJq9ash7WDDC",
    "currency":"zt"
}
```

<br/>

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


**Use Case**

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

<br/>


## Query Withdraw Address

Get the withdrawal address of currency filled in on this platform

**HTTP Request**

+ GET <code>/exchange/api/v1/account/withdraw/address</code>

**Request Parameter**

Parameter       |  Data Type  | Required | Description
--------------|------------|--------|--------
currency      |   string   |  true  |	currency，btc,ltc...(ZBGsupport [currencies])


**Response Content**

The data for turn is a list, field description

Field       |  Data Type  | Required | Description
------------|-----------|--------|-----------
currency    |	string  | true   |	currency name
address     |	string  | true   |	withdrawal address	
remark      |	string  | false   |	remark


**Use Case**

```json
"datas":
{
    "address": "19cdJwd3j6ArHNhiYoWpN8cJq9ash7WDDC",
    "currency":"zt"
}
```

<br/>

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

The data for turn is the withdrawal ID of a string type

**Use Case**

```json
"data":"W6574581089036550144"
```

<br/>


## Cancel a Withdraw Request


**HTTP Request**

+ POST <code>/exchange/api/v1/account/withdraw/cancel/{withdraw-id}</code>

**Request Parameter**

Parameter       |  Data Type  | Required | Description
----------------|------------|--------|--------
withdraw-id     |   string   |  true  | Withdraw ID, fill in the path



**Response Content**

Field    | Data Type  |	Description
------------|-----------|-----------
withdraw-id |	string  |	withdraw ID



**Use Case**

```json
"datas":"W6574581089036550144"
```

<br/>


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

**Use Case**

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

--------------------------------------------------------------------------------

<br/>
<br/>

# Spot Trading 
    
    Access to the spot transactions interface such as None specifically requires signature authentication

## Place a New Order

Send a new order to ZBG for matching

> **Important reminder message:**
> In order to prevent users from misoperation, the price of purchase order should not be three times higher than the recent deal price, and the price of selling order should not be less than one third of the recent deal price

**HTTP Request**

+ POST <code>/exchange/api/v1/order/create</code>

**Request Parameter**

Parameter       |  Data Type  | Required | Description
----------|------------|--------|--------
symbol   |   string   |  true |	trading pair，example：btc_usdt, eth_usdt...(zbg supported [symbols])
side    |   string   |  true |	order type，include buy，sell
amount      |   decimal      |  true |	quantity
price      |   decimal      |  true |	The price of the order


**Response Content**

The data for turn is the order number of string type

> Get this order number does not mean that the order was placed successfully. Users can place an order through [Get Order Details](#get-the-order-detail-of-an-order) enquire order status.

> The order will be rejected if the precision of quantity and price exceeds the preset value of currency.


**Use Case**

```json
"datas": "E6580725150600146944"
```

<br/>


## Submit Cancel for an Order

This endpoint submits a request for cancel order

> This only submit the cancel request, the actual result of the canel request needs to be checked by order status or match result endpoints

**HTTP Request**

+ POST <code>/exchange/api/v1/order/cancel</code>

**Request Parameter**

Parameter       |  Data Type  | Required | Description
----------|------------|--------|--------
symbol   |   string   |  true |	trading pair，例如：btc_usdt,eth_usdt
order-id    |   string   |  true |	order号


**Response Content**

None


<br/>

## Submit Cancel for Multiple Orders by Criteria

This endpoint submit cancellation for multiple orders at once with given criteria.

> This only submit the cancel request, the actual result of the canel request needs to be checked by order status or match result endpoints

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

**Use Case**

```json
"datas": 2
```

<br/>

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

**Use Case**

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

<br/>


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

> Historical orders that have been completed or withdrawn are removed to the backup table by setting history=true to search


**Response Content**

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


**Use Case**

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

<br/>


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


**Use Case**

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

<br/>


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


**Use Case**

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

----------------------------------------------------------------------------------

<br/>
<br/>


# Contract


## Public-Get Contracts

此接口获取ZBG平台支持的所有合约。

**HTTP 请求**

+ GET <code>/exchange/api/v1/future/common/contracts</code>

**请求参数**

无

**返回字段**

字段名称            | 数据类型  |	描述
--------------------|-----------|--------
contractId       |	int  |	合约ID
symbol              |	string  |	合约名称
commodityId       |	int  |	商品币种ID
currencyId      |	int  |	货币币种ID
lotSize     |	string |	最小交易单位
priceTick    |	string |	最小报价单位
makerFeeRatio   |	string  | maker 手续费率
takerFeeRatio   |	string  | taker 手续费率

**用例**

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
            "priceTick":"0.000100000000000000"
        },
        {
            "symbol":"BCH_USDT",
            "lotSize":1,
            "contractId":1000011,
            "takerFeeRatio":"0.000750000000000000",
            "commodityId":"6",
            "currencyId":"7",
            "makerFeeRatio":"0.000250000000000000",
            "priceTick":"0.050000000000000000"
        },
        ...
]
```

<br/>



## 公共-获取合约可用币种

此接口获取ZBG平台支持的合约币种列表。

**HTTP 请求**

+ GET <code>/exchange/api/v1/future/common/currencies</code>

**请求参数**

无

**返回字段**

字段名称        |  数据类型  |	描述
----------------|------------|--------
currencyId            |   string   |	合约方币种id
symbol            |   string   |	币种名称
displayPrecision       |	int  |	显示位数
enabled        |	int   |	是否可充提，1:可用，0:不可用


**用例**

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

<br/>



## 公共-合约K线数据

此接口返回历史K线数据。

**HTTP 请求**

+ GET <code>/exchange/api/v1/future/market/klines</code>

**请求参数**


参数            |  数据类型  |是否必须|	描述
----------------|------------|--------|--------
symbol      |   string   |  true  |	交易对
range            |	string  |	true  | K线类型,支持1M; 3M; 5M; 15M; 30M; 1H; 2H; 4H; 6H; 12H; 1D; 1W;
endTime        |	long   |	false  |	时间，与Kline 返回时间位数一致



**返回字段**

lines数据结构：List<List>
如：

    [
        [${时间}, ${开市价格}, ${最高价格}, ${最低价格}, ${闭市价格}, ${成交量}]
    ]

**用例**

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


<br/>


## 公共-单个合约行情

此接口获取单个合约的行情快照。


**HTTP 请求**

+ GET <code>/exchange/api/v1/future/market/ticker</code>

**请求参数**


参数            |  数据类型  |是否必须|	描述
----------------|------------|--------|--------
symbol      |   string   |  true  |	交易对


**返回字段**

字段名称    | 数据类型  |	描述
------------|-----------|-----------
mt  | number  | 消息类型：messageType
ai  | number  | 应用ID：applId
ci  | number  | 合约ID：contractId
sb  | string  | 合约名称：symbol
td  | number  | 交易日期：tradeDate
te  | number  | 交易时间：time
lp  | string  | 最新价格：lastPrice
mq  | string  | 成交量：matchQty
nt  | number  | 成交比数：numTrades
op  | string  | 开盘价：openPrice
ph  | string  | 最高价：priceHigh
pl  | string  | 最低价：priceLow
hph  | string  | 历史最高价：historyPriceHigh
hpl  | string  | 历史最低价：historyPriceLow
tt  | string  | 当日成交额：totalTurnover
tv  | string  | 当日成交量：totalVolume
tbv  | string  | 买总委托数：totalBidVol
tav  | string  | 卖总委托数：totalAskVol
pp  | string  | 上一交易日收盘价：prevPrice
cp  | string  | 标记价格，结算价：clearPrice
pv  | string  | 总持仓量：posiVol
pcr  | string  | 当日涨跌幅度：priceChangeRadio
pc  | string  | 当日涨跌：priceChange
lui  | number  | 行情序号：lastUpdateId
cs  | number  | 交易对状态：contractStatus
dp  | string  | 交割价格：deliveryPrice
fr  | null  | 资金费率：fundingRate
pfr  | null  | 预测资金费率：predictionFundingRate
pi  | null  | 溢价指数：premiumIndex
ppi  | null  | 预溢价指数：predictionPremiumIndex
fb  | string  | 合理基差：fairBasis
ts  | number  | 交易信号，1=买，2=卖：tradingsignal
sl  | number  | 1~100,交易信号强度 ：signalLevel
ip  | string  | 标的指数价格：indexPrice
bids  | string[]  | 买档位
asks  | string[]  | 卖档位

bids与asks结构：List<List>
如

    [
        [${价格}, ${数量}]
    ]

**用例**

```json
"datas":{
        "mt":4,
        "ai":2,
        "ci":999999,
        "sb":"BTC_ZUSD",
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

<br/>

## 公共-所有合约行情

获得所有合约的行情快照

**HTTP 请求**

+ GET <code>/exchange/api/v1/future/market/tickers</code>

**请求参数**


**返回字段**

返回一个list，子项具体各字段含义，请参考[单个合约行情](#公共-单个合约行情)接口

**用例**

```
"datas":[
    {}
]
```

<br/>

## 公共-合约深度

此接口返回指定合约的当前深度数据。

**HTTP 请求**

+ GET <code>/exchange/api/v1/future/market/depth</code>

**请求参数**

参数            |  数据类型  |是否必须|	描述
----------------|------------|--------|--------
symbol      |   string   |  true  |	交易对
size        |	integer   |	true  |	档位数，默认20


**返回字段**

见如下用例

**用例**

```json
"datas":{
    "asks": [               // 卖盘
        [
            "824.898",      // 第3档卖盘价格
            "305.95"        // 第3档卖盘量
        ],
        [
            "741.435",      // 第2档卖盘价格
            "400.88"        // 第2档卖盘量
        ],
        [
            "741.47",       // 第1档卖盘价格
            "392.99"        // 第1档卖盘量
        ]
        
    ],
    "bids": [               // 买盘
        [
            "294",          // 第1档买盘价格
            "240.32"        // 第1档买盘量
        ],
        [
            "247",          // 第2档买盘价格
            "242.064"       // 第2档买盘量
        ],
        [
            "216",          // 第3档买盘价格
            "174.043"       // 第3档买盘量
        ]
    ],
    "timestamp": "1532183394"   // 请求时的时间戳，秒级别
}
```

<br/>


## 公共-合约历史成交记录

此接口返回指定交易对最新的交易记录。


**HTTP 请求**

+ GET <code>/exchange/api/v1/future/market/trades</code>

**请求参数**

参数            |  数据类型  |是否必须|	描述
----------------|------------|--------|--------
symbol      |   string   |  true  |	交易对


**返回字段**

trades的结构为：List<List>
如：
        
    [
        [${时间戳}, ${成交价格}, ${成交数量}, ${方向}]
    ]

见如下用例

**用例**

```json
"datas": [
        [1586504611360280, "6921.5", "12", -1],
        [1586504615863710, "6920.5", "79", 1],
        [1586504620979503, "6920.5", "16", 1],
    ......
]
```

## 合约下单

**HTTP 请求**

+ POST <code>/exchange/api/v1/future/place</code>

**请求参数**

参数            |  数据类型  |是否必须|	描述
----------------|------------|--------|--------
symbol      |   string   |  必须  |	合约名称
side  | integer  | 必须  | 买1，卖-1
price  | string  | 非必须  | order_type等于3（市价）时非必填
quantity  | string  | 必须  |  下单量
orderType  | integer  | 必须  | 1（限价），3（市价）
positionEffect  | number  | 必须  | 开仓1，平仓2
marginType  | number  | 必须  | 全仓1，逐仓2
marginRate  | string  | 必须  | 全仓时0，逐仓时>=0，保证金率
orderSubType  | integer  | 非必须  | 0（默认值），1（被动委托），2（最近价触发条件委托），<br/>3（指数触发条件委托），4（标记价触发条件委托）
stopPrice  | string  | 非必须  | 触发价格
stopCondition  | integer  | 非必须  | 0：默认，2：止损
minimalQuantity  | string  | 非必须  | 止损点位

备注：
    
    已实现订单类型：
    被动委托：
    order_sub_type: 1
    
    条件委托：
    order_sub_type: 2（最近价触发条件委托），3（指数触发条件委托），4（标记价触发条件委托）
    stop_price: 触发价格
    
    追踪止损：
    order_sub_type: 2（最近价触发条件委托），3（指数触发条件委托），4（标记价触发条件委托）
    minimal_quantity：止损点位
    stop_condition: 2（止损）

**返回字段**

正常时为order_id

**用例**

```json
"datas":"11585489047547210"
```
  
  <br/>

## 合约撤单

**HTTP 请求**

+ POST <code>/exchange/api/v1/future/cancel</code>

**请求参数**

参数            |  数据类型  |是否必须|	描述
----------------|------------|--------|--------
symbol      |   string   |  必须  |	合约名称
orderId  | String  | 必须  | 合约订单号


**返回字段**

无
  
  <br/>

## 合约一键撤单

**HTTP 请求**

+ POST <code>/exchange/api/v1/future/cancel-all</code>

**请求参数**

无

**返回字段**

无

<br/>

## 合约持仓查询


    持仓量 > 0 表示 多头，持仓量 <0 表示空头
    
    contract_side ：正向合约和反向合约
    
    价值= 持仓量*最新价*ContractUnit
    
    开仓价格 = 开仓金额/(持仓量*ContractUnit)
    
    标记价格： 行情获取
    
    强平价格：
    正向合约多头持仓：开仓价 + 开仓金额*(初始保证金率-维持保证金率)/持仓量
    正向合约空头持仓：开仓价 - 开仓金额*(初始保证金率-维持保证金率)/持仓量
    反向合约多头持仓：开仓价 - 开仓金额*(初始保证金率-维持保证金率)/持仓量
    反向合约空头持仓：开仓价 + 开仓金额*(初始保证金率-维持保证金率)/持仓量
    
    保证金： 初始保证金
    
    倍数： 1/初始保证率
    
    正向合约：
                   多头时未实现盈亏=持仓量*（最新价-开仓价）*ContractUnit；
                   空头时未实现盈亏=持仓量*（开仓价-最新价）*ContractUnit；
    反向合约：
                   空头时未实现盈亏=持仓量*（最新价-开仓价）*ContractUnit；
                   多头时未实现盈亏=持仓量*（开仓价-最新价）*ContractUnit；
    
    回报率：盈亏／ 价值
    
    减仓队列： 通过强减队列查询
    
    操作列： 当posiStatus为2时，不能平仓和市价全平
    
    已平仓仓位： 已实现盈亏，取值 closeProfitLoss

**HTTP 请求**

+ GET <code>/exchange/api/v1/future/positions</code>

**请求参数**

无

**返回字段**

字段名称    | 数据类型  |	描述
----------------|------------|--------
contractId  | integer  | 交易对
posiQty  | string  | 持仓量
openAmt  | string  | 开仓金额
initMargin  | string  | 初始保证金
posiStatus  | number  | 1正常，2等待强平
marginType  | integer  | 保证金类型，1全仓2逐仓 
closeProfitLoss  | string  | 已实现盈亏
initMargiRate  | string  | 初始保证率
maintainMarginRate  | string  | 维持保证金
frozenCloseQty  | string  | 平仓委托冻结量
frozenOpenQty  | string  | 开仓委托冻结量
contractUnit  | string  | 合约单位

**用例**

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

<br/>

## 查询合约当前委托

**HTTP 请求**

+ GET <code>/exchange/api/v1/future/orders</code>

**请求参数**

无

**返回字段**

字段名称    | 数据类型  |	描述
----------------|------------|--------
contractId  | number  | 交易对
orderId  | string    | 委托号
clOrderId  | string   | 客户订单编号
price  | string    | 委托价格
quantity  | string    | 委托数量
leftQuantity  | string    | 剩余数量
side  | number    | 买卖方向，1买，-1卖
placeTimestamp  | number    | 委托时间
cancelQty  | string    | 成交数量
matchAmt  | string    | 成交金额
positionEffect  | integer    | 开平标志，1开仓2平仓
marginType  | integer    | 保证金类型，1全仓2逐仓
fcOrderId  | string   | 强平委托号，空时为强平委托
orderType  | integer  | 委托类型（1：限价，2：市价）
stopPrice  | string  | 条件单触发价
orderSubType  | string  | 订单委托类型，0：默认值，1：被动委托，2：最近价触发条件，<br/>3 ：指数除非条件委托，4：标记价触发条件委托
stopCondition  | integer  | 0：默认，2：止损
minimalQuantity  | string  | 止损点位

**用例**

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
<br/>

## 查询合约历史委托

**HTTP 请求**

+ GET <code>/exchange/api/v1/future/orders/his</code>

**请求参数**

参数            |  数据类型  |是否必须|	描述
----------------|------------|--------|--------
symbol      |   string   |  必须  |	合约名称
side  | int  | 否  | -1:上一页，1：下一页
timestamp  | long  | 否  | 微秒级别
pageSize  | int  | 否  | 默认100

    备注
    用户选择了查询时间条件时：
    每次用户选择上一页，下一页时，timestamp需有值
    上一页：
       timestamp= 当前页第一条数据的timestamp
    下一页：
       timestamp= 当前页最后一条数据的timestamp
       


**返回字段**

字段名称    | 数据类型  |	描述
----------------|------------|--------
applId  | number  |   2：期货
timestamp  | number  | 下单时间戳，微秒级别
userId  | number  | 用户合约ID
contractId  | number  | 交易对
uuid  | string  | 唯一标志符
side  | number  | 买卖方向，1买，-1卖
price  | string  | 委托价格
quantity  | string  | 委托数量
orderType  | number  | 委托类型（1：限价，2：市价）
orderSubType  | number  | 订单委托类型，0：默认值，1：被动委托，2：最近价触发条件，<br/>3 ：指数除非条件委托，4：标记价触发条件委托
timeInForce  | number  |   订单有效时期类型：1:取消前有效，2:立即成交剩余撤销，未启用，<br/>3:全部成交否则撤销，未启用，4:五档成交剩余撤销，未启用，5:五档成交剩余转限价，未启用 
minimalQuantity  | string  | 止损点位
stopPrice  | string  | 条件单触发价
stopCondition  | number  | 止损止盈标志 1（止盈，未启用），2（止损，未启用），3（只减仓，未启用）
orderStatus  | number  | 委托状态
makerFeeRatio  | string  | Maker手续费率
takerFeeRatio  | string  | Taker手续费率
clOrderId  | string  | 客户订单编号
filledCurrency  | string  | 成交金额
filledQuantity  | string  | 成交数量
canceledQuantity  | string  | 取消数量 
matchTime  | number  | 撮合时间戳
positionEffect  | number  | 开平标志，1：开仓，2：平仓
marginType  | number  | 保证金类型，1全仓，2逐仓
marginRate  | string  | 保证金率，全仓时0，逐仓时>=0，
fcOrderId  | string  | 强平委托号，非空时为强平委托
deltaPrice  | string  | 标记价与委托价之差
frozenPrice  | string  | 资金计算价格

    orderStatus
    委托状态 0-未申报,1-正在申报,2-已申报未成交,3-部分成交,4-全部成交,5-部分撤单,6-全部撤单,7-撤单中,8-失效,11-缓存高于条件的委托,12-缓存低于条件的委托
**用例**

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

<br/>

## 查询合约资金流水

**HTTP 请求**

+ GET <code>/exchange/api/v1/future/assets/flow</code>

**请求参数**

参数            |  数据类型  |是否必须|	描述
----------------|------------|--------|--------
currencyName      |   string   |  否  |	币种名称
flowId  | long  | 否  | 业务类型
flowType  | long  | 否  | 流水ID
side  | int  | 否  | -1:上一页，1：下一页
startTime  | long  | 否  | 开始时间，微秒
endTime  | long  | 否  | 结束时间，微秒级别
pageSize  | int  | 否  | 默认10

    备注
    上一页时：side = -1  id为当前页的第一条数据的flow_id
    下一页时：side =1  id为当前页最后一条数据的flow_id
    
    流水类型：
     * 3001：期货手续费，
     * 3002：期货手续费抵后返还，
     * 3003：期货手续费抵扣消耗点卡，
     * 3004：期货平仓盈亏，
     * 3005：期货定期结算盈亏，
     * 3006：期货交割手续费，
     * 3007：资金费用 & 期货交割盈亏，
     * 3008：风险基金
     * 8001：资金划转
       


**返回字段**

字段名称    | 数据类型  |	描述
----------------|------------|--------
flowId  | number  | 流水ID
userId  | number  | 用户ID
applId  | number  | 2
currencyId  | number  | 货币ID
flowType  | number  | 流水类型
num  | number  | 金额
occureTime  | number  | 转账时间
createTime  | number  | 记录创建时间
uuid  | string  | 对应的记录id（转账id、成交id等）
note  | string  | 备注


**用例**

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
        "occureTime":1586485266453,
        "createTime":1586485266485,
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
        "occureTime":1586429992480,
        "createTime":1586429992507,
        "uuid":"11585489047547536",
        "note":"999999"
    },
    ...
]
```
<br/>

## 查询合约可用资金

**HTTP 请求**

+ GET <code>/exchange/api/v1/future/assets/available</code>

**请求参数**

参数            |  数据类型  |是否必须|	描述
----------------|------------|--------|--------

       


**返回字段**

字段名称    | 数据类型  |	描述
----------------|------------|--------
currencyId  | number  | 货币ID
currencyName  | String  | 货币
totalBalance  | string  | 总资产
available  | string  | 可用资金
frozenForTrade  | string  | 委托冻结资金
initMargin  | string  | 已占用保证金
frozenInitMargin  | string  | 委托冻结保证金
closeProfitLoss  | string  | 已实现盈亏
createTime  | number  | 记录创建时间


**用例**

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

<br/>

## 查询合约盈亏历史

**HTTP 请求**

+ GET <code>/exchange/api/v1/future/assets/profits</code>

**请求参数**

参数            |  数据类型  |是否必须|	描述
----------------|------------|--------|--------
currencyName      |   string   |  否  |	币种名称
startDate  | string  | 否  | 开始时间,如‘2018-01-01’
endDate  | string  | 否  | 结束时间,如‘2018-01-01’
pageNum  | int  | 否  | 当前页：默认：1
pageSize  | int  | 否  | 默认10
       


**返回字段**

字段名称    | 数据类型  |	描述
----------------|------------|--------
totalRow  | number  | 总记录数
totalPage  | number  | 总页数
pageSize  | number  | 每页返回数
pageNum  | number  | 当前页
list  | object []  | 
├─userId  | number  | 用户ID
├─currencyId  | number  | 币种ID
├─totalMoney  | number  | 总资产
├─orderFrozenMoney  | number  | 冻结资产
├─closeProfitLoss  | number  | 已实现盈亏
├─startDate  | number  | 开始时间
├─endDate  | number  | 结束时间


**用例**

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
<br/>

## 查询合约历史强平

**HTTP 请求**

+ GET <code>/exchange/api/v1/future/fcorders</code>

**请求参数**

参数            |  数据类型  |是否必须|	描述
----------------|------------|--------|--------
symbol      |   string   |  否  |	合约名称
side  | int  | 否  | 买卖方向 1:买，-1:卖
startDate  | long  | 否  | 开始时间,毫秒
endDate  | long  | 否  | 结束时间,毫秒
pageNum  | int  | 否  | 当前页：默认：1
pageSize  | int  | 否  | 默认10
       


**返回字段**

字段名称    | 数据类型  |	描述
----------------|------------|--------
totalRow  | number  | 总记录数
totalPage  | number  | 总页数
pageSize  | number  | 每页返回数
pageNum  | number  | 当前页
list  | object []  | 
├─timestamp  | number  | 委托时间
├─userId  | number  | 用户ID
├─contractId  | number  | 交易对
├─uuid  | string  | 委托编号
├─applId  | number  | 应用标识
├─side  | number  | 买卖方向
├─bankruptcyPrice  | number  | 破产价格
├─closeQty  | number  | 委托量
├─filledCurrency  | number  | 成交金额
├─filledQuantity  | number  | 成交量
├─canceledQuantity  | number  | 撤单数量
├─orderStatus  | number  | 委托状态
├─feeRatio  | number  | 手续费
├─marginType  | number  | 保证金类型

    orderStatus
    0: 未申报；1:正在申报；2:已申报未成交；3:部分成交；4:全部成交；5部分撤单；6:全部撤单；7:撤单中；8:失效

**用例**

```json

```

<br/>

## 查询合约历史强减

**HTTP 请求**

+ GET <code>/exchange/api/v1/future/florders</code>

**请求参数**

参数            |  数据类型  |是否必须|	描述
----------------|------------|--------|--------
symbol      |   string   |  否  |	合约名称
side  | int  | 否  | 买卖方向 1:买，-1:卖
startDate  | long  | 否  | 开始时间,毫秒
endDate  | long  | 否  | 结束时间,毫秒
pageNum  | int  | 否  | 当前页：默认：1
pageSize  | int  | 否  | 默认10
       


**返回字段**

字段名称    | 数据类型  |	描述
----------------|------------|--------
totalRow  | number  | 总记录数
totalPage  | number  | 总页数
pageSize  | number  | 每页返回数
pageNum  | number  | 当前页
list  | object []  | 
├─contractId  | number  | 交易对ID
├─uuid  | string  | 委托编号
├─applId  | number  | 应用标识
├─lossUserId  | number  | 强减用户（亏损方）
├─profitUserId  | number  | 被强减用户（盈利方）
├─execId  | string  | 成交编号
├─side  | number  | 买卖方向
├─timestamp  | number  | 委托时间
├─lossMarginType  | number  | 强减用户（亏损方）保证金类型
├─profitMarginType  | number  | 被强减用户（盈利方）保证金类型
├─closePrice  | number  | 强减价格
├─closeQty  | number  | 强减数量
├─closeAmt  | number  | 强减金额
├─lossSubsidy  | number  | 亏损方补偿金额
├─profitSubsidy  | number  | 盈利方补偿金额


**用例**

```json

```

<br/>

## 调整保证金率

**HTTP 请求**

+ POST <code>/exchange/api/v1/future/adjust-margin-rate</code>

**请求参数**

参数            |  数据类型  |是否必须|	描述
----------------|------------|--------|--------
symbol      |   string   |  是  |	合约名称
initMarginRate  | string  | 是  | 初始保证金率
marginType  | int  | 是  | 保证金类型，1全仓，2逐仓
       


**返回字段**

字段名称    | 数据类型  |	描述
----------------|------------|--------


**用例**

```json

```
<br/>

## 调整保证金

**HTTP 请求**

+ POST <code>/exchange/api/v1/future/adjust-margin</code>

**请求参数**

参数            |  数据类型  |是否必须|	描述
----------------|------------|--------|--------
symbol      |   string   |  是  |	合约名称
margin  | string  | 是  | 保证金
       


**返回字段**

字段名称    | 数据类型  |	描述
----------------|------------|--------


**用例**

```json

```


----------------------------------------------------------------------------------

<br/>
<br/>

# WebSocket Market Data

## General

**接入URL**

<code>wss://kline.zbg.com/websocket</code>


**Heartbeat and Connection**

When the user's Websocket client is connected to firecoin Websocket server, if there is no data push for a long time, the connection may be broken. The client can send ping messages regularly to ensure continuous connection.

    {"action":"PING"}

When the user's Websocket client receives this heartbeat message, it should turnpong message:

    {"dataType":null,"action":"PING","msg":"action not support","code":"5021"}


**Subscribe to Topic**

After successfully establishing a connection with the Websocket server, the Websocket client sends the following request to subscribe to a specific topic:

    {"action":"ADD", "dataType":"message topic", "dataSize":1}

- *action*: request action type，ADD:Increase data subscription，DEL:Remove data subscription
- *dataType*: The data type of the request is detailed in the following sections
- *dataSize*: Request's quantity, determines the quantity of the first full quantity data, and turns a piece of data if it is not passed or 0.

**Use Case**

    {"action":"ADD", "dataType":"336_ENTRUST_ADD_ZT_USDT", "dataSize":1}

After successful subscription, if it is the first subscription, the Websocket client will receive the full quantity message:

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
After that, the Websocket client will receive an update pushed by the server as soon as the subscribed topic is updated.

```json
["E","336","1569311848","ZT_USDT","BID","0.0399","7897.5386"]
```

**Unsubscribe**

The unsubscribe format is as follows：

```json
{"action":"DEL", "dataType":"message topic"}
```

**Use Case**

```json
{"action":"DEL", "dataType":"336_ENTRUST_ADD_ZT_USDT"}
```



----------------------------------------------------

<br/>

## Market Candlestick

**Topic**

Once the k-line data is generated, the Websocket server will push it to the client via this subscription topic interface.

<code>symbol-id_**KLINE**_period_symbol</code>

**Request Parameter**

Parameter       |  Data Type  | Required | Description
----------|------------|--------|--------
symbol-id   |   string   |  true |	trading pair ID，Refer to the [symbols] interface for the turn value ID.
period   |   string   |  true |	Kline period, support :1M, 5M, 15M, 30M, 1H, 1D
symbol   |   string   |  true |	trading pair，such as：btc_usdt,eth_usdt

**Response Content**

The response Data is a list,list of data definition

[K, symbol-id, symbol, timestamp, open, high, low, close, volume, change, dollar-rate, period, conversion, amount.

    amount : Aggregated trading value during the interval (in quote currency)
    volume : Aggregated trading volume during the interval (in base currency)

If it is the first time, the full quantity data is turned.

**Use Case**

Request data：

```json
{"action":"ADD", "dataType":"336_KLINE_1M_ZT_USDT","dataSize":1000} 
```

Response data：

```json
[
    ["K","336","zt_usdt","1569314760","0.0405","0.0405","0.0404","0.0404","42888.96","-0.2469","7.12","1M","false","1735.19"],
    ["K","336","zt_usdt","1569314700","0.0404","0.0405","0.0404","0.0404","63246.27","0","7.12","1M","false","2557"],
    ...
]
```

Subsequent increase in quantity data:
```json
["K","336","zt_usdt","1569314940","0.0405","0.0405","0.0404","0.0405","50044.14","0","7.12","1M","false","2022.62"]
```

<br/>

## Market Depth

**Topic**

When the market depth changes, this topic sends the latest market depth update data:

<code>symbol-id_**ENTRUST_ADD**_symbol</code>

**Request Parameter**

Parameter       |  Data Type  | Required | Description
----------|------------|--------|--------
symbol-id   |   string   |  true |	trading pairID，Refer to the [symbols]
symbol   |   string   |  true |	trading pair，example：btc_usdt,eth_usdt, Refer to the [symbols]

> DataSize: 100 at most

**Response Content**

The repsonse Data is a list, list of data definition：

Full data field description：

[AE, symbol-id, symbol, timestamp, asks:[[price, quantity]], bids[[price, quantity]]]

Increased quantity data field description：

[E, symbol-id, timestamp, symbol, ask/bid, price, quantity]

**Use Case**

Request data：

```json
{"action":"ADD", "dataType":"336_ENTRUST_ADD_ZT_USDT"}
```


First Response data：

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

Subsequent increase in quantity data.

```json
 ["E","336","1569311848","ZT_USDT","BID","0.0399","7897.5386"]
```

<br/>

## Market Trade

**Topic**

This topic provides the latest deal details in the market:

<code>symbol-id_**TRADE**_symbol</code>

**Request Parameter**

Parameter       |  Data Type  | Required | Description
----------|------------|--------|--------
symbol-id   |   string   |  true |	trading pair ID，Refer to [symbols]
symbol   |   string   |  true |	trading pair，such as：btc_usdt,eth_usdt，Refer to [symbols]

> data Size article 50 the most

**Response Content**

The repsonse Data is a list,list of data definition.

Data field description: 

[T, symbol-id, symbol, timestamp, ask/bid, price, quantity]


**Use Case**

Request data：

```json
{"action":"ADD", "dataType":"336_TRADE_ZT_USDT", "dataSize":2}
```


First Response data：

```json
[
    ["T","336","1569316972","ZT_USDT","bid","0.0404","11895.3578"],
    ["T","336","1569316959","ZT_USDT","ask","0.0405","22546.8484"]
]
```

Subsequent increase in quantity data.

```json
 ["T","336","1569316988","ZT_USDT","bid","0.0405","25038.6289"]
```

<br/>

## Market Tickers

**Topic**

Topics provide the latest market overview within 24 hours.：

<code>symbol-id_**TRADE_STATISTIC_24H**</code>

> The type data is refreshed every 5 seconds.

**Request Parameter**

Parameter       |  Data Type  | Required | Description
----------|------------|--------|--------
symbol-id   |   string   |  true |	ALL or trading pair id，Refer to the[symbols]

> when symbol-id = ALL ，Turn the 24-hour market for all trading pairs

> data Size Article 10 the most

**Response Content**


Data field description: 

{
    "trade_statistic":[
        [ symbol-id, close, high，low, volume，change, Latest 6H closing price list, bid，ask,amount],
        ....
    ]
}

    Latest 6H closing price list ：[[serialnumber, close], [serialnumber, close], [serialnumber, close]]
    amount : Aggregated trading value during the interval (in quote currency)
    volume : Aggregated trading volume during the interval (in base currency)

**Use Case**

Request data：

```json
{"action":"ADD", "data Type":"ALL_TRADE_STATISTIC_24H"}
```

Response data：

```json
{"trade_statistic":[
    ["5140","0.0199","0.0199","0.0111","27597.0704","7.57","[[1, 0.0199]]","0.012","0.0457","504.7737"],
    ["5020","203823.8","219584","193014.1","21.8465","-4.83","[[1, 204168.8], [2, 207395.2], [3, 205103.6], [4, 206200.9], [5, 204334.9], [6, 203823.8]]","201002.4","206644.9","4524612.3764"],
    ["5021","28691.3","30639.6","27704.9","29.8016","-4.69","[[1, 28519.8], [2, 29109.5], [3, 28280], [4, 28667.7], [5, 28618], [6, 28691.3]]","28334.3","29047.8","864655.9334"],
]}
```

Request data：

```json
{"action":"ADD", "dataType":"5140_TRADE_STATISTIC_24H"}
```

Response data：

```json
{"trade_statistic":[
    ["5140","0.0199","0.0199","0.0111","27597.0704","7.57","[[1, 0.0199]]","0.012","0.0457","504.7737"]
]}
```

<br/>

## Order Change

**Topic**

Topics provide user order updates

<code>symbol-id_**RECORD_ADD**_user-id_symbol</code>

<!--> This type data is not open to ordinary users, so you need to apply for the configuration, and after the application, you need to make a transaction to trigger the data initialization.-->

**Request Parameter**

Parameter       |  Data Type  | Required | Description
----------|------------|--------|--------
symbol-id   |   string   |  true |	ALL 或trading pair ID，Refer to the [symbols]
user-id|   string   |  true |	user ID
symbol   |   string   |  true |	trading pair，example：btc_usdt,eth_usdt，Refer to the [symbols]

> data Size Article 100 the most

**Response Content**

Full data field description：[AR, market ID, user ID, The time stamp, [OrderNo, type, status, price, entrust quantity, complete quantity, complete amount, average price, establish The time stamp]]

Increased quantity data field description.：[R, market ID, user ID, The time stamp, OrderNo, type, status, price, entrust quantity, complete quantity, complete cash amount,Average price, create The time stamp

type：1:buy, 0:sell

status :  0:created 1:canceled 2: filled 3:partial-filled

**Use Case**

Request data：

```json
{"data Type":"336_RECORD_ADD_7eOUtLBFXTU_ZT_USDT","dataSize":50,"action":"ADD"}
```

Response data：

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

增quantity数据：

```json
["R","336","7eOUtLBFXTU","1569328042","E6582238884078301184",1,0,"0.039","10","0","0","","1569328042"]
```

<br/>

[currencies]:#public-get-all-supported-currencies
[symbols]:#public-get-all-supported-trading-symbols

---------------------------------------------------------------------

<div style="text-align: right;font-size:0"> created by zhangzp at 2019-09-21 </div>
<script>
    var ENV = '<%= ENV %>';
    function getLocalStorage (name) {
            var local = window.localStorage.getItem(name);
            try {
                var result = JSON.parse(decodeURIComponent(local));
                return result;
            } catch (e) {
                return local;
            }
    }
    var userinfo  = getLocalStorage(ENV + 'userInfo')
    console.log(userinfo)
    if(!userinfo){
        $('.createApi').attr("href","/login?path=/u/api");
        $('#payout').attr("href","/login?path=/u/payout");
    }
</script>

