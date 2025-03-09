# MsSql 동기화 체크 쿼리

```sql
SELECT * FROM [I-SYSTEM].[dbo].[TMS_Su_KRname_Question_LOG] WHERE CHARINDEX('_', LOG_RESULT, 0) = 0 ORDER BY 1 DESC
SELECT * FROM [I-SYSTEM].[dbo].[TMS_Bank_Question_Detail_LOG] WHERE CHARINDEX('_', LOG_RESULT, 0) = 0 ORDER BY 1 DESC
SELECT * FROM [I-SYSTEM].[dbo].[TMS_Publish_Question_LOG] WHERE CHARINDEX('_', LOG_RESULT, 0) = 0 ORDER BY 1 DESC
SELECT * FROM [I-SYSTEM].[dbo].[TMS_Su_Bank_Question_Detail_LOG] WHERE CHARINDEX('_', LOG_RESULT, 0) = 0 ORDER BY 1 DESC
SELECT * FROM [I-SYSTEM].[dbo].[TMS_Bank_Question_LOG] WHERE CHARINDEX('_', LOG_RESULT, 0) = 0 ORDER BY 1 DESC


--모든 수동 작업 후 완료된 상태라면 템프테이블은 비워줘야된다.
TRUNCATE TABLE [TMS_Su_KRname_Question_LOG];
TRUNCATE TABLE [TMS_Bank_Question_Detail_LOG];
TRUNCATE TABLE [TMS_Publish_Question_LOG];
TRUNCATE TABLE [TMS_Su_Bank_Question_Detail_LOG];
TRUNCATE TABLE [TMS_Bank_Question_LOG];



[dbo].[TMS_Su_KRname_Question_LOG]
[dbo].[TMS_Bank_Question_Detail_LOG]
[dbo].[TMS_Publish_Question_LOG]
[dbo].[TMS_Su_Bank_Question_Detail_LOG]
[dbo].[TMS_Bank_Question_LOG]


DELETE A
FROM (
			SELECT ROW_NUMBER() OVER (PARTITION BY LOG_SEQ ORDER BY LOG_SEQ) AS NUM, *
			FROM [TMS_Bank_Question_LOG]
      ) A
WHERE num >= 2
```

