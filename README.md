# SoccerUnionApi

## 反波系统API联合登陆接口文档 

- 目录
    + [1 概要](#1---)
      - [1.1 接口用途](#11-----)
      - [1.2 接口申请](#12-----)
      - [1.3 Header 参数](#13-header---)
      - [1.4 签名](#14---)
        * [1.4.1 签名示例](#141-----)
    + [2 接口列表](#2-----)
      - [2.1 会员信息传递](#21-------)
        * [2.1.1 传入参数](#211-----)
        * [2.1.2 返回参数](#212-----)
        * [2.1.3 返回示例](#213-----)
        * [2.1.4 备注](#214---)
      - [2.2 余额相关接口](#22-------)
        * [2.2.1 传入参数](#221-----)
        * [2.2.2 返回参数](#222-----)
        * [2.2.3 调用示例](#223-----)
      - [2.3 会员订单历史记录](#23---------)
        * [2.3.1 传入参数](#231-----)
        * [2.3.2 返回参数](#232-----)
        * [2.3.3 调用示例](#233-----)

<span id="1-----">### 1 概要</span>

<span id="11-----">#### 1.1 接口用途</span>

本接口用于将反波系统植入主体网站，实现从主体网站跳转至反波网站时能够自动识别主网用户，免注册、免登录，同时能够进行余额查询及余额更新。本接口纯Restful风格，传入参数及返回参数全部为JSON格式。

<span id="12-----">#### 1.2 接口申请</span>

请联系客服申请接口，申请通过之后您可获得以下信息：

  1. 专属接口请求地址(apiAddress)
  2. 专属密钥(secretKey)
  3. 专属反波系统跳转地址

<span id="13-----">#### 1.3 Header 参数</span>

请求方式：POST

| 参数名       | 必选 | 类型/参数值      | 说明         |
| ------------ | ---- | ---------------- | ------------ |
| Content-Type | 是   | application/json | 请求参数类型 |

<span id="14-----">#### 1.4 签名</span>

 1. 将全部传入参数（除了sign）的参数名按照字典序排列，请注意值为空的参数无需传入
 2. 构建为链接参数格式
 3. 在最后加上secretKey（密钥请联系客服）
 4. 将字符串进行md5加密
 5. 转换为小写

<span id="141-----">##### 1.4.1 签名示例</span>

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

<span id="2-----">### 2 接口列表</span>

​    

<span id="21-----">#### 2.1 会员信息传递</span>

本接口用于主网向反波系统发送用户信息，以实现用户信息的传递，实现用户无感知登陆反波系统。

请求地址：`{apiAddress}/union-api/user-info`

<span id="211-----">##### 2.1.1 传入参数</span>

| 参数名    | 必选 | 类型   | 字段长度        | 说明     |
| --------- | ---- | ------ | --------------- | -------- |
| memberId   | 是   | int | 0 < length < 11 | 用户ID |
| userName  | 是   | string | 1 < length < 50 | 用户名     |
| sign  | 是   | string | 32 | 签名     |

<span id="212-----">##### 2.1.2 返回参数</span>

| 参数名     | 类型   | 字段长度        | 说明     |
| ---------  | ------ | --------------- | -------- |
| result      | int | 1 | 调用结果，1=成功 0=失败 |
| token     | string | 32 | 一次性访问令牌，请让用户携带此令牌访问我系统，即可识别身份  |
| msg     | string | 1 < length < 100 | 如出错时，返回出错原因，成功时为success |

<span id="213-----">##### 2.1.3 返回示例</span>

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

<span id="214-----">##### 2.1.4 备注</span>

成功获得返回的token之后，请将此token作为参数加在“专属反波系统跳转地址”之后传过来即可，我们可通过此token识别该用户。

例如专属反波系统跳转地址为：https://hcwin68.com

返回的token为：d61054d8b02bf09048850a1c4ddab37a90fed3b0

则将用户跳转至：https://hcwin68.com?token=d61054d8b02bf09048850a1c4ddab37a90fed3b0

完毕

​    
​    

<span id="22-----">#### 2.2 余额相关接口</span>

用于查询会员余额，或更新会员余额。

郑重提示，主网如需对用户余额进行更新，请务必先调用本接口，接口成功返回后，以接口中的余额为准，切勿自行在主网进行余额更新，以免被人抓住漏洞，在主网和反波网站同时操作余额而导致资金错乱。

请求地址：`{apiAddress}/union-api/user-balance`

<span id="221-----">##### 2.2.1 传入参数</span>

| 参数名    | 必选 | 类型   | 字段长度        | 说明     |
| --------- | ---- | ------ | --------------- | -------- |
| memberId   | 是   | int | 0 < length < 11 | 用户ID |
| actionType  | 是   | string | 1 < length < 50 | check=查看余额 update=更新余额     |
| value  | 是/否   | decimal | 长度15，小数点2位 | check时无需传此参数，update时为必传，且不能为0     |
| sign  | 是   | string | 32 | 签名     |

<span id="222-----">##### 2.2.2 返回参数</span>

| 参数名     | 类型   | 字段长度        | 说明     |
| ---------  | ------ | --------------- | -------- |
| result      | int | 1 | 调用结果，1=成功 0=失败 |
| balanceAfterAction     | decimal | 长度15，小数点2位 | check时返回用户当前余额，update时返回用户余额更新之后的最新余额  |
| msg     | string | 1 < length < 100 | 如出错时，返回出错原因，成功时为success |

<span id="223-----">##### 2.2.3 调用示例</span>

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

<span id="23-----">#### 2.3 会员订单历史记录</span>

用于查询会员的历史订单

请求地址：`{apiAddress}/union-api/user-orders`

<span id="231-----">##### 2.3.1 传入参数</span>

| 参数名    | 必选 | 类型   | 字段长度        | 说明     |
| --------- | ---- | ------ | --------------- | -------- |
| memberId   | 是   | int | 0 < length < 11 | 用户ID |
| startTime  | 是   | datetime | - | 搜索开始时间(包含这一秒)     |
| endTime  | 是   | datetime | - | 搜索结束时间(不包含这一秒)     |
| pageId  | 否   | int | 1 <= length <= 3 | 页码，留空则自动拉取第1页，每页最多返回1000条     |
| sort  | 否   | string | 3 <= length <= 4 | asc=正序，desc=倒序，留空则默认为desc    |
| sign  | 是   | string | 32 | 签名     |

<span id="232-----">##### 2.3.2 返回参数</span>

| 参数名     | 类型   | 字段长度        | 说明     |
| ---------  | ------ | --------------- | -------- |
| result      | int | 1 | 调用结果，1=成功 0=失败 |
| data     | array | - | 订单数据，参考以下表格  |
| totalPages     | int | 1 < length < 4 | 总页数，如果大于1说明后面还有页数，需翻页 |
| msg     | string | 1 < length < 100 | 如出错时，返回出错原因，成功时为success |

  >> data订单数据格式

| 参数名     | 类型   | 字段长度        | 说明     |
| ---------  | ------ | --------------- | -------- |
| orderId      | decimal | 7 <= length <= 18 | 订单号 |
| leagueNameCn     | string | 5 <= length <= 50 | 联赛名(中文)  |
| leagueNameEn     | string | 5 <= length <= 100 | 联赛名(英文)  |
| homeTeamNameCn     | string | 5 <= length <= 50 | 主队名(中文)  |
| homeTeamNameEn     | string | 5 <= length <= 100 | 主队名(英文)  |
| awayTeamNameCn     | string | 5 <= length <= 50 | 客队名(中文)  |
| awayTeamNameEn     | string | 5 <= length <= 100 | 客队名(英文)  |
| homeHalfResult     | int | 1 <= length <= 2 | 主队半场比分  |
| awayHalfResult     | int | 1 <= length <= 2 | 客队半场比分  |
| homeFinalResult     | int | 1 <= length <= 2 | 主队全场比分  |
| awayFinalResult     | int | 1 <= length <= 2 | 客队全场比分  |
| homeBetScore     | int | 1 <= length <= 2 | 主队下单比分  |
| awayBetScore     | int | 1 <= length <= 2 | 客队下单比分  |
| betType     | int | 1 | 1=全场 2=半场  |
| status     | int | 1 | 0=未结算 1=赢 2=输 3=订单取消  |
| createTime     | datetime | - | 订单生成时间  |
| payTime     | datetime | - | 派彩时间  |

<span id="233-----">##### 2.3.3 调用示例</span>

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
		"status": 1, //赢
		"createTime": "2021-10-01 12:30:35",
		"payTime": "2021-10-01 12:35:01"
	}, {
		"orderId": 23878611,
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
		"status": 2, //输
		"createTime": "2021-10-01 12:30:35",
		"payTime": null
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

