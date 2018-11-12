## 开发者申请

?> 接口地址：`http://192.168.108.243:8000/api/developer/apply`

?> 请求方式：`POST`

!> 是否登录：`TRUE`

!> 是否授权：`FASLE`

### 请求参数

<div class="request-data">

| 参数名                                |   类型   | 必填 | 说明                                           |
| ------------------------------------- | :------: | :--: | ---------------------------------------------- |
| data                                  | `object` |  ✅  |
| data>>applyType                       | `string` |  ✅  | [申请类型](/api/developer?id=申请类型)         |
| data>>source                          | `string` |  ✅  | [申请来源](/api/developer?id=申请来源)         |
| data>>contactName                     | `string` |  ✅  | 联系人姓名                                     |
| data>>contactNumber                   | `string` |  ✅  | 联系人电话                                     |
| data>>contactRegionCode               | `string` |  ✅  | 联系地址行政区域编码                           |
| data>>contactAddress                  | `string` |  ✅  | 联系地址                                       |
| data>>idCardNumber                    | `string` |  ✅  | 申请人身份证号码                               |
| data>>idCardPicture                   | `string` |  ✅  | 申请人身份证扫描件                             |
| data>>extras                          | `object` |      | 申请类型的补充信息                             |
| ∨ 企业申请补充字段 ∨                  |
| data>>extras>>name                    | `string` |  ✅  | 企业名称                                       |
| data>>extras>>aliasName               | `string` |  ✅  | 企业简称                                       |
| data>>extras>>companySigns            | `string` |  ✅  | 企业标识                                       |
| data>>extras>>scale                   | `string` |  ✅  | [企业规模](/api/developer?id=企业规模)         |
| data>>extras>>enterpriseDescription   | `string` |  ✅  | 企业描述                                       |
| data>>extras>>annualTurnover          | `string` |  ✅  | 年营业额                                       |
| data>>extras>>uniformSocialCreditCode | `string` |  ✅  | 统一社会信用码                                 |
| data>>extras>>businessLicensePicture  | `string` |  ✅  | 营业执照扫描件                                 |
| data>>extras>>cloud                   | `string` |  ✅  | [云平台](/api/developer?id=云平台)             |
| data>>extras>>softwareDevelopmentTeam | `string` |  ✅  | [软件研发团队](/api/developer?id=软件研发团队) |
| ∨ 店铺申请补充字段 ∨                  |
| data>>extras>>                        |

</div>

### 返回参数

<div class="request-data">

| 参数名                |   类型    | 必含 | 说明                                                                                                    |
| --------------------- | :-------: | :--: | ------------------------------------------------------------------------------------------------------- |
| code                  | `object`  |  ✅  | 请求成功`200`                                                                                           |
| data                  | `object`  |  ✅  | 详见数据库（[开发者资格申请表](/database/agc_developer_apply ':target=_blank')）                        |
| data>>personApply     | `object`  |  ✅  | 详见数据库（[开发者资格申请表（个人信息）](/database/agc_developer_person_apply ':target=_blank')）     |
| data>>enterpriseApply |  `array`  |  ✅  | 详见数据库（[开发者资格申请表（企业信息）](/database/agc_developer_enterprise_apply ':target=_blank')） |
| data>>storeApply      |  `array`  |  ✅  | 详见数据库（[开发者资格申请表（店铺信息）](/database/agc_developer_store_apply ':target=_blank')）      |
| message               | `string`  |  ✅  |
| success               | `boolean` |  ✅  |

</div>

### 返回示例

```json
{
	"code": 200,
	"data": {
		"createdTime": "2018-11-12 15:14:45",
		"deletedFlag": false,
		"enterpriseApply": [
			{
				"aliasName": "安马达",
				"annualTurnover": "1000万",
				"businessLicensePicture": "https://agc-bucket.oss-cn-hangzhou.aliyuncs.com//agcloud-20181106-c715f682424a42fb9191de034929fcd0.png",
				"cloud": "cloudOwner",
				"companySigns": "AMD",
				"createdTime": "2018-11-12 15:14:45",
				"deletedFlag": false,
				"enterpriseDescription": "这是一个企业描述",
				"hasSmartProductExperience": false,
				"id": "93390826425176064",
				"modifiedTime": "2018-11-12 15:14:45",
				"name": "台州安马达电动车有限公司",
				"scale": "medium",
				"softwareDevelopmentTeam": "companyTeam",
				"uniformSocialCreditCode": "91331002693627972Q",
				"version": "0"
			}
		],
		"id": "93390826374844416",
		"modifiedTime": "2018-11-12 15:14:45",
		"personApply": {
			"address": "东环路32-7号",
			"contactNumber": "13858697251",
			"createdTime": "2018-11-12 15:14:45",
			"deletedFlag": false,
			"id": "93390826316124160",
			"idCardNumber": "331081199011158011",
			"idCardPicture": "https://agc-bucket.oss-cn-hangzhou.aliyuncs.com//agcloud-20181106-c715f682424a42fb9191de034929fcd0.png",
			"modifiedTime": "2018-11-12 15:14:45",
			"name": "杨玲辉",
			"regionCode": "331081102",
			"version": "0"
		},
		"source": "searchEngines",
		"storeApply": [],
		"type": "enterprise",
		"version": "1"
	},
	"message": "请求成功",
	"success": true
}
```

---

## 枚举类型

### 申请类型

```java
public enum ApplyTypeEnums {
    /**
     * 企业
     */
    enterprise,
    /**
     * 门店
     */
    store,
    /**
     * 个人
     */
    person
}
```

### 申请来源

```java
public enum ApplySourceEnums {
    /**
     * 周围人推荐
     */
    recommendedByPeopleAround,
    /**
     * 已建立合作关系
     */
    cooperativeRelationshipHasBeenEstablished,
    /**
     * 新闻报道
     */
    newsReports,
    /**
     * 搜索引擎
     */
    searchEngines,
    /**
     * 会议会展
     */
    conventionAndExhibition,
    /**
     * 其他
     */
    other
}
```

### 云平台

```java
public enum CloudEnums {
    /**
     * 拥有自己的云平台
     */
    cloudOwner,
    /**
     * 租用第三方的云平台
     */
    thirdCloud,
    /**
     * 无云平台
     */
    none
}
```

### 企业规模

```java
public enum EnterpriseScaleEnums {
    /**
     * 微型企业（1-20人）
     */
    micro,
    /**
     * 小型企业（20-300人）
     */
    small,
    /**
     * 中型企业（300-1000人）
     */
    medium,
    /**
     * 大型企业（1000人以上）
     */
    large
}
```

### 软件研发团队

```java
public enum SoftwareDevelopmentTeamEnums {
    /**
     * 公司团队
     */
    companyTeam,
    /**
     * 外包团队
     */
    outsourcingTeam,
    /**
     * 无团队
     */
    none
}
```
