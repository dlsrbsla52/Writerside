# TMS 문항 동기화 처리 쿼리

#### MsSql에 동기화 처리 후 tqq_question_sync update처리
```SQL
UPDATE tms.tqq_question
SET sync_yn = 'Y'
WHERE qst_id IN (

				SELECT qst_id FROM tms.tqq_question_sync
                WHERE work_stus_cd in ('10', '20', '40', '41', '42')
                  and sync_occur_dtm > '2024-05-01'
                  and sync_occur_dtm < '2024-09-05'
);
```


#### MsSql에 동기화 처리 후 tqq_question_detail_sync update처리
```SQL
UPDATE tms.tqq_question_detail_sync
SET work_stus_cd = '35'
WHERE sync_serno IN (
				SELECT sync_serno FROM tms.tqq_question_detail_sync
				WHERE work_stus_cd in ('10', '20', '40', '41', '42')
				and sync_occur_dtm > '2024-05-01'
                and sync_occur_dtm < '2024-09-05'
);
```


#### MsSql에 동기화 처리 후 tms.tqq_question_sync update처리
```SQL
UPDATE tms.tqq_question_sync
SET work_stus_cd = '35'
WHERE sync_serno IN (
				SELECT sync_serno FROM tms.tqq_question_sync
				WHERE work_stus_cd in ('10', '20', '40', '41', '42')
				and sync_occur_dtm > '2024-05-01'
                and sync_occur_dtm < '2024-09-05'
);
```


#### MsSql에 동기화 처리 후 tms.tqq_textbook_question_conn_sync update처리
```SQL
UPDATE tms.tqq_textbook_question_conn_sync
SET work_stus_cd = '35'
WHERE sync_serno IN (
				SELECT sync_serno FROM tms.tqq_textbook_question_conn_sync
				WHERE work_stus_cd in ('10', '20', '40', '41', '42')
				and sync_occur_dtm > '2024-05-01'
                and sync_occur_dtm < '2024-09-05'
);
```


#### MsSql에 동기화 처리 후 tms.tqq_su_krname_question_sync update처리
```SQL
UPDATE tms.tqq_su_krname_question_sync
SET work_stus_cd = '35'
WHERE sync_serno IN (
				SELECT sync_serno FROM tms.tqq_su_krname_question_sync
				WHERE work_stus_cd in ('10', '20', '40', '41', '42')
                and sync_occur_dtm > '2024-05-01'
                and sync_occur_dtm < '2024-09-05'
);
```


#### MsSql에 동기화 처리 후 tms.tqq_su_question_detail_sync update처리
```SQL
UPDATE tms.tqq_su_question_detail_sync
SET work_stus_cd = '35'
WHERE sync_serno IN (
				SELECT sync_serno FROM tms.tqq_su_question_detail_sync
				WHERE work_stus_cd in ('10', '20', '40', '41', '42')
				and sync_occur_dtm > '2024-05-01'
                and sync_occur_dtm < '2024-09-05'
);
```

