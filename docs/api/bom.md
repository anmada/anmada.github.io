## 新增物料清单
?> 接口地址：`http://192.168.108.243:8000/api/product/bom`

?> 请求方式：`POST`
 
!> 是否登录：`TRUE`
 
!> 是否授权：`TRUE`

### 请求参数
<div class="request-data">

| 参数名 | 类型 | 必填 | 说明 |
| ------ | :------: | :------: | ------ |
| data | `object` | ✅ |
| data >> catalogId | `string` | ✅ | 产品ID |
| data >> code | `string` | ✅ | 物料清单编码 |

</div>

### 返回参数
<div class="request-data">

| 参数名 | 类型 | 必含 | 说明 |
| ------ | :------: | :------: | ------ |
| code | `object` | ✅ | 请求成功`200` |
| data | `object` | ✅ | 详见数据库（[物料清单](/database/agc_bill_of_material ':target=_blank')）|
| data >> catalog | `object` | ✅ | 详见数据库（[产品](/database/agc_catalog ':target=_blank')）|
| data >> catalogBoms | `array` | ✅ | 详见数据库（[物料清单明细](/database/agc_catalog_bom ':target=_blank')）|
| message | `string` | ✅ |
| success | `boolean` | ✅ |

</div>

---

## 修改物料清单明细
?> 接口地址：`http://192.168.108.243:8000/api/product/bom/{bomId}`

?> 请求方式：`PUT`

!> 是否登录：`TRUE`

!> 是否授权：`TRUE`

### 请求参数
<div class="request-data">

| 参数名 | 类型 | 必填 | 说明 |
| ------ | :------: | :------: | ------ |
| bomId | `string` | ✅ | 物料清单ID |
| data | `object` | ✅ |
| data >> parts | `array` | ✅ | 物料清单明细 |
| data >> parts >> id | `string` | | 物料清单明细ID，当为修改该明细时保留id信息，无id表示新增 |
| data >> parts >> catalogId | `string` | | 产品ID，可为空，如id字段为空是，该字段可能为空 |
| data >> parts >> quantity | `int` | ✅ | 产品数量，不可为空，且大于0 |

</div>

### 返回参数
<div class="request-data">

| 参数名 | 类型 | 必含 | 说明 |
| ------ | :------: | :------: | ------ |
| code | `object` | ✅ | 请求成功`200`
| data | `object` | ✅ | 详见数据库（[物料清单](/database/agc_bill_of_material ':target=_blank')）
| data >> catalog | `object` | ✅ | 详见数据库（[产品](/database/agc_catalog ':target=_blank')）|
| data >> catalogBoms | `array` | ✅ | 详见数据库（[物料清单明细](/database/agc_catalog_bom ':target=_blank')）|s
| message | `string` | ✅ |
| success | `boolean` | ✅ |

</div>

## 发布物料清单版本
?> 接口地址：`http://192.168.108.243:8000/api/product/bom/{bomId}/release`

?> 请求方式：`POST`

!> 是否登录：`TRUE`

!> 是否授权：`TRUE`

### 请求参数
<div class="request-data">

| 参数名 | 类型 | 必填 | 说明 |
| ------ | :------: | :------: | ------ |
| bomId | `string` | ✅ | 物料清单ID |

</div>

### 返回参数
<div class="request-data">

| 参数名 | 类型 | 必含 | 说明 |
| ------ | :------: | :------: | ------ |
| code | `object` | ✅ | 请求成功`200`
| data | `object` | ✅ | 详见数据库（[物料清单](/database/agc_bill_of_material ':target=_blank')）
| data >> catalog | `object` | ✅ | 详见数据库（[产品](/database/agc_catalog ':target=_blank')）|
| data >> catalogBoms | `array` | ✅ | 详见数据库（[物料清单明细](/database/agc_catalog_bom ':target=_blank')）|s
| message | `string` | ✅ |
| success | `boolean` | ✅ |

</div>