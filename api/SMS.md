## 请求短信验证码接口

?> <span class="label label-info">POST</span> `http://192.168.108.243:8000/api/gateway/SMS/request`

### 请求参数

<div class="request-data">

| 参数名        |   类型   | 必填 | 说明                   |
| ------------- | :------: | :--: | ---------------------- |
| data          | `object` |  ✅  |
| data >> phone | `string` |  ✅  | 手机号码               |
| data >> op    | `stirng` |  ✅  | 服务名称，如`注册验证` |

</div>

### 返回参数

<div class="request-data">

| 参数名  |   类型    | 必含 | 说明               |
| ------- | :-------: | :--: | ------------------ |
| code    | `object`  |  ✅  | 请求成功`200`      |
| data    | `object`  |  ✅  | 手机验证码发送成功 |
| message | `string`  |  ✅  |                    |
| success | `boolean` |  ✅  |                    |

</div>

---

## 验证短信验证码接口

?> <span class="label label-info">POST</span> `http://192.168.108.243:8000/api/gateway/SMS/verify`

<div class="request-data">

| 参数名        |   类型   | 必填 | 说明       |
| ------------- | :------: | :--: | ---------- |
| data          | `object` |  ✅  |
| data >> phone | `string` |  ✅  | 手机号码   |
| data >> code  | `stirng` |  ✅  | 短信验证码 |

</div>

### 返回参数

<div class="request-data">

| 参数名  |   类型    | 必含 | 说明               |
| ------- | :-------: | :--: | ------------------ |
| code    | `object`  |  ✅  | 请求成功`200`      |
| data    | `object`  |  ✅  | 手机验证码验证成功 |
| message | `string`  |  ✅  |                    |
| success | `boolean` |  ✅  |                    |

</div>
