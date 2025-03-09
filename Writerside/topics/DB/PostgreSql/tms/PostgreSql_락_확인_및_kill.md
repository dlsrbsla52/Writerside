# PostgreSql 락 확인 및 kill

**테이블별 락 확인**
```sql
select t.relname,
       l.locktype,
       page,
       virtualtransaction,
       pid,
       mode,
       granted
from pg_locks l,
     pg_stat_all_tables t
where l.relation = t.relid
order by relation asc;
```


**락 kill**
```sql
select pg_terminate_backend(pid) from pg_stat_activity
```


