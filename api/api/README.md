## 请求约束

接口考虑安全机制，设置了授权加密算法。需在每次 http 请求中的 Headers 携带以下信息

<div class="request-data">

| 参数名        |   类型   | 必填 | 说明                                   |
| ------------- | :------: | :--: | -------------------------------------- |
| appId         | `string` |  ✅  | AppId,由平台分配                       |
| timestamp     | `string` |  ✅  | 当前请求时间戳                         |
| nonce         | `string` |  ✅  | 随机字符串                             |
| signature     | `string` |  ✅  | 签名                                   |
| Authorization | `string` |      | jwt 登录凭证，需要登录权限的接口中需要 |

</div>

---

## 签名算法

### 签名算法（JAVA 版本）

```java
String[] arr = new String[]{ appId, timestamp, nonce };
Arrays.sort(arr);
// 拼成字符串
String      content   = StrUtil.join("",arr);;
String      signature = SecureUtil.sha1(content);
```

### 签名算法（JavaScript 版本）
```javascript
let signArray = [headers.appId, headers.timestamp, headers.nonce];
signArray.sort();
const crypto = require('crypto');
const sha1 = crypto.createHash('sha1');
const signature = sha1.update(signArray.join('')).digest('hex');
```