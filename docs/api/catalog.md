## 新增产品
?> 接口地址：`http://192.168.108.243:8000/api/product/catalog`

?> 请求方式：`POST`
 
!> 是否登录：`TRUE`
 
!> 是否授权：`TRUE`

### 请求参数
<div class="request-data">

| 参数名 | 类型 | 必填 | 说明 |
| ------ | :------: | :------: | ------ |
| data | `object` | ✅ |
| data >> brandId | `string` | ✅ | 品牌ID |
| data >> name | `string` | ✅ | 产品名称 |
| data >> code | `string` | ✅ | 产品编码 |
| data >> type | `string` | ✅ | [产品类型](/api/catalog?id=产品类型) |
| data >> logo | `string` | ✅ | 产品logo地址，通过文件上传接口获取 |
| data >> pictures | `stringArray` | ✅ | 产品图册地址数组，通过文件上传接口获取 |

</div>

#### 产品类型
```java
public enum CatalogType {
    /**
     * 电动车成车
     */
    Electrombile,
    /**
     * 配件
     */
    Parts,
    /**
     * 用品
     */
    Articles
}
```

### 返回参数
<div class="request-data">

| 参数名 | 类型 | 必含 | 说明 |
| ------ | :------: | :------: | ------ |
| code | `object` | ✅ | 请求成功`200` |
| data | `object` | ✅ | 详见数据库（[产品](/database/agc_catalog ':target=_blank')）|
| message | `string` | ✅ |
| success | `boolean` | ✅ |

</div>

---

## 修改产品信息
?> 接口地址：`http://192.168.108.243:8000/api/product/catalog/{catalogId}`

?> 请求方式：`PUT`
 
!> 是否登录：`TRUE`
 
!> 是否授权：`TRUE`

### 请求参数
<div class="request-data">

| 参数名 | 类型 | 必填 | 说明 |
| ------ | :------: | :------: | ------ |
| catalogId | `string` | ✅ | 产品ID |
| data | `object` | ✅ |
| data >> name | `string` | ✅ | 产品名称 |
| data >> code | `string` | ✅ | 产品编码 |
| data >> type | `string` | ✅ | [产品类型](/api/catalog?id=产品类型) |
| data >> logo | `string` | ✅ | 产品logo地址，通过文件上传接口获取 |
| data >> pictures | `stringArray` | ✅ | 产品图册地址数组，通过文件上传接口获取 |

</div>

### 返回参数
<div class="request-data">

| 参数名 | 类型 | 必含 | 说明 |
| ------ | :------: | :------: | ------ |
| code | `object` | ✅ | 请求成功`200` |
| data | `object` | ✅ | 详见数据库（[产品](/database/agc_catalog ':target=_blank')）|
| message | `string` | ✅ |
| success | `boolean` | ✅ |

</div>

---

## 删除产品
?> 接口地址：`http://192.168.108.243:8000/api/product/catalog/{catalogId}`

?> 请求方式：`DELETE`
 
!> 是否登录：`TRUE`
 
!> 是否授权：`TRUE`

### 请求参数
<div class="request-data">

| 参数名 | 类型 | 必填 | 说明 |
| ------ | :------: | :------: | ------ |
| catalogId | `string` | ✅ | 产品ID |

</div>

### 返回参数
<div class="request-data">

| 参数名 | 类型 | 必含 | 说明 |
| ------ | :------: | :------: | ------ |
| code | `object` | ✅ | 请求成功`200` |
| message | `string` | ✅ |
| success | `boolean` | ✅ |

</div>

---

## 查看产品信息
?> 接口地址：`http://192.168.108.243:8000/api/product/catalog/{catalogId}`

?> 请求方式：`GET`

### 请求参数
<div class="request-data">

| 参数名 | 类型 | 必填 | 说明 |
| ------ | :------: | :------: | ------ |
| catalogId | `string` | ✅ | 产品ID |

</div>

### 返回参数
<div class="request-data">

| 参数名 | 类型 | 必含 | 说明 |
| ------ | :------: | :------: | ------ |
| code | `object` | ✅ | 请求成功`200` |
| data | `object` | ✅ | 详见数据库（[产品](/database/agc_catalog ':target=_blank')）|
| message | `string` | ✅ |
| success | `boolean` | ✅ |

</div>

---

## 分页查询产品
?> 接口地址：`http://192.168.108.243:8000/api/product/catalog/`

?> 请求方式：`GET`

### 请求参数
<div class="request-data">

| 参数名 | 类型 | 必填 | 说明 |
| ------ | :------: | :------: | ------ |
| name | `string` | | 产品名称 |
| code | `string` | | 产品编码 |
| type | `string` | | [产品类型](/api/catalog?id=产品类型) |
| page | `int` | | 页码，下标从0开始
| size | `int` | | 每页大小

</div>

### 返回参数
<div class="request-data">

| 参数名 | 类型 | 必含 | 说明 |
| ------ | :------: | :------: | ------ |
| code | `object` | ✅ | 请求成功`200` |
| data | `object` | ✅ |
| data >> content | `array` | ✅ | 详见数据库（[产品](/database/agc_catalog ':target=_blank')） |
| data >> pageNumber | `int` | ✅ | 查询页码 |
| data >> pageSize | `int` | ✅ | 分页大小 |
| data >> totalPage | `int` | ✅ | 总页码 |
| data >> totalRows | `int` | ✅ | 总记录条数 |
| data >> hasNext | `boolean` | ✅ | 是否还有下一页 |
| message | `string` | ✅ |
| success | `boolean` | ✅ |

</div>

---