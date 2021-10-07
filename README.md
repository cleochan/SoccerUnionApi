# SoccerUnionApi

## 反波系统API套壳专用接口文档 


​    
​    

### 1 签名方式

用户注册接口    

### 2 接口列表

#### 2.1 会员信息传递

向我系统推送会员信息，如果memberId为第一次推送，会在我系统自动注册为新会员并自动登录，如果memberId已存在（之前有推送过），则会自动登录。

请求地址：`{apiAddress}/union-api/user/info`

##### 2.1.1 传入参数  

| 参数名    | 必选 | 类型   | 限制条件        | 说明     |
| --------- | ---- | ------ | --------------- | -------- |
| memberId   | 是   | int | 0 < length < 11 | 用户ID |
| userName  | 是   | string | 1 < length < 50 | 用户名     |

##### 2.1.2 返回参数  

| 参数名     | 类型   | 限制条件        | 说明     |
| ---------  | ------ | --------------- | -------- |
| result      | int | 1 | 调用结果，1=成功 0=失败 |
| token     | string | 32 | 一次性访问令牌，请让用户携带此令牌访问我系统，即可识别身份  |
| msg     | string | 1 < length < 100 | 如出错时，返回出错原因，成功时为success |





- 用户信息注册  
- 用户可以通过 手机号/邮箱 进行注册  
- 同一个 手机号/邮箱只能注册一个账号  

### 3 请求地址  

`{apiAddress}/api/user/signup`  

### 4 请求方式  

**POST**  

### 5 请求参数  

#### 5.1 Header 参数  

| 参数名       | 必选 | 类型/参数值      | 说明         |
| ------------ | ---- | ---------------- | ------------ |
| Content-Type | 是   | application/json | 请求参数类型 |

#### 5.2 Body 参数  

| 参数名    | 必选 | 类型   | 限制条件        | 说明     |
| --------- | ---- | ------ | --------------- | -------- |
| account   | 是   | string | 1 < length < 50 | 用户账号 |
| passcode  | 是   | string | 1 < length < 50 | 密码     |
| checkCode | 是   | string | length = 6      | 验证码   |

**注意事项**: 密码(passcode) 的加密方式为 xxxxxx  

**需要调用到的其他接口**:  

| 接口名称   | 接口地址                               | 用途说明           |
| ---------- | -------------------------------------- | ------------------ |
| 获取验证码 | `{apiAddress}/api/common/getCheckCode` | 获取注册所需验证码 |

​    

### 6 返回示例  

```json
{
    "code": 200,  // 状态码
    "msg": "成功",  // 提示信息
    "data": null  // 返回内容
}
```

​    

### 7 备注  

更多返回错误码请查看首页返回状态码表  

[接口返回状态码表](URL/for/api/responseCode/table)  






