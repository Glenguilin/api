#  Exchange官方API文档

#### Exchange交易所开发文档([English Docs](https://github.com/wbfex/api/blob/master/api/us_en/api_doc_en.md))
- [介绍](#Introduction)
- [开始使用](#startToUse)
- [API接口加密验证](#a1)
    - [生成API Key](#a2)
    - [发起请求](#a3)
    - [签名](#a4)
    - 选择时间戳
    - [请求交互](#a6)
        - [请求](#a7)
        - [分页](#a8)
    - [标准规范](#a9)
        - [时间戳](#b1)
        - [例子](#b2)
        - [数字](#b3)
        - [限流 - REST API](#b4)
- [业务API参考](#b5)
  - [open-api](#b6) ([Java-Demo](#open-api-java))
    -   [资产余额](#1)
    -   [获取全部委托](#2)
    -   [获取全部成交记录](#3)
    -   [取消委托单](#4)
    -   [根据币对取消全部委托单](#5)
    -   [创建订单](#6)
    -   [获取所有交易对行情](#7)
    -   [获取K线数据](#8)
    -   [获取当前行情](#9)
    -   [获取行情成交记录](#10)
    -   [获取各个币对的最新成交价](#11)
    -   [查询买卖盘深度](#12)
    -   [批量下单，同时批量撤回指定订单](#13)
    -   [批量下单，同时批量撤回指定订单-V2](#27)
    -   [获取当前委托](#14)
    -   [获取订单详情](#15)
    -   [查询系统支持的所有交易对及精度](#17)
    -   [获取用户资产以及充值记录](#18)
  - [ws-api](#b7) ([Java-Demo](#ws-api-java))
    -   [订阅-K线行情](#19)
    -   [订阅-前24小时行情](#20)
    -   [订阅-深度盘口（高频）](#21)
    -   [订阅-深度盘口](#22)
    -   [订阅-实时成交信息](#23)
    -   [请求-K线历史数据](#24)
    -   [请求-成交历史数据](#25)
    -   [请求-首页24行情数据](#26)


---


# <span id="Introduction">介绍</span>

欢迎使用交易所开发者文档

本文档提供了相关API的使用方法介绍。open-api包含了资产余额，获取全部委托，获取全部成交记录等接口，ws-api则提供了K线相关功能接口。

---

# <span id="startToUse">开始使用</span>
REST，即Representational State Transfer的缩写，是一种流行的互联网传输架构。它具有结构清晰、符合标准、易于理解、扩展方便的，正得到越来越多网站的采用。其优点如下：

- 在RESTful架构中，每一个URL代表一种资源；
- 客户端和服务器之间，传递这种资源的某种表现层；
- 客户端通过四个HTTP指令，对服务器端资源进行操作，实现“表现层状态转化”。 

建议开发者使用REST API进行币币交易或者资产提现等操作。

---


# <span id="a1">API接口加密验证</span>

## <span id="a2">生成API Key</span>
在对任何请求进行签名之前，您必须通过 [交易所]【用户中心】-【API】创建一个API key。 创建key后，您将获得3个必须记住的信息：

- API Key
 
- Secret Key

API Key 和 Secret Key将由随机生成和提供
## <span id="a3">发起请求</span>
所有REST请求都必须包含以下标题：

- ACCESS-KEY API KEY作为一个字符串。
- ACCESS-SIGN 使用base64编码签名（请参阅签名消息）。
- ACCESS-TIMESTAMP 作为您的请求的时间戳。
- 所有请求都应该含有application/json类型内容，并且是有效的JSON。

## <span id="a4">签名</span>

生成待签名的字符串
    -   [open-api Demo](#open-api-java)
    
1、先将参数以其参数名的字典序升序进行排序

2、遍历排序后的字典，将所有参数按"keyvalue"格式拼接在一起（非空参数）

3、使用MD5对待签名串求签

例：

api_key = 1234567

time = 12312312312137

secret_key = 789654

sign=md5(api_key1234567time12312312312137789654)



## <span id="a6">请求交互</span>

REST访问的根URL：``` https://openapi.wbf.live ```

###  <span id="a7">请求</span>
所有请求基于Https协议，请求头信息中Content-Type 需要统一设置为:'application/json’。

**请求交互说明**

1、请求参数：根据接口请求参数规定进行参数封装。

2、提交请求参数：将封装好的请求参数通过POST/GET/DELETE等方式提交至服务器。

3、服务器响应：服务器首先对用户请求数据进行参数安全校验，通过校验后根据业务逻辑将响应数据以JSON格式返回给用户。

4、数据处理：对服务器响应数据进行处理。

**成功**

HTTP状态码200表示成功响应，并可能包含内容。如果响应含有内容，则将显示在相应的返回内容里面。

**常见错误码**

- 400 Bad Request – Invalid request forma 请求格式无效

- 401 Unauthorized – Invalid API Key 无效的API Key

- 403 Forbidden – You do not have access to the requested resource 请求无权限

- 404 Not Found 没有找到请求

- 429 Too Many Requests 请求太频繁被系统限流

- 500 Internal Server Error – We had a problem with our server 服务器内部错误

如果失败，response body 带有错误描述信息

###  <span  id="a8">分页</span>

部分返回数据集的REST请求支持使用游标分页。 游标分页允许在结果的当前页面之前和之后获取结果，并且非常适合于实时数据。根据当前的返回结果，后续请求可以在此基础之上指定请求数据的方向，可以请求在这之前和之后的数据。before和after游标可通过响应头CB_BEFORE和CB_AFTER使用。

**例子**

```GET /orders?before=2&limit=30```


## <span id="a9">标准规范</span>

### <span id="b1">时间戳</span> 
除非另外指定，API中的所有时间戳均以微秒为单位返回。

请求签名中的ACCESS-TIMESTAMP的单位是秒，允许用小数表示更精确的时间。请求的时间戳必须在API服务时间的30秒内，否则请求将被视为过期并被拒绝。如果本地服务器时间和API服务器时间之间存在较大的偏差，那么我们建议您使用通过查询API服务器时间来更新http header。

###  <span id="b2">例子</span> 
1524801032573

###  <span id="b3">数字</span> 
为了保持跨平台时精度的完整性，十进制数字作为字符串返回。建议您在发起请求时也将数字转换为字符串以避免截断和精度错误。

整数（如交易编号和顺序）不加引号。

###  <span id="b4">限流</span>  
如果请求过于频繁系统将自动限制请求，并在http header中返回429 too many requests状态码。

REST API

- 公共接口：我们通过IP限制公共接口的调用：每2秒最多6个请求。

- 私人接口：我们通过用户ID限制私人接口的调用：每2秒最多6个请求。

- 某些接口的特殊限制在具体的接口上注明

---

# <span id="b5">业务API参考</span>

##  <span id="b6">open-api</span>


### <span id="1">资产余额</span>

1. 接口地址: /open/api/user/account
2. 接口说明: (get请求)资产余额

|参数|    填写类型|   说明|
|--------|--------|--------|
|api_key|   必填| api_key|
|time|  必填| 时间戳|
|sign|  必填| 签名|

返回值:

|字段|    实例| 解释|
|--------|--------|--------|
|code|  0|   |
|msg|   "suc"|  code>0失败|
|data|  {<br>"total_asset":432323.23,<br>"coin_list":[<br>{"coin":"btc","normal":32323.233,"locked":32323.233,"btcValuatin":112.33},<br>{"coin":"ltc","normal":32323.233,"locked":32323.233,"btcValuatin":112.33},<br>{"coin":"bch","normal":32323.233,"locked":32323.233,"btcValuatin":112.33},<br>]<br>}<br>|total_asset:总
