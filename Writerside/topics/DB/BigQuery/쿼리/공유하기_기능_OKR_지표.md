# 공유하기 기능 OKR 지표

### 공유하기 참여수
```sql
SELECT  
    MEM_GROUP, count(*)  
FROM  
    `zocbodb.data.TB_TRACKING_LOG_202409`  
WHERE  
    EVENT_ACTION_TARGET_CD = 'OW3WUAV8UVPFZPFCHGNSJIMY720240905'  
  and reg_dt_Ymd = '20240912'  
group by MEM_GROUP;
```

### 공유하기 링크 진입수
``` sql
SELECT  
    count(*)  
FROM  
    `zocbodb.data.TB_TRACKING_LOG_202409`  
where  
    (SHARE_PLATFORM not in ('') and SHARE_PLATFORM is not null )  
  and reg_dt_Ymd = '20240911';
```
