---
title: ZBG Futures API Documentation

language_tabs: # must be one of https://git.io/vQNgJ
  - json

toc_footers:
  - <a href="https://www.zbg.com/" class="createApi">ZBG Exchange</a>

includes:

search: true
---

# 更新日志


## 2020-04-20 

新增和修改合约下单[合约](#c0b35a1aa9)等接口:

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
* 新增接口[[/exchange/api/v1/future/assets/transfer](#0d83610961)]，现货-合约资金划转
* 新增接口[[/exchange/api/v1/future/assets/transfer-history](#dc5b365f5c)]，现货-合约资金划转记录
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
* 移除接口[/exchange/api/v1/common/future/ticker]
* 移除接口[/exchange/api/v1/common/future/depth]
* 移除接口[/exchange/api/v1/common/future/trades]


## 2020-03-31

增加[合约](#c0b35a1aa9) 



# 简介

## API简介

欢迎使用 ZBG 合约 API！您可以通过使用此API获取合约市场行情数据，进行交易，并且管理你的账户。

文档右侧是针对请求参数以及响应结果的示例。

<aside class="notice">
如果在使用过程中有任何问题，请联系我们技术讨论QQ群：829230107，我们将会为您做出最权威的解答.
</aside>


# 接入说明

## 接入 URLs

REST API

<code>https://www.zbg.fun</code>

域名有时会存在被墙或延迟高等情况，可暂时使用备用域名:

- https://www.zbgpro.com
- https://www.zbgpro.net
- https://www.zbg.kim


## 接口类型

本章节主要为接口类型的细节分以下两个方面：

- 公共接口
- 私有接口

**公共接口**

公共接口可用于获取配置信息和行情数据。公共请求无需认证即可调用。

**私有接口**

私有接口可用于订单管理和账户管理。每个私有请求必须使用规范的验证形式进行签名。

私有接口需要使用您的API key进行验证。您可以在<a href="https://www.zbg.fun/login?path=https://www.zbg.fun/u/api" class="createApi">这里</a>生成API key。



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

## 公共-获取合约列表

此接口获取ZBG平台支持的所有合约。

**HTTP 请求**

+ GET <code>/exchange/api/v1/future/common/contracts</code>

**请求参数**

无

**返回字段**

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



## 公共-获取合约可用币种

此接口获取ZBG平台支持的合约币种列表。

**HTTP 请求**

+ GET <code>/exchange/api/v1/future/common/currencies</code>

**请求参数**

无

**返回字段**

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

字段名称        |  数据类型  |	描述
----------------|------------|--------
currencyId            |   string   |	合约方币种id
symbol            |   string   |	币种名称
displayPrecision       |	int  |	显示位数
enabled        |	int   |	是否可充提，1:可用，0:不可用


# 合约市场行情

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

lines数据结构：List<List>
如：

    [
        [${时间}, ${开市价格}, ${最高价格}, ${最低价格}, ${闭市价格}, ${成交量}]
    ]





## 公共-单个合约行情

此接口获取单个合约的行情快照。


**HTTP 请求**

+ GET <code>/exchange/api/v1/future/market/ticker</code>

**请求参数**


参数            |  数据类型  |是否必须|	描述
----------------|------------|--------|--------
symbol      |   string   |  true  |	交易对


**返回字段**

> Response:

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


<br/>

## 公共-所有合约行情

获得所有合约的行情快照

**HTTP 请求**

+ GET <code>/exchange/api/v1/future/market/tickers</code>

**请求参数**


**返回字段**

> Response:

```
"datas":[
    {}
]
```

返回一个list，子项具体各字段含义，请参考[单个合约行情](#964054eba1)接口




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
asks             |	list  |	卖盘列表，按卖价顺序
bids          |	list  |	买盘列表，按买价倒序
timestamp               |	long  |	请求时的时间戳，毫秒级别




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

> Response:

```json
"datas": [
        [1586504611360280, "6921.5", "12", -1],
        [1586504615863710, "6920.5", "79", 1],
        [1586504620979503, "6920.5", "16", 1],
    ......
]
```

# 合约交易

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
    
已实现订单类型：<br/>
被动委托：<br/>
order_sub_type: 1
    
条件委托：<br/>
order_sub_type: 2（最近价触发条件委托），3（指数触发条件委托），4（标记价触发条件委托）<br/>
stop_price: 触发价格
    
追踪止损：<br/>
order_sub_type: 2（最近价触发条件委托），3（指数触发条件委托），4（标记价触发条件委托）<br/>
minimal_quantity：止损点位<br/>
stop_condition: 2（止损）

**返回字段**

> Response:

```json
"datas":"11585489047547210"
```
  
正常时为order_id



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
    
价值= 持仓量 * 最新价 * ContractUnit
    
开仓价格 = 开仓金额/(持仓量*ContractUnit)
    
标记价格： 行情获取
    
强平价格：

    正向合约多头持仓：开仓价 + 开仓金额 * (初始保证金率-维持保证金率)/持仓量
    
    正向合约空头持仓：开仓价 - 开仓金额 * (初始保证金率-维持保证金率)/持仓量
    
    反向合约多头持仓：开仓价 - 开仓金额 * (初始保证金率-维持保证金率)/持仓量
    
    反向合约空头持仓：开仓价 + 开仓金额 * (初始保证金率-维持保证金率)/持仓量
    
保证金： 初始保证金
    
倍数： 1/初始保证率
    
正向合约：

    多头时未实现盈亏=持仓量 *（最新价-开仓价）* ContractUnit； 
                
    空头时未实现盈亏=持仓量 *（开仓价-最新价）* ContractUnit；

反向合约：

    空头时未实现盈亏=持仓量 *（最新价-开仓价）* ContractUnit；
                   
    多头时未实现盈亏=持仓量 *（开仓价-最新价）* ContractUnit；
    
回报率：盈亏／ 价值
    
减仓队列： 通过强减队列查询
    
操作列： 当posiStatus为2时，不能平仓和市价全平
    
已平仓仓位： 已实现盈亏，取值 closeProfitLoss

**HTTP 请求**

+ GET <code>/exchange/api/v1/future/positions</code>

**请求参数**

无

**返回字段**

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




## 查询合约当前委托

**HTTP 请求**

+ GET <code>/exchange/api/v1/future/orders</code>

**请求参数**

无

**返回字段**

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

备注<br/>
用户选择了查询时间条件时：<br/>
每次用户选择上一页，下一页时，timestamp需有值<br/>

上一页：<br/>
   timestamp= 当前页第一条数据的timestamp<br/>
下一页：<br/>
   timestamp= 当前页最后一条数据的timestamp<br/>
       


**返回字段**

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


orderStatus委托状态：

状态 | 描述
---|---
 0|未申报
 1|正在申报
 2|已申报未成交
 3|部分成交
 4|全部成交
 5|部分撤单
 6|全部撤单
 7|撤单中
 8|失效
 11|缓存高于条件的委托
 12|缓存低于条件的委托

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

无


## 调整保证金

**HTTP 请求**

+ POST <code>/exchange/api/v1/future/adjust-margin</code>

**请求参数**

参数            |  数据类型  |是否必须|	描述
----------------|------------|--------|--------
symbol      |   string   |  是  |	合约名称
margin  | string  | 是  | 保证金

**返回字段**

无



# 合约资产

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

字段名称    | 数据类型  |	描述
----------------|------------|--------
flowId  | number  | 流水ID
userId  | number  | 用户合约ID
applId  | number  | 2
currencyId  | number  | 货币ID
flowType  | number  | 流水类型
num  | number  | 金额
occureTime  | number  | 转账时间
createTime  | number  | 记录创建时间
uuid  | string  | 对应的记录id（转账id、成交id等）
note  | string  | 备注



## 查询合约可用资金

**HTTP 请求**

+ GET <code>/exchange/api/v1/future/assets/available</code>

**请求参数**

无

**返回字段**

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

## 现货-合约资金划转

此接口用户币币现货账户与合约账户之间的资金划转。

**HTTP 请求**

+ POST <code>/exchange/api/v1/future/assets/transfer</code>

**请求参数**

参数            |  数据类型  |是否必须|	描述
----------------|------------|--------|--------
currency      |   string   |  是  |	币种名称
amount  | string  | 是  | 划转数量
side  | int  | 是  | 划转方向，0: 现货账户向合约账户划转 1: 合约账户向现货账户划转

       
**返回字段**

> Response:

```json
"datas":"4b79ca9e72294e3898999af23769f05e"
```

返回划转单号

## 现货-合约资金划转历史查询

接口返回用户币币现货账户与合约账户之间的资金划转记录。

**HTTP 请求**

+ POST <code>/exchange/api/v1/future/assets/transfer-history</code>

**请求参数**

参数            |  数据类型  |是否必须|	描述
----------------|------------|--------|--------
clientId      |   string   |  否  |	划转单号
currency      |   string   |  否  |	币种名称
side  | int  | 否  | 划转方向，0: 现货账户向合约账户划转 1: 合约账户向现货账户划转
pageNum  | int  | 否  | 当前页：默认：1
pageSize  | int  | 否  | 默认10
       
**返回字段**

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

字段名称    | 数据类型  |	描述
----------------|------------|--------
totalRow  | number  | 总记录数
totalPage  | number  | 总页数
pageSize  | number  | 每页返回数
pageNum  | number  | 当前页
list  | object []  | 
├─clientId  | string  | 划转账单
├─userId  | number  | 用户ID
├─currency  | string  | 币种
├─amount  | number  | 划转数量
├─side  | int  | 划转方向，0: 现货账户向合约账户划转 1: 合约账户向现货账户划转
├─state  | string  | 划转状态， confirming：划转中，completed：划转成功，failure：划转失败
├─createdAt  | string  | 记录创建时间
├─confirmTime  | string  | 确认划转时间

<div style="text-align: right;font-size:0"> created by zhangzp at 2019-09-21 </div>
