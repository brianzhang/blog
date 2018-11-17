title: SQL 行转列
author: brian
tags: []
categories: []
date: 2018-11-15 17:19:00
---
好久没有触碰SQL，最近遇到一个查询需求，对多行数据并归后，要进行分组显示

```sql
select 
   tb_member.relate_id as 'UID', 
   tb_currency_user.member_id as '用户ID',
	 max(case tb_currency_user.currency_id when 1 then tb_currency_user.num else 0 end) as 'ETH',
	 max(case tb_currency_user.currency_id when 25 then tb_currency_user.num else 0 end) as 'USDT',
	 max(case tb_currency_user.currency_id when 26 then tb_currency_user.num else 0 end) as 'BTC'
   max(case tb_currency_user.currency_id when 28 then tb_currency_user.num else 0 end) as 'WTC'
	
from tb_currency_user 
left join tb_member on tb_currency_user.member_id  =  tb_member.member_id
where tb_currency_user.num > 0 and tb_currency_user.currency_id in(26, 25, 1)
group by tb_currency_user.member_id;

```

> 结果

| UID | 用户ID | ETH | USDT | BTC | WTC |
| --- | ----- | --- | ---- | --- | --- |
| 001 | 1     | 0.001 | 0 | 0.0001 | 2 |
| 001 | 1     | 0.001 | 0 | 0.0001 | 2 |
| 001 | 1     | 0.001 | 0 | 0.0001 | 2 |
| 001 | 1     | 0.001 | 0 | 0.0001 | 2 |
| 001 | 1     | 0.001 | 0 | 0.0001 | 2 |