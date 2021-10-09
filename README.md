# SoccerUnionApi

## 反波系统API联合登陆接口文档 

### 1 概要

#### 1.1 接口用途

本接口用于将反波系统植入主体网站，实现从主体网站跳转至反波网站时能够自动识别主网用户，免注册、免登录，同时能够进行余额查询及余额更新。本接口纯Restful风格，传入参数及返回参数全部为JSON格式。

#### 1.2 接口申请

请联系客服申请接口，申请通过之后您可获得以下信息：

  1. 专属接口请求地址(apiAddress)
  2. 专属密钥(secretKey)
  3. 专属反波系统跳转地址

#### 1.3 Header 参数

请求方式：POST

| 参数名       | 必选 | 类型/参数值      | 说明         |
| ------------ | ---- | ---------------- | ------------ |
| Content-Type | 是   | application/json | 请求参数类型 |

#### 1.4 签名

 1. 将全部传入参数（除了sign）的参数名按照字典序排列，请注意值为空的参数无需传入
 2. 构建为链接参数格式
 3. 在最后加上secretKey（密钥请联系客服）
 4. 将字符串进行md5加密
 5. 转换为小写

##### 1.2.1 签名示例

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

### 2 接口列表

​    

#### 2.1 会员信息传递

本接口用于主网向反波系统发送用户信息，以实现用户信息的传递，实现用户无感知登陆反波系统。

请求地址：`{apiAddress}/union-api/user-info`

##### 2.1.1 传入参数  

| 参数名    | 必选 | 类型   | 字段长度        | 说明     |
| --------- | ---- | ------ | --------------- | -------- |
| memberId   | 是   | int | 0 < length < 11 | 用户ID |
| userName  | 是   | string | 1 < length < 50 | 用户名     |
| sign  | 是   | string | 32 | 签名     |

##### 2.1.2 返回参数  

| 参数名     | 类型   | 字段长度        | 说明     |
| ---------  | ------ | --------------- | -------- |
| result      | int | 1 | 调用结果，1=成功 0=失败 |
| token     | string | 32 | 一次性访问令牌，请让用户携带此令牌访问我系统，即可识别身份  |
| msg     | string | 1 < length < 100 | 如出错时，返回出错原因，成功时为success |

##### 2.1.3 返回示例

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

##### 2.1.4 备注

成功获得返回的token之后，请将此token作为参数加在“专属反波系统跳转地址”之后传过来即可，我们可通过此token识别该用户。

例如专属反波系统跳转地址为：https://hcwin68.com

返回的token为：d61054d8b02bf09048850a1c4ddab37a90fed3b0

则将用户跳转至：https://hcwin68.com?token=d61054d8b02bf09048850a1c4ddab37a90fed3b0

完毕

​    
​    

#### 2.2 余额相关接口

用于查询会员余额，或更新会员余额。

郑重提示，主网如需对用户余额进行更新，请务必先调用本接口，接口成功返回后，以接口中的余额为准，切勿自行在主网进行余额更新，以免被人抓住漏洞，在主网和反波网站同时操作余额而导致资金错乱。

请求地址：`{apiAddress}/union-api/user-balance`

##### 2.2.1 传入参数  

| 参数名    | 必选 | 类型   | 字段长度        | 说明     |
| --------- | ---- | ------ | --------------- | -------- |
| memberId   | 是   | int | 0 < length < 11 | 用户ID |
| actionType  | 是   | string | 1 < length < 50 | check=查看余额 update=更新余额     |
| value  | 是/否   | decimal | 长度15，小数点2位 | check时无需传此参数，update时为必传，且不能为0     |
| sign  | 是   | string | 32 | 签名     |

##### 2.2.2 返回参数  

| 参数名     | 类型   | 字段长度        | 说明     |
| ---------  | ------ | --------------- | -------- |
| result      | int | 1 | 调用结果，1=成功 0=失败 |
| balanceAfterAction     | decimal | 长度15，小数点2位 | check时返回用户当前余额，update时返回用户余额更新之后的最新余额  |
| msg     | string | 1 < length < 100 | 如出错时，返回出错原因，成功时为success |

##### 2.2.3 调用示例

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
