---
title: ZBG API 文档

language_tabs: # must be one of https://git.io/vQNgJ
  - json

toc_footers:
  - <a href="https://www.zbg.fun/" class="createApi">ZBG Exchange</a>

includes:

search: true
---

# 更新日志

## 2020-03-31

增加[sdk](#sdk)

## 2019-12-09 

修改行情类接口：

* 新增接口[[/exchange/api/v1/commom/trade-history](#534bcb6bde)]，查询历史的成交记录
* 接口[[https://kline.zbg.fun/api/data/v1/entrusts](#060bae00ac)]放大盘口深度
* 接口[[https://kline.zbg.fun/api/data/v1/trades](#e6bf273c4e)]放大查询数量限制

## 2019-11-21 

* 接口[[/exchange/api/v1/account/balance](#870c0ab88b)]增加balance返回字段
* 新增接口[[/exchange/api/v1/account/balance/{currency}](#4a2b79e5d6)]
* 修改签名规则，新增[API 访问口令](#c64cd15fdc)字段，新增JS签名调用示例代码

## 2019-10-09 

* 新增搜索账户接口[[/exchange/api/v1/account/search](#2a0d0be224)]
* 新增Websocket模块
* 接口[[/exchange/api/v1/common/symbols][symbols]]增加返回字段ID
* 接口[[/exchange/api/v1/common/currencys][currencys]]增加返回字段ID

## 2019-09-21 

创建文档



# 简介

## API简介

欢迎使用 ZBG API！您可以通过使用此API获取本平台市场行情数据，进行交易，并且管理你的账户。

此API文档接口路径、字段命名贴合行业主流用法，让您的程序从其他主流交易所更容易切换。
[旧版](/help/restApi)有所有接口demo，但和行业主流用法习惯、接口、字段命名有部分差距。

文档右侧是针对请求参数以及响应结果的示例。

<aside class="notice">
如果在使用过程中有任何问题，请联系我们技术讨论QQ群：829230107，我们将会为您做出最权威的解答.
</aside>



## 子账号

子账号可以用来隔离资产与交易，资产可以在母子账号之间划转； 子账号用户只能在子账号内进行交易，并且子账号之间资产不能直接划转，只有母账号有划转权限。

子账号拥有独立的登陆账号密码和 API Key。

***子账户功能说明:***

1. 主账户可以将部分资金划转到子账户（资金相互划转不收手续费），子账户资产可独立管理操作；
2. 用户可授权委托其他机构或人员对子账户进行管理；
3. 用户可以通过API对子账户进行交易操作（API只能通过主账号开启和关闭）；
4. 子账户不能进行下列操作：法币交易；提币；上币申请；设置和修改密码；绑定邮箱和手机号等。

子账号可以访问所有公共接口，包括基本信息和市场行情，子账号可以访问的私有接口如下：


接口 | 说明
-----| -----
[GET  /exchange/api/v1/account/balance](#870c0ab88b)  | 账户余额
[POST /exchange/api/v1/order/create](#fd6ce2a756)  | 下单
[POST /exchange/api/v1/order/cancel](#4e53c0fccd)  | 撤销订单
[POST /exchange/api/v1/order/batch-cancel](#4e53c0fccd)  | 批量撤销订单
[GET  /exchange/api/v1/order/open-orders](#49333e98d3)  | 查询当前未成交完成的订单
[GET  /exchange/api/v1/order/orders](#515c1e61e5)  | 查询历史订单
[GET  /exchange/api/v1/order/detail](#92d59b6aad)  | 查询订单详情
[GET  /exchange/api/v1/order/trades](#b546ba2cec)  | 查询成交明细


<aside class="notice">
其他接口子账号不可访问，如果尝试访问，系统会返回错误码：6041。
</aside>



# 接入说明

## 接入 URLs

REST API

<code>https://www.zbg.fun</code>

域名有时会存在被墙或延迟高等情况，可暂时使用备用域名:

- https://www.zbgpro.com
- https://www.zbgpro.net
- https://www.zbg.kim

Kline API

<code>https://kline.zbg.fun</code>

备用域名:

- https://kline.zbgpro.com
- https://kline.zbgpro.net
- https://kline.zbg.kim

WebSocket 

<code>wss://kline.zbg.fun/websocket</code>

备用域名:

- wss://kline.zbgpro.com/websocket
- wss://kline.zbgpro.net/websocket
- wss://kline.zbg.kim/websocket



## SDK和代码示例

SDK (推荐)

[Java](https://github.com/zbgapi/zbg-api-v1-sdk/tree/master/zbg-java-sdk-api)
|
[Python](https://github.com/zbgapi/zbg-api-v1-sdk/tree/master/zbg-python-sdk-api)
|
[ccxt](https://github.com/zbgapi/ccxt)


## 接口类型

本章节主要为接口类型的细节分以下两个方面：

- 公共接口
- 私有接口

**公共接口**

公共接口可用于获取配置信息和行情数据。公共请求无需认证即可调用。

**私有接口**

私有接口可用于订单管理和账户管理。每个私有请求必须使用规范的验证形式进行签名。

私有接口需要使用您的API key进行验证。您可以在<a href="/u/api" class="createApi">这里</a>生成API key。



## 请求格式

**所有的API请求都以GET或者POST形式发出。**

**对于GET请求，所有的参数都在路径参数里；对于POST请求，所有参数则以JSON格式发送在请求主体（body）里。**


## 返回格式

```json
{
"datas":null,                 // 接口返回数据主体
"resMsg":
    {
        "code":"1",          // 错误码
        "message":"success!" // 提示信息
    }
    
}
```

所有的接口返回都是JSON格式。在JSON最上层有几个表示请求状态和属性的字段："code", 和 "message"。 实际的接口返回内容在"datas"字段里。返回码为1是表示请求成功，其他为请求失败，详细请参考[错误码列表](#e08c1d4f9a)



## 签名认证

> java 签名用例如下：

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
     * 这里使用Spring自带的Http请求工具，用户也可使用其他开源项目
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
        // 如果用户在创建API Key时设置了，则必传
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
     * 不为空、不为空字符串、不为双引号、不为空{}
     */
    private boolean isEmpty(String source) {
        return source == null || source.isEmpty() || source.equals("\"\"") || source.trim().equals("{}");
    }

    /**
     * post 请求接口，ContentType 统一都是采用 appliation/json 方式
     *
     * @param url   请求接口
     * @param param 请求参数
     * @return 返回信息
     * @throws BuzException 如果请求失败
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
        // 如果用户在创建API Key时设置了，则必传
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

> md5摘要方法如下：


```java
public static final String ALGORITHM_MD5 = "MD5";

/**
 * md5加密
 *
 * @param message 源字符串
 * @return 加密字符串
 */
public static String md5(String message) {
    return StringKit.toHex(messageDigest(message, ALGORITHM_MD5));
}

/**
 * 给字节数组生成摘要
 *
 * @param message   字符串
 * @param algorithm 摘要算法
 * @return 摘要字节数组
 */
private static byte[] messageDigest(String message, String algorithm) {
    return messageDigest(message.getBytes(UTF_8), algorithm);
}

/**
 * 给字节数组生成摘要
 *
 * @param message   字节码数组
 * @param algorithm 摘要算法
 * @return 摘要字节数组
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

> StringKit.toHex() 方法如下：

```java
/**
 * 首先初始化一个字符数组，用来存放每个16进制字符
 */
public static final char[] HEX_DIGITS = {'0', '1', '2', '3', '4', '5', '6', '7', '8', '9', 'A', 'B', 'C', 'D', 'E', 'F'};
/**
 * 转16进制字符串
 *
 * @param bytes 字节码数组
 * @return 16进制字符串
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

> JS 签名调用用例：

```js
// GET 方法请求签名，以 查询历史订单 接口为例。

//引入axios模块
const axios = require('axios')
// 引入MD5模块
const MD5 = require('MD5')

//请求数据
var data = {
    symbol: "zt_usdt",
    side: "buy",
    page: 1,
    size: 10
}
var timestamp = (new Date()).getTime();
//请求头
var headers = {
    Apiid: 'api key', // 替换成自己的 api key
    Clienttype: 5,
    Timestamp: timestamp,
    // 如果用户在创建API Key时设置了，则必传
    // 替换签名中的 passphrase
    Passphrase: MD5(timestamp.toString() + 'passphrase')
    // 签名顺序：Apiid+时间戳+请求参数+Apisecret
    // 替换签名中的 api key 和 api secret
    Sign: MD5('api key' + timestamp.toString() + dataStringify(data) + 'api secret')
};

var config = {
    url: 'https://www.zbgpro.net/exchange/api/v1/order/orders',
    headers: headers,
    method: 'GET',
    timeout: 3000,
    params: data
};

//参数内容拼串为所有参数按key值首字母排序后再按key1+value1+key2+value2...的形式拼串
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
// POST 方法请求签名，以 下单 接口为例

//引入axios模块
const axios = require('axios')
// 引入MD5模块
const MD5 = require('MD5')

//请求数据
var data = {
    symbol: 'zt_usdt',
    side: 'buy',
    amount: 1,
    price: 0.038
}
var timestamp = (new Date()).getTime();
//请求头
var headers = {
    Apiid: 'api key', // 替换成自己的 api key
    Clienttype: 5,
    Timestamp: timestamp,
    // 如果用户在创建API Key时设置了，则必传
    // 替换签名中的 passphrase
    Passphrase: MD5(timestamp.toString() + 'passphrase')
    // 签名顺序：Apiid+时间戳+请求参数+Apisecret
    // 替换签名中的 api key 和 api secret
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

**签名说明**

API 请求在通过网络传输的过程中极有可能被篡改，为了确保请求未被更改，除公共接口（基础信息，行情数据）外的私有接口均必须使用您的 API Key 和 API Secret 做签名认证，以校验参数或参数值在传输途中是否发生了更改。

签名接口组成部分：

+ 接口请求地址：如 https://www.zbg.fun/exchange/api/v1/order/orders。
+ API 访问密钥（Apiid）：您申请的API Key中的 Access Key，位于Header中。
+ API 访问口令(Passphrase) ：创建API Key 时生成的。
+ 时间戳（Timestamp）：您发出请求的时间的毫秒级时间戳，位于Header中。
+ 必选和可选参数：每个方法都有一组用于定义 API 调用的必需参数和可选参数。可以在每个方法的说明中查看这些参数及其含义。 
+ 签名（Sign）：签名计算得出的值，用于确保签名有效和未被篡改，Sign=md5(Apiid+Timestamp+参数内容拼串+apisecret) ，位于Header中。

即请求头都必须包含一下内容：

*Apiid* : 字符串类型的API Key。

*Timestamp* : 发起请求的时间戳。

*Sign* : 签名字符串(请参阅签名)。

*Passphrase* : 您在创建API密钥时指定的passphrase。

<aside class="notice">
Passphrase 请求头是并非直接使用passphrase请求口令，而使经过 md5(timestamp+Passphrase) 处理的字符串。
该字段非必填，取决于用户创建API Key时有没有指定该字段。
</aside>


**创建 API Key** 

您可以在 <a href="/u/api" class="createApi">这里</a> 创建 API Key。

API Key 包括以下部分

+ Access Key : API 访问密钥
+ Secret Key : 签名认证加密所使用的密钥（仅申请时可见）
+ Passphrase : API 访问口令


<aside class="warning">
这两个密钥与账号安全紧密相关，无论何时都请勿向其它人透露
</aside>

API Key和SecretKey将由ZBG随机生成和提供，Passphrase将由您提供以确保API访问的安全性。

Passphrase 为选填字段用户可以根据自己的需求选择提不提供该字段，如果填写了改字段，则在访问接口时请求头必须带上，后台会进行校验。


**时间戳**

<code>Timestamp</code>  请求头是毫秒级别的长整型时间戳。

时间戳和服务器时间前后相差60秒以上的请求将被系统视为过期并拒绝。如果您认为服务器和API服务器之间存在较大的时间偏差，那么我们建议您使用[获取服务器时间](#03482df8b5)的接口来查询API服务器时间


**签名**

以查询订单列表为例

<p><code>https://www.zbg.fun/exchange/api/v1/order/orders?symbol=zt_ust&side=buy&from=1&size=100</code></p>
1. 按照ASCII码的顺序对参数(key)进行排序，例如上面的请求排序后

<p><code>from=1</code></p>
<p><code>side=buy</code></p>
<p><code>size=100</code></p>
<p><code>symbol=zt_usdt</code></p>

2. 按照上面的顺序拼接字符串

<p><code>from1sizebuysize100sumbolzt_usdt</code></p>

3. 利用上一步的请求“字符串”和您的密钥生成一个数字签名：MD5(key + timestamp + 签名串 + secret)

<p><code>5fcbdb0862e10f9f6b885fdde42d58a1</code></p>

4. 将生成的数字签名、App key、时间戳等字段加入Header

<p><code>header.put("Apiid",id);</code></p>
<p><code>header.put("Timestamp", String.valueOf(timestamp));</code></p>
<p><code>header.put("Passphrase", md5(timestamp + passphrase));</code></p>
<p><code>header.put("Sign", "5fcbdb0862e10f9f6b885fdde42d58a1"</code></p>

<aside class="warning">
Post 请求下 Body 方式 application/json 的情况略有差异。这种情况下没有1、2步骤，直接将body转成json字符串当成签名字符串进入第3步。
</aside>



## 错误码
错误码|描述
----|-----
1|请求成功
6000| 参数缺失
6001|一般错误提示
6010|找不到市场
6011|你已开启谷歌登录验证，本次登录需要您的谷歌验证码
6012|未知操作类型!
6013|您没有进行手机认证和Google认证，暂时不能进行充值/提现业务，为了您的账号安全，请进行手机认证或Google认证。
6014|短信验证码错误，请重新获取
6019|您的地址请求频繁，请一小时后重试
6020|网络异常，请稍后重试
6021|限制提币操作
6033|请输入正确的谷歌验证码
6040|该账户已冻结，暂时不能操作
6041|该账户不是主账户，暂时不能操作
6043|用户已经被列入登录黑名单不能进行登录
6096 |无效的参数
6097 |请求过于频繁
6098|用户已经被列入交易黑名单不能进行交易
6115|提交提币申请失败！
6125|无效的货币类型！
6133|提现地址不合法！
6150|单次提现超出或低于限额
6151|当天提现超出限额
6152|提现超出用户限额
6153|资金不足
6154|此记录不需要重新发起提币    
2012|委托信息不存在或系统正在处理！
6400|当前市场未到开盘时间
6402|您的下单量超出最大限制
6403|您的下单单价超出限制
6603|当前币种禁止充值
6894|时间过长，API签名已失效
6895|校验API权限失败，接口不属于授权API
6896|校验API权限失败,Userid和Apiid不匹配
6897|校验API权限失败，请确认是否开启Api权限
6898|多次校验API权限失败，请确认是否开启Api权限
6899|该市场现暂不对外开放api交易
6900|交易所现暂不对外开放api交易
6991|价格精度错误，价格小数位最多%s位
6992|订单数量精度错误，订单数量小数位最多%s位
6999|拒绝访问



# 基础信息

## 公共-获取所有交易对

此接口获取ZBG平台支持的所有交易对。

**HTTP 请求**

+ GET <code>/exchange/api/v1/common/symbols</code>

> curl "https://www.zbg.fun/exchange/api/v1/common/symbols"

**请求参数**

无

**返回字段**

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

字段名称            | 数据类型  |	描述
--------------------|-----------|--------
id       |	string  |	交易对ID
base-currency       |	string  |	交易对中的基础币种
quote-currency      |	string  |	交易对中的报价币种
price-precision     |	integer |	交易对报价的精度（小数点后位数）
amount-precision    |	integer |	交易对基础币种计数精度（小数点后位数）
symbol-partition    |	string  |	交易区，可能值: main：主流区，innovation：创新区
symbol              |	string  |	交易对
state               |	string  | 交易对状态；可能值: [online,offline,suspend] <br/>online - 已上线；<br>offline - 交易对已下线，不可交易；<br/>suspend -- 交易暂停 
min-order-amt       |	decimal  |	交易对最小下单量 
max-order-amt       |	decimal  |	交易对最大下单量 为空时不限制


## 公共-获取所有币种

此接口获取ZBG平台支持的所有币种信息列表。

**HTTP 请求**

+ GET <code>/exchange/api/v1/common/currencys</code>

**请求参数**

无


**返回字段**

> Response:

```json
"datas":[
        {
            "daily-draw-limit":200000,
            "name":"trxusdt",
            "draw-fee":"0",
            "id":"516",
            "draw-flag":true,
            "once-draw-limit":20000,
            "min-draw-limit":"6.00000000"
        },
        {
            "daily-draw-limit":100000,
            "name":"ksm",
            "draw-fee":"0.1",
            "id":"511",
            "draw-flag":true,
            "once-draw-limit":10000,
            "min-draw-limit":"0.11000000"
        }
]
```

字段名称        |  数据类型  |	描述
----------------|------------|--------
id            |   string   |	币种ID
name            |   string   |	币种名称
draw-flag       |	boolean  |	是否可提币
draw-fee        |	string   |	提币手续费
once-draw-limit |	integer  |	一次可提币最大限制
daily-draw-limit|	integer  |	每日提币最大限制
min-draw-limit|	integer  |	一次提币最小限制


## 公共-获取当前系统时间

此接口返回当前的系统时间，时间是调整为北京时间的时间戳，单位毫秒。

**HTTP 请求**

+ GET <code>/exchange/api/v1/common/timestamp</code>

**请求参数**

无

**返回字段**

> Response:

```json
"datas":1568812709229
```

毫秒级时间戳


## 公共-获取当前辅助价格

查询指定币种对usd，qc，btc的折算价格。

**HTTP 请求**

+ GET <code>/exchange/api/v1/common/assist-price</code>

> curl https://www.zbg.fun/exchange/api/v1/common/assist-price?currencys=usdt,eth

**请求参数**

参数            |  数据类型  |是否必须|	描述
----------------|------------|--------|--------
currencys       |   string   |  false  |币种，多个以 “,” 隔开 如：zt,usdt,btc



**返回字段**

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

返回 cny, usd, btc 为key的三个map， map内容值：<currency, price>


## 公共-历史成交记录

返回最新80条历史成交记录

**HTTP 请求**

+ GET <code>/exchange/api/v1/common/trade-history/{symbol}</code>


> curl https://www.zbg.fun/exchange/api/v1/common/trade-history/zt_usdt

返回从[trade-id]往后的最多1000历史成交记录：

+ GET <code>/exchange/api/v1/common/trade-history/{symbol}/{trade-id}</code>

> curl https://www.zbg.fun/exchange/api/v1/common/trade-history/zt_usdt/T6609287828167733248

**请求参数**

参数            |  数据类型  |是否必须|	描述
----------------|------------|--------|--------
symbol       |   string   |  true  |	市场名称
trade-id       |   string   |  false  |	交易ID

**返回字段**

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
        },
        ......
]
```

返回的数据是个list列表，列表项字段：

字段名称    | 数据类型  |	描述
------------|-----------|-----------
trade-id |	string  |	交易 ID
price      |	decimal  |	委托价
side     |	string  |	买卖类型， buy/sell
amount    |	decimal  |	数量
total       |	decimal  |	总额
created-at  |	long  |	发起时间戳，毫秒
date  |	string  |	发起时间 yyyy-MM-dd HH:mm:ss


# 行情数据

## 公共-K 线数据（蜡烛图）

此接口返回历史K线数据。

**HTTP 请求**

+ GET <code>https://kline.zbg.fun/api/data/v1/klines</code>

**请求参数**


参数            |  数据类型  |是否必须|	描述
----------------|------------|--------|--------
marketName      |   string   |  true  |	交易对
type            |	string  |	true  | K线类型,支持1M，5M，15M，30M，1H，1D，1W<br/> 七种类型，分别代表1-30分钟，1小时，1日，1周 
dataSize        |	integer   |	true  |	返回 K 线数据条数,[1,100]


**返回字段**


> Response:

```json
"datas":[
    [
        "K",            // 数据类型，K 代表 K 线
        "90",           // 市场ID，可忽略
        "etc_usdt",     // 交易对
        "1532181600",   // 时间戳，秒级别
        "826.7",        // 开盘价
        "827.68",       // 最高价
        "826.68",       // 最低价
        "826.07",       // 收盘价
        "492261",       // 成交量
        "0.04",         // 涨跌幅度
        "6.8",          // 美元汇率
        "1H",           // K 线周期
        "false"         // 是否记过转换
        "407073731.01"  // 成交额(单位是买方币种)]
    ],
    ..........
]
```

返回一个双层的list，里层一个list即为一条数据：
    
    [
        数据类型,
        市场ID,
        交易对,
        时间戳，秒级别,
        开盘价,
        最高价,
        最低价,
        收盘价,
        成交量,
        涨跌幅度,
        美元汇率,
        K 线周期,
        是否记过转换,
        成交额(单位是买方币种)]
    ]



## 公共-聚合行情（Ticker）

此接口获取ticker信息同时提供最近24小时的交易聚合信息。

<aside class="notice">
这个数据服务端更新速度为10秒一次，不必太过频繁获取
</aside>

**HTTP 请求**

+ GET <code>https://kline.zbg.fun/api/data/v1/ticker</code>

**请求参数**


参数            |  数据类型  |是否必须|	描述
----------------|------------|--------|--------
marketName      |   string   |  true  |	交易对

**返回字段**

> Response:

```json
"datas":[
    "386",              // 市场ID，可忽略
    "0.291",            // 最新成交价
    "0.2972",           // 最高价
    "0.2848",           // 最低价
    "295240028.896",    // 24小时成交量
    "-0.75",            // 24小时涨跌幅度
    "[                  
        [
            1,          // 序号
            0.2932      // 收盘价
        ], 
        [2, 0.2928], [3, 0.2923], [4, 0.292], [5, 0.2897], [6, 0.291]
    ]",
    "0.2899",           // 买一价，
    "0.2912",           // 卖一价,
    "86114684.5935"     // 24小时成交额(单位是买方币种),
]
```

返回 string list，数据说明

[ 
    市场ID, 
    最新成交价, 
    最高价，
    最低价, 
    24小时成交量，
    24小时涨跌幅, 
    最近6H收盘价列表,
    买一价，
    卖一价,
    24小时成交额(单位是买方币种)
]

最近6H收盘价列表按时间顺序排序，数据说明：

[[序号, 收盘价], [序号, 收盘价], [序号, 收盘价]]



## 公共-最新聚合行情（Tickers）

获得所有交易对的 tickers，数据取值时间区间为24小时滚动。

<aside class="notice">
这个数据服务端更新速度为10秒一次，不必太过频繁获取
</aside>

**HTTP 请求**

+ GET <code>https://kline.zbg.fun/api/data/v1/tickers</code>

**请求参数**

isUseMarketName为true和false时返回的数据格式是不一样的

参数            |  数据类型  |是否必须|	描述
----------------|------------|--------|--------
isUseMarketName  |   boolean   |  false  |传true，会返回交易对

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

> curl https://kline.zbg.fun/api/data/v1/tickers?isUseMarketName=true

> Response:

```json
"datas":{
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

**返回字段**

返回 list，但是isUseMarketName为true和false时list里面的元素的数据格式是不一样的。当isUseMarketName=false时
list里面元素是一个 string list；为true时是一个key为交易对名称，value为string list的map，

返回 string list，数据说明

    [ 
        市场ID, 
        最新成交价, 
        最高价，
        最低价, 
        24小时成交量,
        24小时涨跌幅, 
        最近6H收盘价列表,
        买一价，
        卖一价,
        24小时成交额(单位是买方币种)
    ]

最近6H收盘价列表按时间顺序排序，数据说明：

[[序号, 收盘价], [序号, 收盘价], [序号, 收盘价]]


## 公共-市场深度数据

此接口返回指定交易对的当前市场深度数据。

本接口最多返回买卖200档的盘口数据

**HTTP 请求**

+ GET <code>https://kline.zbg.fun/api/data/v1/entrusts</code>

**请求参数**

参数            |  数据类型  |是否必须|	描述
----------------|------------|--------|--------
marketName      |   string   |  true  |	交易对
dataSize        |	integer   |	true  |档位数，表示买卖各5档，最大为200


**返回字段**

> Response:

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

字段名称            | 数据类型  |	描述
--------------------|-----------|--------
asks             |	list  |	卖盘列表，按卖价倒序
bids          |	list  |	买盘列表，按买价倒序
timestamp               |	long  |	请求时的时间戳，秒级别


## 公共-最近历史成交记录

此接口返回指定交易对最新的交易记录。


**HTTP 请求**

+ GET <code>https://kline.zbg.fun/api/data/v1/trades</code>

**请求参数**

参数            |  数据类型  |是否必须|	描述
----------------|------------|--------|--------
marketName      |   string   |  true  |	交易对
dataSize        |	integer   |	false  |数据量，默认80，最多为1000


**返回字段**

> Response:

```json
"datas": [
    [
        "T",            // 数据类型       
        "90",           // 市场ID
        "1532183063",   // 时间戳
        "ETC_USDT",     // 市场名称
        "bid",          // 买卖类型 (ask: 卖, bid: 买)
        "827.90",       // 成交价格
        "515.50"        // 成交量
    ],
    ......
]
```

返回 string list，数据说明

    [ 
        数据类型, 
        市场ID, 
        时间戳，
        市场名称, 
        买卖类型 (ask: 卖, bid: 买),
        成交价格, 
        成交量
    ]


# 账户相关

    访问账户相关的接口如无特别说明都需要进行签名认证

## 账户信息

查询当前用户信息及其子账号列表

**HTTP 请求**

+ GET <code>/exchange/api/v1/account/accounts</code>

**请求参数**

无

**返回字段**

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

字段名称            | 数据类型  |	描述
--------------------|-----------|--------
user-id             |	string  |	用户ID
login-name          |	string  |	登录名
phone               |	string  |	手机号
email               |	string  |	邮箱
type                |	string |	账户类型，main，sub，virtual
certification       |	boolean |	是否进行了实名认证
mobile-auth         |	boolean |	是否进行了手机验证
email-auth          |	boolean |	是否进行了邮箱验证
google-auth         |	boolean |	是否进行了谷歌验证
safe-pwd-auth       |	boolean |	是否设置了安全密码
subs                |   list    |   子账号ID列表


## 账户搜索

搜索用户信息

**HTTP 请求**

+ GET <code>/exchange/api/v1/account/search</code>

**请求参数**

参数            |  数据类型  |是否必须|	描述
----------------|------------|--------|--------
account     |   string   |  true  |	查询账号
account-type     |	int   |	true  |	账号，0:手机号，1:邮箱，2:登录名，3:微信号
google-code     |	string   |	true  | 谷歌验证码
country-code     |	string   |	false  | account-type=0时可传

**返回字段**

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

字段名称            | 数据类型  |	描述
--------------------|-----------|--------
user-id                  |	string  |	用户ID
login-name          |	string  |	登录名
phone               |	string  |	手机号
email               |	string  |	邮箱
type                |	string |	账户类型，main，sub，virtual
certification       |	boolean |	是否进行了实名认证
mobile-auth         |	boolean |	是否进行了手机验证
email-auth          |	boolean |	是否进行了邮箱验证
google-auth         |	boolean |	是否进行了谷歌验证
safe-pwd-auth       |	boolean |	是否设置了安全密码


## 账户余额

查询当前用户的资产信息。

**HTTP 请求**

+ GET <code>/exchange/api/v1/account/balance</code>

**请求参数**

无

**返回字段**

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

返回的数据是一个list，字段说明


字段名称        | 数据类型  |	描述
----------------|-----------|--------
user-id         |	string  |	用户ID
currency        |	string  |	币种名称
balance         |	decimal |	余额
available       |	decimal |	可用余额
freeze          |	decimal |	冻结(不可用)


## 查询单个币种的账户余额

查询当前用户指定币种的的资产信息。

**HTTP 请求**

+ GET <code>/exchange/api/v1/account/balance/{currency}</code>

**请求参数**

参数            |  数据类型  |是否必须|	描述
----------------|------------|--------|--------
currency     |	string   |	true  |	币种名称

**返回字段**

> Response:

```json
"datas":{
    "freeze":"4224.4187921662601158",
    "available":"18255.174060294635375085",
    "user-id":"7eU4wsh3XLE",
    "currency":"usdt"
}

```

字段名称        | 数据类型  |	描述
----------------|-----------|--------
user-id         |	string  |	用户ID
currency        |	string  |	币种名称
balance         |	decimal |	余额
available       |	decimal |	可用余额
freeze          |	decimal |	冻结(不可用)


## 资产划转（母子账号之间）

母账户执行母子账户间的资产划转。

**HTTP 请求**

+ POST <code>/exchange/api/v1/account/sub/transfer</code>

**请求参数**

参数            |  数据类型  |是否必须|	描述
----------------|------------|--------|--------
sub-uid      |   string   |  true  |	子账号ID
currency     |	string   |	true  |	币种名称
amount     |	decimal   |	true  |	数量
type     |	string   |	true  | 划转类型：<br/>master-transfer-in:子账号划转给母账户虚拟币<br/> master-transfer-out :母账户划转给子账号虚拟币 

**返回字段**

无



## 子账号余额（汇总）

母账户查询其下所有子账号的各币种汇总余额。

**HTTP 请求**

+ GET <code>/exchange/api/v1/account/sub/aggregate-balance</code>

**请求参数**

无


**返回字段**

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

返回的数据是一个list，字段说明

字段名称            | 数据类型  |	描述
--------------------|-----------|--------
currency            |	string  |	币种名称
balance             |	decimal |子账号下该币种所有余额（可用余额和冻结余额的总和)



## 子账号余额

母账户查询其下指定子账号的各币种余额。

**HTTP 请求**

+ GET <code>/exchange/api/v1/account/sub/balance/{sub-uid}</code>

**请求参数**

参数            |  数据类型  |是否必须|	描述
----------------|------------|--------|--------
sub-uid      |   string   |  true  |	子账号ID

**返回字段**

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

返回的数据是一个list，字段说明

字段名称            | 数据类型  |	描述
--------------------|-----------|--------
currency          |	string  |	币种名称
balance         |	decimal |	余额
available       |	decimal |	可用余额
freeze          |	decimal |	冻结(不可用)



# 充值与提现

## 充币地址查询

生成并获取币种的在本平台的充币地址。

**HTTP 请求**

+ GET <code>/exchange/api/v1/account/deposit/address</code>

**请求参数**

参数            |  数据类型  |是否必须|	描述
----------------|------------|--------|--------
currency      |   string   |  true  |	币种，btc,ltc...(ZBG支持的[币种][currencys])


**返回字段**

> Response:

```json
"datas":
{
    "address": "19cdJwd3j6ArHNhiYoWpN8cJq9ash7WDDC",
    "currency":"zt"
}
```

字段名称    | 数据类型  |是否必须|	描述
------------|-----------|--------|-----------
currency    |	string  | true   |	币种名称
address     |	string  | true   |	充币地址
memo         |	string  | false   |	充币地址标签

    注意：关于标签备注(Memo)
    
    1.如果您的地址是 个人钱包地址，可任意填写。
    
    2.如果您的地址是 交易所充值地址，则提币时同时需要一个提币地址和标签(又名Tag/标签/Memo/备注等)，标签保证您的提币地址唯一，与充币地址一一对应。
    
    3.请您务必遵守正确的提币步骤，在提币时输入完整信息，否则将面临 丢失币。的风险，ZBG平台将不承担找回服务。
    
    4.若有疑问请联系ZBG客服。



## 充币记录查询

查询用户指定币种的在本平台的充币记录。

**HTTP 请求**

+ GET <code>/exchange/api/v1/account/deposit/history</code>

**请求参数**

参数      |  数据类型  |是否必须|	描述
----------|------------|--------|--------
currency  |   string   |  false |	币种，btc,ltc...(ZBG支持的[币种][currencys])
direct    |   string   |  false |	返回记录排序方向， “prev” （升序）or “next” （降序）默认为“next” （降序）
page      |   int      |  false |	页码，默认 1
size      |   int      |  false |	查询记录大小，默认100，取值 [1,500]


**返回字段**

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

返回的数据是一个page对象，字段说明


字段名称    | 数据类型  |	描述
------------|-----------|-----------
page        |	int     |	页码
size        |	int     |	查询记录大小	
rows        |	int     |	查询的总记录数
list        |	array   |	数据列表，字段见下

+ 充币记录字段：

字段名称    | 数据类型  |	描述
------------|-----------|-----------
deposit-id |	string  |	充币 ID
currency    |	string  |	币种	
amount      |	decimal  |	充币数
address     |	string  |	地址
tx-hash     |	string  |	交易哈希
confirm-times     |	int  |	确认次数
state       |	string  |	状态：confirming：确认中，completed：完成
type        |	string  |	充币类型，参见如下
created-at  |	long  |	发起时间 
confirmed-at|	long  |	入账时间6次区块确认时间点 

+ 充币类型定义：

类型       | 描述
-----------|----------
blockchain | 区块链转入
system     | 系统充值
recharge   | 法币充值
transfer   | 商户之间互相转账


## 提币地址查询

获取币种的在本平台填写的充币地址。

**HTTP 请求**

+ GET <code>/exchange/api/v1/account/withdraw/address</code>

**请求参数**

参数          |  数据类型  |是否必须|	描述
--------------|------------|--------|--------
currency      |   string   |  true  |	币种，btc,ltc...(ZBG支持的[币种][currencys])


**返回字段**

> Response:

```json
{
  "datas":
    {
        "address": "19cdJwd3j6ArHNhiYoWpN8cJq9ash7WDDC",
        "currency":"zt"
    }
}
```

字段名称    | 数据类型  |是否必须|	描述
------------|-----------|--------|-----------
currency    |	string  | true   |	币种名称
address     |	string  | true   |	提币地址
remark      |	string  | false   |	备注



## 提币

**HTTP 请求**

+ POST <code>/exchange/api/v1/account/withdraw/create</code>

**请求参数**

参数        |  数据类型  |是否必须|	描述
------------|------------|--------|--------
currency    |   string   |  true  |	币种，btc,ltc...(ZBG支持的[币种][currencys])
address     |   string   |  true  |	提币地址，需要提前在 <a href="/u/payout" id="payout">官网</a> 上设置
amount      |   decimal  |  true  |	提币数量


**返回字段**

> Response:

```json
"datas":"W6574581089036550144"
```

返回的数据是一个string类型的提现ID


## 取消提币


**HTTP 请求**

+ POST <code>/exchange/api/v1/account/withdraw/cancel/{withdraw-id}</code>

**请求参数**

参数            |  数据类型  |是否必须|	描述
----------------|------------|--------|--------
withdraw-id     |   string   |  true  |	提现 ID，填在 path 中	



**返回字段**

> Response:

```json
"datas":"W6574581089036550144"
```

字段名称    | 数据类型  |	描述
------------|-----------|-----------
withdraw-id |	string  |	提现 ID


## 提币记录查询

查询用户在本平台的提币记录。

**HTTP 请求**

+ GET <code>/exchange/api/v1/account/withdraw/history</code>

**请求参数**

参数      |  数据类型  |是否必须|	描述
----------|------------|--------|--------
currency  |   string   |  false |	币种，btc,ltc...(ZBG支持的[币种][currencys])
direct    |   string   |  false |	返回记录排序方向， “prev” （升序）or “next” （降序）默认为“next” （降序）
page      |   int      |  false |	页码，默认 1
size      |   int      |  false |	查询记录大小，默认100，取值 [1,500]


**返回字段**

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

返回的数据是一个page对象，字段说明


字段名称    | 数据类型  |	描述
------------|-----------|-----------
page        |	int     |	页码
size        |	int     |	查询记录大小	
rows        |	int     |	查询的总记录数
list        |	array   |	数据列表，字段见下

+ 充币记录字段：

字段名称    | 数据类型  |	描述
------------|-----------|-----------
withdraw-id |	string  |	提币 ID
currency    |	string  |	币种	
amount      |	decimal  |	提币数
address     |	string  |	地址
tx-hash     |	string  |	交易哈希
state       |	string  |	状态参见如下
fee        |	decimal  |	手续费
created-at  |	long  |	发起时间
audited-at  |	long  |	审核时间

+ 提币状态定义：

状态       | 描述
-----------|----------
reexamine | 审核中
canceled     | 已撤销
pass   | 审批通过
reject   | 审批拒绝
transferred   | 已打币
confirmed   | 已完成


# 现货交易
    
访问现货交易相关的接口如无特别说明都需要进行签名认证

## 下单

发送一个新订单到 ZBG 进行撮合。

 **重要提醒信息:**
 为防止用户误操作，买单下单价格不能高于最近成交价三倍，卖单下单价格不能低于最近成交价三分之一

**HTTP 请求**

+ POST <code>/exchange/api/v1/order/create</code>

**请求参数**

参数      |  数据类型  |是否必须|	描述
----------|------------|--------|--------
symbol   |   string   |  true |	交易对，例如：btc_usdt,eth_usdt
side    |   string   |  true |	订单类型，包括 buy，sell
amount      |   decimal      |  true |	订单交易量
price      |   decimal      |  true |	order的交易价格


**返回字段**

> Response:

```json
"datas": "E6580725150600146944"
```

返回的数据是 string 类型的订单号

- 返回该订单号并不代表下单成功，用户可以通过 [查询订单详情](#92d59b6aad) 查询订单状态

- 下单的数量和价格的精度能超过币种预设的值，系否则统将拒绝接受此订单。


## 撤销订单

此接口发送一个撤销订单的请求。

此接口只提交取消请求，实际取消结果需要通过订单状态，撮合状态等接口来确认。

**HTTP 请求**

+ POST <code>/exchange/api/v1/order/cancel</code>

**请求参数**

参数      |  数据类型  |是否必须|	描述
----------|------------|--------|--------
symbol   |   string   |  true |	交易对，例如：btc_usdt,eth_usdt
order-id    |   string   |  true |	订单号


**返回字段**

无


## 批量撤销订单

此接口发送批量撤销订单的请求。

此接口只提交取消请求，实际取消结果需要通过订单状态，撮合状态等接口来确认。

**HTTP 请求**

+ POST <code>/exchange/api/v1/order/batch-cancel</code>

**请求参数**

参数      |  数据类型  |是否必须|	描述
----------|------------|--------|--------
symbol   |   string   |  true |	交易对，例如：btc_usdt,eth_usdt
side    |   string   |  false | 主动交易方向，“buy”或“sell”， 
order-ids    |   list   |  false |	订单号
price-from    |   decimal   |  false |	委托价格区间取消：取消单价>=price-from的委托
price-to    |   decimal   |  false |	委托价格区间取消：取消单价<=price-to



**返回字段**

> Response:

```json
"datas": 2
```

返回取消的订单数


## 查询当前未成交完成的订单

查询已提交但是仍未完全成交或未被撤销的订单。

**HTTP 请求**

+ GET <code>/exchange/api/v1/order/open-orders</code>

**请求参数**

参数      |  数据类型  |是否必须|	描述
----------|------------|--------|--------
symbol   |   string   |  true |	交易对，例如：btc_usdt,eth_usdt
page    |   int   |  false |	页码，默认 1
size    |   int   |  false |	返回订单的数量， 默认20 最大值 100



**返回字段**

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

返回的数据是个page对象

字段名称    | 数据类型  |	描述
------------|-----------|-----------
page        |	int     |	页码
size        |	int     |	查询记录大小	
rows        |	int     |	查询的总记录数
list        |	array   |	数据列表，字段见下

+ 充币记录字段：

字段名称    | 数据类型  |	描述
------------|-----------|-----------
order-id |	string  |	订单 ID
symbol    |	string  |	交易对	
price      |	decimal  |	委托价
side     |	string  |	订单类型， buy/sell
amount    |	string  |	订单委托数量
available-amount       |	string  |	订单中可成交的数量
filled-amount    |	string  |	订单中已成交部分的数量
filled-cash-amount       |	string  |	订单中已成交部分的总价格
state        |	string  | 订单状态，submitted：订单申请（未入库）, <br/>partial-filled：部分交易, <br/>cancelling：正在撤单, <br/>created：已创建（入库） 
created-at  |	long  |	发起时间



## 查询历史订单

此接口基于搜索条件查询历史订单。

**HTTP 请求**

+ GET <code>/exchange/api/v1/order/orders</code>

**请求参数**

已完成或撤销的历史订单会移除到备份表中，这是可通过设置 history=true 来查询
 
参数      |  数据类型  |是否必须|	描述
----------|------------|--------|--------
symbol   |   string   |  true |	交易对，例如：btc_usdt,eth_usdt
side   |   string   |  false |	订单类型，buy/sell
state   |   string   |  false | 状态，有效状态：partial-filled: 部分成交, <br/> partial-canceled: 部分成交撤销,<br/> filled: 完全成交, <br/> canceled: 已撤销，<br/> created: 已创建（入库） 
start-date   |   string   |  false | 查询开始日期, 日期格式yyyy-mm-dd 
end-date   |   string   |  false | 查询结束日期, 日期格式yyyy-mm-dd 
history  |   boolean   |  false | 是否查询历史记录，默认flase。 
page    |   int   |  false |	页码，默认 1
size    |   int   |  false |	返回订单的数量， 默认20 最大值 100


**返回字段**

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

返回的数据是个page对象

字段名称    | 数据类型  |	描述
------------|-----------|-----------
page        |	int     |	页码
size        |	int     |	查询记录大小	
rows        |	int     |	查询的总记录数
list        |	array   |	数据列表，字段见下

+ 充币记录字段：

字段名称    | 数据类型  |	描述
------------|-----------|-----------
order-id |	string  |	订单 ID
symbol    |	string  |	交易对	
price      |	decimal  |	委托价
side     |	string  |	订单类型， buy/sell
amount    |	string  |	订单委托数量
available-amount       |	string  |	订单中可成交的数量
filled-amount    |	string  |	订单中已成交部分的数量
filled-cash-amount       |	string  |	订单中已成交部分的总价格
state        |	string  | 订单状态，包括：partial-filled: 部分成交,<br/> partial-canceled: 部分成交撤销,<br/> filled: 完全成交,<br/> canceled: 已撤销，<br/> created: 已创建（入库） 
created-at  |	long  |	发起时间



## 查询订单详情

此接口返回指定订单的最新状态和详情。

**HTTP 请求**

+ GET <code>/exchange/api/v1/order/detail</code>

**请求参数**

参数      |  数据类型  |是否必须|	描述
----------|------------|--------|--------
symbol   |   string   |  true |	交易对，例如：btc_usdt,eth_usdt
order-id   |   string   |  true |	订单号


**返回字段**

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

字段名称    | 数据类型  |	描述
------------|-----------|-----------
order-id |	string  |	订单 ID
symbol    |	string  |	交易对	
price      |	decimal  |	委托价
side     |	string  |	订单类型， buy/sell
amount    |	string  |	订单委托数量
available-amount       |	string  |	订单中可成交的数量
filled-amount    |	string  |	订单中已成交部分的数量
filled-cash-amount       |	string  |	订单中已成交部分的总价格
state        |	string  | 订单状态，包括：partial-filled: 部分成交, <br/> partial-canceled: 部分成交撤销, <br/> filled: 完全成交, <br/> canceled: 已撤销，<br/> created: 已创建（入库） 
created-at  |	long  |	发起时间


## 查询成交明细

此接口返回指定订单的成交明细。

**HTTP 请求**

+ GET <code>/exchange/api/v1/order/trades</code>

**请求参数**

参数      |  数据类型  |是否必须|	描述
----------|------------|--------|--------
symbol   |   string   |  true |	交易对，例如：btc_usdt,eth_usdt
order-id   |   string   |  true |	订单号


**返回字段**

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
返回一个list对象：

字段名称    | 数据类型  |	描述
------------|-----------|-----------
trade-id |	string  |	订单成交记录ID
order-id |	string  |	订单 ID
match-id |	string  |	撮合订单 ID
symbol    |	string  |	交易对	
price      |	decimal  |	委托价
side     |	string  |	主动单类型， buy/sell
filled-amount    |	string  |	成交数量
filled-fees       |	string  |	成交手续费
role        |	string  |	成交角色	maker,taker
created-at  |	long  |	发起时间


# Websocket行情数据

## 简介

**接入URL**

<code>wss://kline.zbg.fun/websocket</code>


**心跳消息**

当用户的Websocket客户端连接到Websocket服务器后，如果长时间没有数据推可能会导致连接断开。客户端可以定时定时发送ping消息来保证连接不断。

    {"action":"PING"}

当用户的Websocket客户端接收到此心跳消息后，应返回pong消息：

    {"dataType":null,"action":"PING","msg":"action not support","code":"5021"}


**订阅主题**

成功建立与Websocket服务器的连接后，Websocket客户端发送如下请求以订阅特定主题：

    {"action":"ADD", "dataType":"message topic", "dataSize":1}

- action: 请求的动作类型，ADD:增加数据订阅，DEL:移除数据订阅。 
- dataType: 请求的数据类型, 下面各个章节会详细说明。
- dataSize: 请求的数据数量，决定首次全量数据的数量，不传或者为 0 则返回一条数据。

用例：

    {"action":"ADD", "dataType":"336_ENTRUST_ADD_ZT_USDT", "dataSize":1}

成功订阅后，如果是第一次订阅则Websocket客户端将会收到全量信息：


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


之后, 一旦所订阅的主题有更新，Websocket客户端将收到服务器推送的更新消息（push）：


    ["E","336","1569311848","ZT_USDT","BID","0.0399","7897.5386"]


**取消订阅**

取消订阅的格式如下：

{"action":"DEL", "dataType":"message topic"}

用例：

{"action":"DEL", "dataType":"336_ENTRUST_ADD_ZT_USDT"}



## K线数据

**主题订阅**

一旦K线数据产生，Websocket服务器将通过此订阅主题接口推送至客户端：

<code>symbol-id_KLINE_period_symbol</code>

```json
{
    "action":"ADD", 
    "dataType":"336_KLINE_1M_ZT_USDT",
    "dataSize":1000
} 
```

**参数**

参数      |  数据类型  |是否必须|	描述
----------|------------|--------|--------
symbol-id   |   string   |  true |	交易对ID，参考[获取所有交易对][symbols]接口中返回值ID
period   |   string   |  true |	K线周期，支持：1M, 5M, 15M, 30M, 1H, 1D
symbol   |   string   |  true |	交易对，例如：btc_usdt,eth_usdt

**返回字段**


> Response:

> 请求返回数据：

```json
[
    ["K","336","zt_usdt","1569314760","0.0405","0.0405","0.0404","0.0404","42888.96","-0.2469","7.12","1M","false","1735.19"],
    ["K","336","zt_usdt","1569314700","0.0404","0.0405","0.0404","0.0404","63246.27","0","7.12","1M","false","2557"],
]
```

> 后续增量数据：

```json
["K","336","zt_usdt","1569314940","0.0405","0.0405","0.0404","0.0405","50044.14","0","7.12","1M","false","2022.62"]
```

数据返回的是一个list，list各数据定义：

[K, 交易对ID, 交易对, 时间戳, 开盘价, 最高价, 最低价, 收盘价, 成交量, 涨跌幅度, 美元汇率, K线周期, 是否经过转换, 交易额]

如果是首次，则会返回全量数据。

## 市场深度

**主题订阅**

当市场深度发生变化时，此主题发送最新市场深度更新数据：

<code>symbol-id_ENTRUST_ADD_symbol</code>

```json
{
    "action":"ADD", 
    "dataType":"336_ENTRUST_ADD_ZT_USDT"
}
```

**参数**

参数      |  数据类型  |是否必须|	描述
----------|------------|--------|--------
symbol-id   |   string   |  true |	交易对ID，参考[获取所有交易对][symbols]接口中返回值ID
symbol   |   string   |  true |	交易对，例如：btc_usdt,eth_usdt

**返回字段**

> Response:

> 首次请求返回数据：

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
         ]
    },{
        "bids":[
            ["0.0404","45.1537"],
            ["0.0403","338.0193"],
            ["0.0401","3831.9868"],
            ["0.04","2086.9575"],
        ]
    }
]]
```

数据返回的是一个list，list各数据定义：

全量数据字段说明：

[AE, 市场ID, 币种信息, 时间戳, asks(卖盘):[[价格, 量]], bids(买盘):[[价格, 量]]]


> 后续增量数据：

```json
 ["E","336","1569311848","ZT_USDT","BID","0.0399","7897.5386"]
```

增量数据字段说明：

[E, 市场ID, 时间戳, 币种信息, 买卖类型(ASK卖/BID买), 价格, 量]



## 交易记录

**主题订阅**

此主题提供市场最新成交明细：

<code>symbol-id_TRADE_symbol</code>

**参数**

```json
{
    "action":"ADD", 
    "dataType":"336_TRADE_ZT_USDT",
    "dataSize":2
}
```

dataType中的替换参数：

参数      |  数据类型  |是否必须|	描述
----------|------------|--------|--------
symbol-id   |   string   |  true |	交易对ID，参考[获取所有交易对][symbols]接口中返回值ID
symbol   |   string   |  true |	交易对，例如：btc_usdt,eth_usdt

dataSize 最多50条

**返回字段**

> Response:

> 首次请求返回数据：

```json
[
    ["T","336","1569316972","ZT_USDT","bid","0.0404","11895.3578"],
    ["T","336","1569316959","ZT_USDT","ask","0.0405","22546.8484"]
]
```

> 后续增量数据：

```json
 ["T","336","1569316988","ZT_USDT","bid","0.0405","25038.6289"]
```

数据返回的是一个list，list各数据定义：

数据字段说明: 

    [T, 市场ID, 交易对, 时间戳, 买卖类型(ask卖/bid买), 价格, 量]


## 市场24H行情数据

**主题订阅**

主题提供24小时内最新市场概要：

<code>*symbol-id*_TRADE_STATISTIC_24H</code>

该类型数据每5秒钟刷新一次

**参数**

> 请求全部市场行情入参

```json
{
    "action":"ADD", 
    "dataType":"ALL_TRADE_STATISTIC_24H"
}
```

> 请求单个市场行情入参

```json
{
    "action":"ADD", 
    "dataType":"5140_TRADE_STATISTIC_24H"
}
```

参数      |  数据类型  |是否必须|	描述
----------|------------|--------|--------
symbol-id   |   string   |  true |	ALL 或交易对ID，参考[获取所有交易对][symbols]接口中返回值ID，

symbol-id = ALL 时，返回所有交易对的24小时行情

**返回字段**

> Response:

> 全部市场返回数据：

```json
{"trade_statistic":[
    ["5140","0.0199","0.0199","0.0111","27597.0704","7.57","[[1, 0.0199]]","0.012","0.0457","504.7737"],
    ["5020","203823.8","219584","193014.1","21.8465","-4.83","[[1, 204168.8], [2, 207395.2], [3, 205103.6], [4, 206200.9], [5, 204334.9], [6, 203823.8]]","201002.4","206644.9","4524612.3764"],
    ["5021","28691.3","30639.6","27704.9","29.8016","-4.69","[[1, 28519.8], [2, 29109.5], [3, 28280], [4, 28667.7], [5, 28618], [6, 28691.3]]","28334.3","29047.8","864655.9334"],
]}
```

> 单个市场请求返回数据：

```json
{"trade_statistic":[
    ["5140","0.0199","0.0199","0.0111","27597.0704","7.57","[[1, 0.0199]]","0.012","0.0457","504.7737"]
]}
```

数据字段说明: 

    {
        "trade_statistic":[
            [ 市场ID, 最新成交价, 最高价，最低价, 24小时成交量，24小时涨跌幅, 最近6H收盘价列表,买一价，卖一价,24小时成交额(单位是买方币种)],
            ....
        ]
    }

最近6H收盘价列表数据说明：[[序号, 收盘价], [序号, 收盘价], [序号, 收盘价]]



## 订单更新

**主题订阅**

主题提供用户订单最新情况

<code>symbol-id_RECORD_ADD_user-id_symbol</code>

**参数**

```json
{
    "dataType":"336_RECORD_ADD_7eOUtLBFXTU_ZT_USDT",
    "dataSize":50,
    "action":"ADD"
}
```
dataType中的参数：

参数      |  数据类型  |是否必须|	描述
----------|------------|--------|--------
symbol-id   |   string   |  true |	ALL 或交易对ID，参考[获取所有交易对][symbols]接口中返回值ID，
user-id|   string   |  true |	用户ID
symbol   |   string   |  true |	交易对，例如：btc_usdt,eth_usdt

dataSize 最多100条

**返回字段**

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
    ]
]]
```

> 增量数据：

```json
["R","336","7eOUtLBFXTU","1569328042","E6582238884078301184",1,0,"0.039","10","0","0","","1569328042"]
```

全量数据字段说明：[AR, 市场ID, 用户ID, 时间戳, [委托单号, 买卖类型, 状态, 价格, 委托量, 完成量, 完成金额, 均价, 创建时间戳]]

增量数据字段说明：[R, 市场ID, 用户ID, 时间戳, 委托单号, 买卖类型, 状态, 价格, 委托量, 完成量, 完成金额, 均价, 创建时间戳]

买卖类型：1:买：0:卖

状态 :  0起始 1取消 2交易成功 3交易一部分


[currencys]:#63273c70c8
[symbols]:#3f90dca7eb

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
