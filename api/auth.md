## 登录接口

?> <span class="label label-info">POST</span> `http://192.168.108.243:8000/api/gateway/auth`

### 请求参数

<div class="request-data">

| 参数名           |   类型   | 必填 | 说明     |
| ---------------- | :------: | :--: | -------- |
| data             | `object` |  ✅  |
| data >> username | `string` |  ✅  | 手机号码 |
| data >> password | `string` |  ✅  | 密码     |

</div>

### 返回参数

<div class="request-data">

| 参数名  |   类型    | 必含 | 说明          |
| ------- | :-------: | :--: | ------------- |
| code    | `object`  |  ✅  | 请求成功`200` |
| data    | `object`  |  ✅  | JWT 密钥      |
| message | `string`  |  ✅  |
| success | `boolean` |  ✅  |

</div>

---

## 注册接口

?> <span class="label label-info">POST</span> `http://192.168.108.243:8000/api/gateway/register`

### 请求参数

<div class="request-data">

| 参数名               |   类型   | 必填 | 说明       |
| -------------------- | :------: | :--: | ---------- |
| data                 | `object` |  ✅  |            |
| data >> username     | `string` |      | 用户名     |
| data >> phone        | `string` |      | 手机号码   |
| data >> code         | `string` |      | 手机验证码 |
| data >> new_password | `string` |  ✅  | 密码       |

</div>

> `username`和`phone`、`code`为 2 选一必填项，`username`为空时，`phone`不可为空，`phone`与`code`需同时存在

### 返回参数

<div class="request-data">

| 参数名  |   类型    | 必含 | 说明                               |
| ------- | :-------: | :--: | ---------------------------------- |
| code    | `object`  |  ✅  | 请求成功`200`                      |
| data    | `object`  |  ✅  | 账号注册成功，请切换到登录页面登录 |
| message | `string`  |  ✅  |                                    |
| success | `boolean` |  ✅  |                                    |

</div>

---

## 找回密码

?> <span class="label label-info">POST</span> `http://192.168.108.243:8000/api/gateway/password/find`

### 请求参数

<div class="request-data">

| 参数名               |   类型   | 必填 | 说明       |
| -------------------- | :------: | :--: | ---------- |
| data                 | `object` |  ✅  |
| data >> phone        | `string` |  ✅  | 手机号码   |
| data >> code         | `string` |  ✅  | 手机验证码 |
| data >> new_password | `string` |  ✅  | 密码       |

</div>

### 返回参数

<div class="request-data">

| 参数名  |   类型    | 必含 | 说明                         |
| ------- | :-------: | :--: | ---------------------------- |
| code    | `object`  |  ✅  | 请求成功`200`                |
| data    | `object`  |  ✅  | 找回密码成功，使用新密码登录 |
| message | `string`  |  ✅  |                              |
| success | `boolean` |  ✅  |                              |

</div>

---
