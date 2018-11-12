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
| | ✅ | address | 联系地址 | varchar | 20 |
| | ✅ | id_card_picture | 申请人身份证扫描件 | text | 0 |
| | ✅ | name | 姓名 | varchar | 100 |
| | ✅ | region_code | 行政区域编码 | varchar | 20 |

</div>