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
| | ✅ | daily_of_passenger_flow | 日常客流量 | bigint | 20 |  |
| | ✅ | address | 门店位置 | varchar | 255 |  |
| | ✅ | business_license_picture | 营业执照扫描件 | text | 0 |  |
| | ✅ | has_reform_experience | 改装经验 | char | 1 | '0' |
| | ✅ | month_of_modify | 月改装量 | bigint | 20 |  |
| | ✅ | name | 门店名称 | varchar | 255 |  |
| | ✅ | region_code | 门店区域编码 | varchar | 255 |  |
| | | store_picture | 店铺门头 | text | 0 |  |
| | ✅ | uniform_social_credit_code | 统一社会信用码 | varchar | 30 |  |
| | ✅ | apply_id | [申请表ID](/database/agc_developer_apply) | bigint | 20 | 

</div>