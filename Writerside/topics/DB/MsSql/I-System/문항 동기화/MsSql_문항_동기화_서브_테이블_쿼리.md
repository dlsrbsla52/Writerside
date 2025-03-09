# MsSql 문항 동기화 서브 테이블 쿼리

### 서브 테이블 동기화 쿼리 **I-System에서 구동할 것.**
```sql
DECLARE		@sync_serno				NUMERIC(18, 0)
DECLARE		@sync_mode				CHAR(1)

DECLARE		@BQD_idx				INT
DECLARE		@Question_id			BIGINT
DECLARE		@BQD_Gubun				INT
DECLARE		@BQD_Source				INT				= NULL
DECLARE		@BQD_Source_smallName	INT				= NULL
DECLARE		@BQD_TestYear			CHAR(4)			= NULL
DECLARE		@BQD_TestMonth			CHAR(2)			= NULL
DECLARE		@BQD_Kind				INT				= NULL
DECLARE		@BQD_Grade				INT				= NULL
DECLARE		@BQD_TextBook			VARCHAR(20)		= NULL
DECLARE		@BQD_Page				INT				= NULL
DECLARE		@BQD_MunGubun			INT				= NULL
DECLARE		@BQD_MunNumber			VARCHAR(50)		= NULL
DECLARE		@BQD_Danwon				VARCHAR(50)		= NULL
DECLARE		@BQD_EGjimun			VARCHAR(3)		= NULL
DECLARE		@BQD_GiBookName			VARCHAR(200)	= NULL
declare		@sync_result			varchar(10)		= null

	DECLARE	@target_date	VARCHAR(8)
	DECLARE	@target_folder	VARCHAR(6)
	DECLARE	@RESULT			CHAR(1)				= 'F'


		SET @target_date	= CONVERT(CHAR(8), GETDATE(), 112)
		SET @target_folder	= CONVERT(CHAR(6), GETDATE(), 112)
		SET @sync_result = 'F';

		DECLARE CUR_1 CURSOR FAST_FORWARD FOR

		select 
		[sync_serno], [iud_cd], [serno], [qst_id], [schrec_enote_div_cd], [schrec_src_id], [schrec_src_svpt_id], [setqstn_yy], [setqstn_mm], [schrec_frm_cd], [setqstn_grade_cd], [qustn_dtl_pubr_nm], [page_cnt], [qst_div_cd], [qst_seq_info], [unt_nm], [eng_text_div_cd], [etc_book_nm]
		from 
		tqq_question_detail_sync WITH (NOLOCK)
		ORDER BY [sync_serno] DESC

		OPEN CUR_1

		FETCH NEXT FROM CUR_1
		INTO	@sync_serno, @sync_mode, @BQD_idx, @Question_id, @BQD_Gubun, @BQD_Source, @BQD_Source_smallName, @BQD_TestYear, @BQD_TestMonth, @BQD_Kind, @BQD_Grade, @BQD_TextBook, @BQD_Page, @BQD_MunGubun, @BQD_MunNumber, @BQD_Danwon, @BQD_EGjimun, @BQD_GiBookName

		WHILE @@FETCH_STATUS=0
			BEGIN
				SET @sync_result = 'F';
				IF @sync_mode = 'I' BEGIN	
					IF not exists(select * from [I-SYSTEM].[dbo].tb_Bank_Question_Detail where BQD_idx = @BQD_idx) BEGIN
						SET IDENTITY_INSERT [I-SYSTEM].[dbo].tb_Bank_Question_Detail ON

						INSERT INTO [I-SYSTEM].[dbo].tb_Bank_Question_Detail(BQD_idx, Question_id, BQD_Gubun, BQD_Source, BQD_Source_smallName, BQD_TestYear, BQD_TestMonth, BQD_Kind, BQD_Grade, BQD_TextBook, BQD_Page, BQD_MunGubun, BQD_MunNumber, BQD_Danwon, BQD_EGjimun, BQD_GiBookName)
						VALUES(@BQD_idx, @Question_id, @BQD_Gubun, @BQD_Source, @BQD_Source_smallName, @BQD_TestYear, @BQD_TestMonth, @BQD_Kind, @BQD_Grade, @BQD_TextBook, @BQD_Page, @BQD_MunGubun, @BQD_MunNumber, @BQD_Danwon, @BQD_EGjimun, @BQD_GiBookName)
											

						SET IDENTITY_INSERT [I-SYSTEM].[dbo].tb_Bank_Question_Detail OFF						
					END
					ELSE BEGIN
						SET @sync_result = 'F(-8)';
					END
				END
				
				IF @sync_mode = 'U' BEGIN
					IF exists(select * from [I-SYSTEM].[dbo].tb_Bank_Question_Detail where BQD_idx = @BQD_idx) BEGIN
						UPDATE		[I-SYSTEM].[dbo].[tb_Bank_Question_Detail]
						SET			Question_id = @Question_id
									, BQD_Gubun = @BQD_Gubun
									, BQD_Source = @BQD_Source
									, BQD_Source_smallName = @BQD_Source_smallName
									, BQD_TestYear = @BQD_TestYear
									, BQD_TestMonth = @BQD_TestMonth
									, BQD_Kind = @BQD_Kind
									, BQD_Grade = @BQD_Grade
									, BQD_TextBook = @BQD_TextBook
									, BQD_Page = @BQD_Page
									, BQD_MunGubun = @BQD_MunGubun
									, BQD_MunNumber = @BQD_MunNumber
									, BQD_Danwon = @BQD_Danwon
									, BQD_EGjimun = @BQD_EGjimun
									, BQD_GiBookName = @BQD_GiBookName
						WHERE		BQD_idx = @BQD_idx

					END
					ELSE BEGIN
						SET @sync_result = 'F(-7)';
						
					END
				END
				
				IF @sync_mode = 'D' BEGIN
					IF exists(select * from [I-SYSTEM].[dbo].tb_Bank_Question_Detail where BQD_idx = @BQD_idx) BEGIN
						DELETE FROM [I-SYSTEM].[dbo].[tb_Bank_Question_Detail]
						WHERE		BQD_idx = @BQD_idx


					END
					ELSE BEGIN
						SET @sync_result = 'F(-4)';
					END					
					
				END


				FETCH NEXT FROM CUR_1
				INTO	@sync_serno, @sync_mode, @BQD_idx, @Question_id, @BQD_Gubun, @BQD_Source, @BQD_Source_smallName, @BQD_TestYear, @BQD_TestMonth, @BQD_Kind, @BQD_Grade, @BQD_TextBook, @BQD_Page, @BQD_MunGubun, @BQD_MunNumber, @BQD_Danwon, @BQD_EGjimun, @BQD_GiBookName
			END

		CLOSE CUR_1
		DEALLOCATE CUR_1
```
