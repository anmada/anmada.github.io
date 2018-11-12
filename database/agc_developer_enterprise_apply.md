<div class="database-data">

| 主键 | 必填 | 字段名 | 描述 | 类型 | 长度 | 默认值 |
| :------: | :------: | ------ | ------ | :------ | :------: | ------ |
| ✅ | ✅ | id | 主键ID | bigint | 20 |
| | | business_type | 业务类型 | varchar | 20 | NULL |
| | | create_source | 执行创建操作的账号或平台 | varchar | 20 | NULL |
| | | created_time | 创建时间 | timestamp |
| | ✅ | deleted_flag | 逻辑删除标志 | char | 1 | '0' |
| | | description | 描述 | varchar | 255 | NULL |
| | | modified_time | 修改时间 | timestamp |
| | | modify_source | 执行修改操作的账号或平台 | varchar | 20 | NULL |
| | | owner | 业务所有者 | varchar | 20 | NULL |
| | | retire_source | 执行作废操作的账号或平台 | varchar | 20 | NULL |
| | | retired_time | 作废时间 | timestamp | 0 | NULL |
| | | state | 状态 | varchar | 20 | NULL |
| | ✅ | version | 数据版本 | bigint | 20 | '0' |
| | ✅ | enterprise_description | 企业描述 | text | 0 |
| | ✅ | alias_name | 企业简称 | varchar | 50 |
| | ✅ | annual_turnover | 年营业额 | varchar | 30 |
| | ✅ | business_license_picture | 营业执照扫描件 | text | 0 |
| | ✅ | cloud | 云平台情况 | varchar | 20 |
| | ✅ | company_signs | 企业标识 | varchar | 6 |
| | ✅ | has_smart_product_experience | 智能产品经验 | char | 1 | '0' |
| | ✅ | name | 企业全称 | varchar | 255 |
| | | official_website | 企业官方网站 | varchar | 255 | NULL |
| | ✅ | scale | 员工规模 | varchar | 20 |
| | ✅ | software_development_team | 软件开发团队 | varchar | 20 |
| | ✅ | uniform_social_credit_code | 统一社会信用码 | varchar | 30 |
| | ✅ | apply_id | [申请表ID](/database/agc_developer_apply) | bigint | 20 |

</div>