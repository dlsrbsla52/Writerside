# TMS 문항 동기화 확인 쿼리

## tqq_question_sync에 날짜별로 동기화 실패, 미처리 데이터 있는지 확인
```SQL
SELECT count(*)
FROM tms.tqq_question_sync
WHERE work_stus_cd in ('10', '20', '40', '41', '42')
  and sync_occur_dtm > '2024-05-01'
  and sync_occur_dtm < '2024-09-05'
;
```


## tqq_question_sync에 날짜별로 동기화 실패, 미처리 데이터 있는지 확인_1
```SQL
SELECT *
FROM tms.tqq_question_sync
WHERE work_stus_cd in ('10', '20', '40', '41', '42')
  and sync_occur_dtm > '2024-05-01'
  and sync_occur_dtm < '2024-09-05'
;
```


## tqq_question_detail_sync 날짜별로 동기화 실패, 미처리 데이터 있는지 확인
```SQL
select *
from tms.tqq_question_detail_sync
WHERE work_stus_cd in ('10', '20', '40', '41', '42')
  and sync_occur_dtm > '2024-05-01'
  and sync_occur_dtm < '2024-09-05'
;
```


## tqq_textbook_question_conn_sync 날짜별로 동기화 실패, 미처리 데이터 있는지 확인
```SQL
select *
from tms.tqq_textbook_question_conn_sync
WHERE work_stus_cd in ('10', '20', '40', '41', '42')
  and sync_occur_dtm > '2024-05-01'
  and sync_occur_dtm < '2024-09-05'
;
```

## tqq_su_krname_question_sync 날짜별로 동기화 실패, 미처리 데이터 있는지 확인
```SQL
select *
from tms.tqq_su_krname_question_sync
WHERE work_stus_cd in ('10', '20', '40', '41', '42')
  and sync_occur_dtm > '2024-05-01'
  and sync_occur_dtm < '2024-09-05'
;
```

## tqq_su_question_detail_sync 날짜별로 동기화 실패, 미처리 데이터 있는지 확인
```SQL
select *
from tms.tqq_su_question_detail_sync
WHERE work_stus_cd in ('10', '20', '40', '41', '42')
  and sync_occur_dtm > '2024-05-01'
  and sync_occur_dtm < '2024-09-05'
;
```