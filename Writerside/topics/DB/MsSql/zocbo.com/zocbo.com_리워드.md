# 공유하기 리워드 지급 회원수

```sql
SELECT 
	convert(varchar(10), REGDATE, 120), 
	case
		when b.mem_Group = 'S' then '학생'
		when b.mem_Group = 'P' then '학부모'
		when b.mem_Group = 'T' then '선생님'
	end, 
	count(*) 
FROM zocbolog.dbo.TB_SHARE_LOG as a with(nolock)
inner join tb_member as b with(nolock) on a.USER_ID = b.User_ID
where 
	b.USER_ID not in (select USer_ID from tb_zocbo_staff with(nolock))
	AND b.mem_Group IN ('S', 'P', 'T')
	and a.REGDATE >= '2024-09-10 17:50:00'
group by convert(varchar(10), REGDATE, 120), b.mem_Group
order by convert(varchar(10), REGDATE, 120),
	case
		when b.mem_Group = 'S' then 1
		when b.mem_Group = 'P' then 2
		when b.mem_Group = 'T' then 3
		else 4
	end
```

[[query]] #쿼리