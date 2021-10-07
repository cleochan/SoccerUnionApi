# SoccerUnionApi

## 反波系统API联合登陆接口文档 

### 1 概要

#### 1.1 Header 参数  

| 参数名       | 必选 | 类型/参数值      | 说明         |
| ------------ | ---- | ---------------- | ------------ |
| Content-Type | 是   | application/json | 请求参数类型 |

#### 1.2 签名

 1. 将全部传入参数（除了sign）的参数名按照字典序排列
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

向我系统推送会员信息，如果memberId为第一次推送，会在我系统自动注册为新会员并自动登录，如果memberId已存在（之前有推送过），则会自动登录。

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
	"msg": "用户ID为必填项"
}
```

##### 2.1.4 备注

成功获得返回的token之后，请将此token作为参数加在反波系统域名之后传过来即可，我们可通过此token识别该用户。

例如反波系统的域名为：https://hcwin68.com

返回的token为：d61054d8b02bf09048850a1c4ddab37a90fed3b0

则将用户跳转至：https://hcwin68.com?token=d61054d8b02bf09048850a1c4ddab37a90fed3b0

完毕

​    
​    

#### 2.2 余额相关接口

用于查询会员余额，或更新会员余额

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
