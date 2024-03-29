# SoccerUnionApi

## 反波系统API联合登陆接口文档 

- 目录
  + [1 概要](#1-----)
    - [1.1 接口用途](#11-----)
    - [1.2 接口申请](#12-----)
    - [1.3 Header 参数](#13-----)
    - [1.4 签名](#14-----)
      * [1.4.1 签名示例](#141-----)
  + [2 接口列表](#2-----)
    + [2.1 标准版](#21-----)
      + [2.1.1 会员信息传递](#211-----)
        + [2.1.1.1 传入参数](#2111-----)
        + [2.1.1.2 返回参数](#2112-----)
        + [2.1.1.3 返回示例](#2113-----)
        + [2.1.1.4 备注](#2114-----)
      + [2.1.2 余额相关接口](#212-----)
        + [2.1.2.1 传入参数](#2121-----)
        + [2.1.2.2 返回参数](#2122-----)
        + [2.1.2.3 调用示例](#2123-----)
      + [2.1.3 会员订单历史记录](#213-----)
        + [2.1.3.1 传入参数](#2131-----)
        + [2.1.3.2 返回参数](#2132-----)
        + [2.1.3.3 调用示例](#2133-----)
      + [2.1.4 查询余额更新状态](#214-----)
        + [2.1.4.1 传入参数](#2141-----)
        + [2.1.4.2 返回参数](#2142-----)
        + [2.1.4.3 调用示例](#2143-----)
    + [2.2 联盟版](#22-----)
      + [2.2.1 会员信息传递](#221-----)
        + [2.2.1.1 传入参数](#2211-----)
        + [2.2.1.2 返回参数](#2212-----)
        + [2.2.1.3 返回示例](#2213-----)
        + [2.2.1.4 备注](#2214-----)
      + [2.2.2 余额相关接口](#222-----)
        + [2.2.2.1 传入参数](#2221-----)
        + [2.2.2.2 返回参数](#2222-----)
        + [2.2.2.3 调用示例](#2223-----)
      + [2.2.3 会员订单历史记录](#223-----)
        + [2.2.3.1 传入参数](#2231-----)
        + [2.2.3.2 返回参数](#2232-----)
        + [2.2.3.3 调用示例](#2233-----)
      + [2.2.4 查询余额更新状态](#224-----)
        + [2.2.4.1 传入参数](#2241-----)
        + [2.2.4.2 返回参数](#2242-----)
        + [2.2.4.3 调用示例](#2243-----)

### <span id="1-----">1 概要</span>

#### <span id="11-----">1.1 接口用途</span>

本接口用于将反波系统植入主体网站，实现从主体网站跳转至反波网站时能够自动识别主网用户，免注册、免登录，同时能够进行余额查询及余额更新。本接口纯Restful风格，传入参数及返回参数全部为JSON格式。

#### <span id="12-----">1.2 接口申请</span>

请联系客服申请接口，申请通过之后您可获得以下信息：

    1. 专属接口请求地址(apiAddress)
    2. 专属密钥(secretKey)
    3. 专属反波系统跳转地址

#### <span id="13-----">1.3 Header 参数</span>

请求方式：POST

| 参数名       | 必选 | 类型/参数值      | 说明         |
| ------------ | ---- | ---------------- | ------------ |
| Content-Type | 是   | application/json | 请求参数类型 |

#### <span id="14-----">1.4 签名</span>

  1. 将全部传入参数（除了sign）的参数名按照字典序排列，请注意值为空的参数无需传入
  2. 构建为链接参数格式
  3. 在最后加上secretKey（密钥请联系客服）
  4. 将字符串进行md5加密
  5. 转换为小写

##### <span id="141-----">1.4.1 签名示例</span>

 - 1.传入参数

```json
{
	"memberId": 1001,
	"actionType": "update",
	"value": "800.00"
}
```

 - 2.参数名按字典序排列

```json
{
	"actionType": "update",
	"memberId": 1001,
	"value": "800.00"
}
```

 - 3.构建为链接参数格式

actionType=update&memberId=1001&value=800.00

 - 4.在最后加上secretKey（假设secretKey为aaabbbccc）

actionType=update&memberId=1001&value=800.00aaabbbccc

 - 5.转为小写的md5，即为sign

cbc0b11733b785b0317f1cc7d6f20fd8

​    

### <span id="2-----">2 接口列表</span>



#### <span id="21-----">2.1 标准版</span>

标准版适用于集成方只有一个网站，只有一套会员体系。

联盟版适用于集成方有多个网站需要与反波系统对接，有多套会员体系，不同体系中可能存在重复的会员ID和用户名。联盟版传入参数里的agentToken用于识别不同的的体系，请联系管理员获取。

标准版和联盟版不可混用，一个集成方只可以调用其中一个版本，混用会导致会员ID重复而报错。



#### <span id="211-----">2.1.1 会员信息传递</span>

本接口用于主网向反波系统发送用户信息，以实现用户信息的传递，实现用户无感知登录反波系统。将用户ID和用户名发给接口之后，反波系统会自动判断该会员是否存在，如果不存在会自动注册。

请求地址：`{apiAddress}/union-api/user-info`

##### <span id="2111-----">2.1.1.1 传入参数</span>

| 参数名   | 必选 | 类型   | 字段长度         | 说明   |
| -------- | ---- | ------ | ---------------- | ------ |
| memberId | 是   | bigint | 0 < length <= 15 | 用户ID |
| userName | 是   | string | 1 < length < 50  | 用户名 |
| sign     | 是   | string | 32               | 签名   |

##### <span id="2112-----">2.1.1.2 返回参数</span>

| 参数名 | 类型   | 字段长度         | 说明                                                       |
| ------ | ------ | ---------------- | ---------------------------------------------------------- |
| result | int    | 1                | 调用结果，1=成功 0=失败                                    |
| token  | string | 32               | 一次性访问令牌，请让用户携带此令牌访问我系统，即可识别身份 |
| msg    | string | 1 < length < 100 | 如出错时，返回出错原因，成功时为success                    |

##### <span id="2113-----">2.1.1.3 返回示例</span>

 - 成功

```json
{
	"result": 1,
	"token": "d61054d8b02bf09048850a1c4ddab37a90fed3b0",
	"msg": "success"
}
```

 - 失败

```json
{
	"result": 0,
	"token": null,
	"msg": "签名不正确"
}
```

##### <span id="2114-----">2.1.1.4 备注</span>

成功获得返回的token之后，请将此token作为参数加在“专属反波系统跳转地址”之后传过来即可，我们可通过此token识别该用户。

例如专属反波系统跳转地址为：https://abcd.com

返回的token为：d61054d8b02bf09048850a1c4ddab37a90fed3b0

则将用户跳转至：https://abcd.com/auth?token=d61054d8b02bf09048850a1c4ddab37a90fed3b0

完毕


​    

#### <span id="212-----">2.1.2 余额相关接口</span>

用于查询会员余额，或更新会员余额。

郑重提示，主网如需对用户余额进行更新，请务必先调用本接口，接口成功返回后，以接口中的余额为准，切勿自行在主网进行余额更新，以免被人抓住漏洞，在主网和反波网站同时操作余额而导致资金错乱。

请求地址：`{apiAddress}/union-api/user-balance`

##### <span id="2121-----">2.1.2.1 传入参数</span>

| 参数名        | 必选  | 类型    | 字段长度          | 说明                                                         |
| ------------- | ----- | ------- | ----------------- | ------------------------------------------------------------ |
| memberId      | 是    | bigint  | 0 < length <= 15  | 用户ID                                                       |
| actionType    | 是    | string  | 1 < length < 50   | check=查看余额 update=更新余额                               |
| value         | 是/否 | decimal | 长度15，小数点2位 | check时无需传此参数，update时为必传，且不能为0               |
| clientOrderId | 是/否    | string  | 1 < length < 100  | update时必传，第三方订单号，可通过`balance-update-results`接口确认余额更新状态 |
| sign          | 是    | string  | 32                | 签名                                                         |

##### <span id="2122-----">2.1.2.2 返回参数</span>

| 参数名             | 类型    | 字段长度          | 说明                                                         |
| ------------------ | ------- | ----------------- | ------------------------------------------------------------ |
| result             | int     | 1                 | 调用结果，1=成功 0=失败                                      |
| balanceAfterAction | decimal | 长度15，小数点2位 | check时返回用户当前余额，update时返回用户余额更新之后的最新余额 |
| msg                | string  | 1 < length < 100  | 如出错时，返回出错原因，成功时为success                      |

##### <span id="2123-----">2.1.2.3 调用示例</span>

 - 查询余额传入参数

```json
{
	"memberId": 1001,
	"actionType": "check",
	"sign": "cbc0b11733b785b0317f1cc7d6f20fd8"
}
```

 - 查询余额返回参数（成功）

```json
{
	"result": 1,
	"balanceAfterAction": "1000.00", //说明此用户当前余额为1000
	"msg": "success"
}
```

 - 查询余额返回参数（失败）

```json
{
	"result": 0,
	"balanceAfterAction": null,
	"msg": "用户未找到"
}
```

 - 更新余额传入参数

```json
{
	"memberId": 1001,
	"actionType": "update",
	"value": "800.00", //说明用户想充值800
	"clientOrderId": "ab123456", //第三方订单号
	"sign": "cbc0b11733b785b0317f1cc7d6f20fd8"
}
```

```json
{
	"memberId": 1001,
	"actionType": "update",
	"value": "-800.00", //说明用户想扣除余额800
	"sign": "cbc0b11733b785b0317f1cc7d6f20fd8"
}
```

 - 更新余额返回参数（成功）

```json
{
	"result": 1,
	"balanceAfterAction": "1800.00", //说明此用户更新完之后的余额为1800
	"msg": "success"
}
```

 - 查询余额返回参数（失败）

```json
{
	"result": 0,
	"balanceAfterAction": "100.00",
	"msg": "用户当前余额为100.00，无法扣除800.00，扣除金额不可大于余额"
}
```


​    
​    

#### <span id="213-----">2.1.3 会员订单历史记录</span>

用于查询会员的历史订单

请求地址：`{apiAddress}/union-api/user-orders`

##### <span id="2131-----">2.1.3.1 传入参数</span>

| 参数名           | 必选 | 类型     | 字段长度         | 说明                                          |
| ---------------- | ---- | -------- | ---------------- | --------------------------------------------- |
| memberId         | 否   | bigint   | 0 < length <= 15 | 用户ID，如果不传会返回所有用户的订单          |
| startTime        | 否   | datetime | -                | 下单时间：开始时间(包含这一秒)                |
| endTime          | 否   | datetime | -                | 下单时间：结束时间(不包含这一秒)              |
| startUpdatedTime | 否   | datetime | -                | 更新时间：开始时间(包含这一秒)                |
| endUpdatedTime   | 否   | datetime | -                | 更新时间：结束时间(不包含这一秒)              |
| pageId           | 否   | int      | 1 <= length <= 3 | 页码，留空则自动拉取第1页，每页最多返回1000条 |
| sort             | 否   | string   | 3 <= length <= 4 | asc=正序，desc=倒序，留空则默认为desc         |
| sign             | 是   | string   | 32               | 签名                                          |

***下单和更新时间必须二选一，不可同时为空***

##### <span id="2132-----">2.1.3.2 返回参数</span>

| 参数名     | 类型   | 字段长度         | 说明                                      |
| ---------- | ------ | ---------------- | ----------------------------------------- |
| result     | int    | 1                | 调用结果，1=成功 0=失败                   |
| data       | array  | -                | 订单数据，参考以下表格                    |
| totalPages | int    | 1 < length < 4   | 总页数，如果大于1说明后面还有页数，需翻页 |
| msg        | string | 1 < length < 100 | 如出错时，返回出错原因，成功时为success   |

  >> data订单数据格式

| 参数名          | 类型     | 字段长度           | 说明                                  |
| --------------- | -------- | ------------------ | ------------------------------------- |
| orderId         | decimal  | 7 <= length <= 18  | 订单号                                |
| memberId        | bigint   | 0 < length <= 15   | 用户ID                                |
| matchId         | string   | 0 < length < 20    | 赛事ID(可能带有英文字母)              |
| kickoffTime     | datetime | -                  | 开赛时间                              |
| userName        | string   | 1 < length < 50    | 用户名                                |
| leagueNameCn    | string   | 5 <= length <= 50  | 联赛名(中文)                          |
| leagueNameEn    | string   | 5 <= length <= 100 | 联赛名(英文)                          |
| homeTeamNameCn  | string   | 5 <= length <= 50  | 主队名(中文)                          |
| homeTeamNameEn  | string   | 5 <= length <= 100 | 主队名(英文)                          |
| awayTeamNameCn  | string   | 5 <= length <= 50  | 客队名(中文)                          |
| awayTeamNameEn  | string   | 5 <= length <= 100 | 客队名(英文)                          |
| homeHalfResult  | int      | 1 <= length <= 2   | 主队半场比分                          |
| awayHalfResult  | int      | 1 <= length <= 2   | 客队半场比分                          |
| homeFinalResult | int      | 1 <= length <= 2   | 主队全场比分                          |
| awayFinalResult | int      | 1 <= length <= 2   | 客队全场比分                          |
| homeBetScore    | int      | 1 <= length <= 2   | 下单主队比分                          |
| awayBetScore    | int      | 1 <= length <= 2   | 下单客队比分                          |
| betType         | int      | 1                  | 1=全场 2=半场                         |
| betValue        | decimal  | 长度16，小数点2位  | 下注金额                              |
| bonusRate       | decimal  | 长度10，小数点4位  | 获利率。例如12.2500代表获利率为12.25% |
| bonus           | decimal  | 长度16，小数点2位  | 获利                                  |
| status          | int      | 1                  | 0=未返利 1=赢 2=输 3=订单取消         |
| createTime      | datetime | -                  | 订单生成时间                          |
| payTime         | datetime | -                  | 派彩时间                              |
| updatedTime     | datetime | -                  | 最后更新时间                          |

##### <span id="2133-----">2.1.3.3 调用示例</span>

 - 传入参数

```json
{
	"memberId": 1001,
	"startTime": "2021-10-01 00:00:00",
	"endTime": "2021-10-01 23:59:59",
	"pageId": 1,
	"sort": "desc",
	"sign": "cbc0b11733b785b0317f1cc7d6f20fd8"
}
```

 - 返回参数（成功）

```json
{
	"result": 1,
	"data": [{
		"orderId": 23878254,
		"memberId": 1001,
		"matchId": "EHK987287615",
		"kickoffTime": "2023-04-20 22:00:00",
		"userName": "abc123",
		"leagueNameCn": "美女超",
		"leagueNameEn": "USA WPSL Women",
		"homeTeamNameCn": "芝加哥城SC女足",
		"homeTeamNameEn": "Chicago City SC Women",
		"awayTeamNameCn": "密尔沃基巴伐利亚女足",
		"awayTeamNameEn": "Milwaukee Bavarian SC Women",
		"homeHalfResult": 2,
		"awayHalfResult": 1,
		"homeFinalResult": 3,
		"awayFinalResult": 3,
		"homeBetScore": 3,
		"awayBetScore": 1,
		"betType": 1, //投注全场
		"betValue": 10000.00,
		"bonusRate": 12.5100,
		"bonus": 1188.45,
		"status": 1, //赢
		"createTime": "2021-10-01 12:30:35",
		"payTime": "2021-10-01 12:35:01",
		"updatedTime": "2021-10-01 12:35:01"
	}, {
		"orderId": 23878611,
		"memberId": 1001,
		"matchId": "9846737835",
		"kickoffTime": "2023-04-20 22:00:00",
		"userName": "abc123",
		"leagueNameCn": "孟冠联",
		"leagueNameEn": "Bangladesh Championship League",
		"homeTeamNameCn": "法奇拉坡尔",
		"homeTeamNameEn": "Fakirapool Young Men's Club",
		"awayTeamNameCn": "福提斯",
		"awayTeamNameEn": "Fortis FC",
		"homeHalfResult": 1,
		"awayHalfResult": 1,
		"homeFinalResult": 2,
		"awayFinalResult": 1,
		"homeBetScore": 1,
		"awayBetScore": 1,
		"betType": 2, //投注半场
		"betValue": 2000.00,
		"bonusRate": 3.3000,
		"bonus": -2000.00,
		"status": 2, //输
		"createTime": "2021-10-01 12:30:35",
		"payTime": null,
		"updatedTime": null
	}],
	"totalPages": 2, //还有第2页，传入参数里pageId改为2，可获取第2页内容
	"msg": "success"
}
```

 - 返回参数（失败）

```json
{
	"result": 0,
	"data": [],
	"totalPages": null,
	"msg": "页码不存在"
}
```


​    
​    

#### <span id="214-----">2.1.4 查询余额更新状态</span>

会员余额增加或扣减后，可通过本接口查询状态以确认操作是否成功。

请求地址：`{apiAddress}/union-api/balance-update-results`

##### <span id="2141-----">2.1.4.1 传入参数</span>

| 参数名        | 必选 | 类型   | 字段长度          | 说明                                                         |
| ------------- | ---- | ------ | ----------------- | ------------------------------------------------------------ |
| clientOrderId | 否   | string | 1 < length < 1024 | 支持传单个或多个clientOrderId，如需传多个请用`｜`隔开，例如aaa\|bbb\|ccc，最多一次性查询100条 |
| sign          | 是   | string | 32                | 签名                                                         |

##### <span id="2142-----">2.1.4.2 返回参数</span>

| 参数名 | 类型   | 字段长度                                                     | 说明                                    |
| ------ | ------ | ------------------------------------------------------------ | --------------------------------------- |
| result | int    | 1                                                            | 调用结果，1=成功 0=失败                 |
| data   | array  | 以数组形式返回查询的clientOrderId订单状态，成功为success，失败为failed |                                         |
| msg    | string | 1 < length < 100                                             | 如出错时，返回出错原因，成功时为success |

##### <span id="2143-----">2.1.4.3 调用示例</span>

 - 查询余额传入参数

```json
{
	"clientOrderId": "aaa|bbb|ccc",
	"sign": "cbc0b11733b785b0317f1cc7d6f20fd8"
}
```

 - 返回参数

```json
{
	"result": 1,
	"data": {
		"aaa": "success",
		"bbb": "success",
		"ccc": "failed"
	},
	"msg": "success"
}
```





#### <span id="22-----">2.2 联盟版</span>

标准版适用于集成方只有一个网站，只有一套会员体系。

联盟版适用于集成方有多个网站需要与反波系统对接，有多套会员体系，不同体系中可能存在重复的会员ID和用户名。联盟版传入参数里的agentToken用于识别不同的的体系，请联系管理员获取。

标准版和联盟版不可混用，一个集成方只可以调用其中一个版本，混用会导致会员ID重复而报错。



#### <span id="221-----">2.2.1 会员信息传递</span>

本接口用于主网向反波系统发送用户信息，以实现用户信息的传递，实现用户无感知登录反波系统。将用户ID和用户名发给接口之后，反波系统会自动判断该会员是否存在，如果不存在会自动注册。

请求地址：`{apiAddress}/union-api-v2/user-info`

##### <span id="2211-----">2.2.1.1 传入参数</span>

| 参数名     | 必选 | 类型   | 字段长度         | 说明       |
| ---------- | ---- | ------ | ---------------- | ---------- |
| memberId   | 否   | bigint | 0 < length <= 15 | 用户ID (如果为空，系统会自动为该用户名生成一个会员ID)     |
| userName   | 是   | string | 1 < length < 50  | 用户名     |
| agentToken | 是   | string | 1 < length < 50  | 代理识别号 |
| sign       | 是   | string | 32               | 签名       |

##### <span id="2212-----">2.2.1.2 返回参数</span>

| 参数名 | 类型   | 字段长度         | 说明                                                       |
| ------ | ------ | ---------------- | ---------------------------------------------------------- |
| result | int    | 1                | 调用结果，1=成功 0=失败                                    |
| token  | string | 32               | 一次性访问令牌，请让用户携带此令牌访问我系统，即可识别身份 |
| msg    | string | 1 < length < 100 | 如出错时，返回出错原因，成功时为success                    |

##### <span id="2213-----">2.2.1.3 返回示例</span>

 - 成功

```json
{
	"result": 1,
	"token": "d61054d8b02bf09048850a1c4ddab37a90fed3b0",
	"msg": "success"
}
```

 - 失败

```json
{
	"result": 0,
	"token": null,
	"msg": "签名不正确"
}
```

##### <span id="2214-----">2.2.1.4 备注</span>

成功获得返回的token之后，请将此token作为参数加在“专属反波系统跳转地址”之后传过来即可，我们可通过此token识别该用户。

例如专属反波系统跳转地址为：https://abcd.com

返回的token为：d61054d8b02bf09048850a1c4ddab37a90fed3b0

则将用户跳转至：https://abcd.com/auth?token=d61054d8b02bf09048850a1c4ddab37a90fed3b0

完毕


​    

#### <span id="222-----">2.2.2 余额相关接口</span>

用于查询会员余额，或更新会员余额。

郑重提示，主网如需对用户余额进行更新，请务必先调用本接口，接口成功返回后，以接口中的余额为准，切勿自行在主网进行余额更新，以免被人抓住漏洞，在主网和反波网站同时操作余额而导致资金错乱。

请求地址：`{apiAddress}/union-api-v2/user-balance`

##### <span id="2221-----">2.2.2.1 传入参数</span>

| 参数名        | 必选  | 类型    | 字段长度          | 说明                                                         |
| ------------- | ----- | ------- | ----------------- | ------------------------------------------------------------ |
| memberId      | 否    | bigint  | 0 < length <= 15  | 用户ID (ID和用户名只能选其一，不能同时传)                                                      |
| userName      | 否    | string  | 1 < length <= 50  | 用户名 (ID和用户名只能选其一，不能同时传)                                                       |
| agentToken    | 是    | string  | 1 < length < 50   | 代理识别号                                                   |
| actionType    | 是    | string  | 1 < length < 50   | check=查看余额 update=更新余额                               |
| value         | 是/否 | decimal | 长度15，小数点2位 | check时无需传此参数，update时为必传，且不能为0               |
| clientOrderId | 是/否    | string  | 1 < length < 100  | update时必传，第三方订单号，可通过`balance-update-results`接口确认余额更新状态 |
| sign          | 是    | string  | 32                | 签名                                                         |

##### <span id="2222-----">2.2.2.2 返回参数</span>

| 参数名             | 类型    | 字段长度          | 说明                                                         |
| ------------------ | ------- | ----------------- | ------------------------------------------------------------ |
| result             | int     | 1                 | 调用结果，1=成功 0=失败                                      |
| balanceAfterAction | decimal | 长度15，小数点2位 | check时返回用户当前余额，update时返回用户余额更新之后的最新余额 |
| msg                | string  | 1 < length < 100  | 如出错时，返回出错原因，成功时为success                      |

##### <span id="2223-----">2.2.2.3 调用示例</span>

 - 查询余额传入参数

```json
{
	"memberId": 1001,
  "agentToken": "7c4a8d09ca3762af61e59520943dc26494f8941b",
	"actionType": "check",
	"sign": "cbc0b11733b785b0317f1cc7d6f20fd8"
}
```

 - 查询余额返回参数（成功）

```json
{
	"result": 1,
	"balanceAfterAction": "1000.00", //说明此用户当前余额为1000
	"msg": "success"
}
```

 - 查询余额返回参数（失败）

```json
{
	"result": 0,
	"balanceAfterAction": null,
	"msg": "用户未找到"
}
```

 - 更新余额传入参数

```json
{
	"memberId": 1001,
  "agentToken": "7c4a8d09ca3762af61e59520943dc26494f8941b",
	"actionType": "update",
	"value": "800.00", //说明用户想充值800
	"clientOrderId": "ab123456", //第三方订单号
	"sign": "cbc0b11733b785b0317f1cc7d6f20fd8"
}
```

```json
{
	"memberId": 1001,
  "agentToken": "7c4a8d09ca3762af61e59520943dc26494f8941b",
	"actionType": "update",
	"value": "-800.00", //说明用户想扣除余额800
	"sign": "cbc0b11733b785b0317f1cc7d6f20fd8"
}
```

 - 更新余额返回参数（成功）

```json
{
	"result": 1,
	"balanceAfterAction": "1800.00", //说明此用户更新完之后的余额为1800
	"msg": "success"
}
```

 - 查询余额返回参数（失败）

```json
{
	"result": 0,
	"balanceAfterAction": "100.00",
	"msg": "用户当前余额为100.00，无法扣除800.00，扣除金额不可大于余额"
}
```


​    
​    

#### <span id="223-----">2.2.3 会员订单历史记录</span>

用于查询会员的历史订单

请求地址：`{apiAddress}/union-api-v2/user-orders`

##### <span id="2231-----">2.2.3.1 传入参数</span>

| 参数名           | 必选 | 类型     | 字段长度         | 说明                                          |
| ---------------- | ---- | -------- | ---------------- | --------------------------------------------- |
| memberId         | 否   | bigint   | 0 < length <= 15 | 用户ID，如果不传会返回所有用户的订单          |
| userName         | 否   | string   | 1 < length <= 50 | 用户名 (ID和用户名只能选其一，不能同时传)        |
| agentToken       | 是   | string   | 1 < length < 50  | 代理识别号                                    |
| startTime        | 否   | datetime | -                | 下单时间：开始时间(包含这一秒)                |
| endTime          | 否   | datetime | -                | 下单时间：结束时间(不包含这一秒)              |
| startUpdatedTime | 否   | datetime | -                | 更新时间：开始时间(包含这一秒)                |
| endUpdatedTime   | 否   | datetime | -                | 更新时间：结束时间(不包含这一秒)              |
| pageId           | 否   | int      | 1 <= length <= 3 | 页码，留空则自动拉取第1页，每页最多返回1000条 |
| sort             | 否   | string   | 3 <= length <= 4 | asc=正序，desc=倒序，留空则默认为desc         |
| sign             | 是   | string   | 32               | 签名                                          |

***下单和更新时间必须二选一，不可同时为空***

##### <span id="2232-----">2.2.3.2 返回参数</span>

| 参数名     | 类型   | 字段长度         | 说明                                      |
| ---------- | ------ | ---------------- | ----------------------------------------- |
| result     | int    | 1                | 调用结果，1=成功 0=失败                   |
| data       | array  | -                | 订单数据，参考以下表格                    |
| totalPages | int    | 1 < length < 4   | 总页数，如果大于1说明后面还有页数，需翻页 |
| msg        | string | 1 < length < 100 | 如出错时，返回出错原因，成功时为success   |

  >> data订单数据格式

| 参数名          | 类型     | 字段长度           | 说明                                  |
| --------------- | -------- | ------------------ | ------------------------------------- |
| orderId         | decimal  | 7 <= length <= 18  | 订单号                                |
| memberId        | bigint   | 0 < length <= 15   | 用户ID                                |
| matchId         | string   | 0 < length < 20    | 赛事ID(可能带有英文字母)              |
| kickoffTime     | datetime | -                  | 开赛时间                              |
| userName        | string   | 1 < length < 50    | 用户名                                |
| leagueNameCn    | string   | 5 <= length <= 50  | 联赛名(中文)                          |
| leagueNameEn    | string   | 5 <= length <= 100 | 联赛名(英文)                          |
| homeTeamNameCn  | string   | 5 <= length <= 50  | 主队名(中文)                          |
| homeTeamNameEn  | string   | 5 <= length <= 100 | 主队名(英文)                          |
| awayTeamNameCn  | string   | 5 <= length <= 50  | 客队名(中文)                          |
| awayTeamNameEn  | string   | 5 <= length <= 100 | 客队名(英文)                          |
| homeHalfResult  | int      | 1 <= length <= 2   | 主队半场比分                          |
| awayHalfResult  | int      | 1 <= length <= 2   | 客队半场比分                          |
| homeFinalResult | int      | 1 <= length <= 2   | 主队全场比分                          |
| awayFinalResult | int      | 1 <= length <= 2   | 客队全场比分                          |
| homeBetScore    | int      | 1 <= length <= 2   | 下单主队比分                          |
| awayBetScore    | int      | 1 <= length <= 2   | 下单客队比分                          |
| betType         | int      | 1                  | 1=全场 2=半场                         |
| betValue        | decimal  | 长度16，小数点2位  | 下注金额                              |
| bonusRate       | decimal  | 长度10，小数点4位  | 获利率。例如12.2500代表获利率为12.25% |
| bonus           | decimal  | 长度16，小数点2位  | 获利                                  |
| status          | int      | 1                  | 0=未返利 1=赢 2=输 3=订单取消         |
| createTime      | datetime | -                  | 订单生成时间                          |
| payTime         | datetime | -                  | 派彩时间                              |
| updatedTime     | datetime | -                  | 最后更新时间                          |

##### <span id="2233-----">2.2.3.3 调用示例</span>

 - 传入参数

```json
{
	"memberId": 1001,
  "agentToken": "7c4a8d09ca3762af61e59520943dc26494f8941b",
	"startTime": "2021-10-01 00:00:00",
	"endTime": "2021-10-01 23:59:59",
	"pageId": 1,
	"sort": "desc",
	"sign": "cbc0b11733b785b0317f1cc7d6f20fd8"
}
```

 - 返回参数（成功）

```json
{
	"result": 1,
	"data": [{
		"orderId": 23878254,
		"memberId": 1001,
		"matchId": "EHK987287615",
		"kickoffTime": "2023-04-20 22:00:00",
		"userName": "abc123",
		"leagueNameCn": "美女超",
		"leagueNameEn": "USA WPSL Women",
		"homeTeamNameCn": "芝加哥城SC女足",
		"homeTeamNameEn": "Chicago City SC Women",
		"awayTeamNameCn": "密尔沃基巴伐利亚女足",
		"awayTeamNameEn": "Milwaukee Bavarian SC Women",
		"homeHalfResult": 2,
		"awayHalfResult": 1,
		"homeFinalResult": 3,
		"awayFinalResult": 3,
		"homeBetScore": 3,
		"awayBetScore": 1,
		"betType": 1, //投注全场
		"betValue": 10000.00,
		"bonusRate": 12.5100,
		"bonus": 1188.45,
		"status": 1, //赢
		"createTime": "2021-10-01 12:30:35",
		"payTime": "2021-10-01 12:35:01",
		"updatedTime": "2021-10-01 12:35:01"
	}, {
		"orderId": 23878611,
		"memberId": 1001,
		"matchId": "9846737835",
		"kickoffTime": "2023-04-20 22:00:00",
		"userName": "abc123",
		"leagueNameCn": "孟冠联",
		"leagueNameEn": "Bangladesh Championship League",
		"homeTeamNameCn": "法奇拉坡尔",
		"homeTeamNameEn": "Fakirapool Young Men's Club",
		"awayTeamNameCn": "福提斯",
		"awayTeamNameEn": "Fortis FC",
		"homeHalfResult": 1,
		"awayHalfResult": 1,
		"homeFinalResult": 2,
		"awayFinalResult": 1,
		"homeBetScore": 1,
		"awayBetScore": 1,
		"betType": 2, //投注半场
		"betValue": 2000.00,
		"bonusRate": 3.3000,
		"bonus": -2000.00,
		"status": 2, //输
		"createTime": "2021-10-01 12:30:35",
		"payTime": null,
		"updatedTime": null
	}],
	"totalPages": 2, //还有第2页，传入参数里pageId改为2，可获取第2页内容
	"msg": "success"
}
```

 - 返回参数（失败）

```json
{
	"result": 0,
	"data": [],
	"totalPages": null,
	"msg": "页码不存在"
}
```


​    
​    

#### <span id="224-----">2.2.4 查询余额更新状态</span>

会员余额增加或扣减后，可通过本接口查询状态以确认操作是否成功。

请求地址：`{apiAddress}/union-api-v2/balance-update-results`

##### <span id="2241-----">2.2.4.1 传入参数</span>

| 参数名        | 必选 | 类型   | 字段长度          | 说明                                                         |
| ------------- | ---- | ------ | ----------------- | ------------------------------------------------------------ |
| clientOrderId | 否   | string | 1 < length < 1024 | 支持传单个或多个clientOrderId，如需传多个请用`｜`隔开，例如aaa\|bbb\|ccc，最多一次性查询100条 |
| sign          | 是   | string | 32                | 签名                                                         |

##### <span id="2242-----">2.2.4.2 返回参数</span>

| 参数名 | 类型   | 字段长度                                                     | 说明                                    |
| ------ | ------ | ------------------------------------------------------------ | --------------------------------------- |
| result | int    | 1                                                            | 调用结果，1=成功 0=失败                 |
| data   | array  | 以数组形式返回查询的clientOrderId订单状态，成功为success，失败为failed |                                         |
| msg    | string | 1 < length < 100                                             | 如出错时，返回出错原因，成功时为success |

##### <span id="2243-----">2.2.4.3 调用示例</span>

 - 查询余额传入参数

```json
{
	"clientOrderId": "aaa|bbb|ccc",
	"sign": "cbc0b11733b785b0317f1cc7d6f20fd8"
}
```

 - 返回参数

```json
{
	"result": 1,
	"data": {
		"aaa": "success",
		"bbb": "success",
		"ccc": "failed"
	},
	"msg": "success"
}
```

