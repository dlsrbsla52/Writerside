# MsSql 문항 동기화 메인 테이블 쿼리

### 메인테이블 동기화 쿼리 **I-System에서 구동할 것.**
```sql

DECLARE		@SYNC_SERNO				NUMERIC(18, 0)
DECLARE		@SYNC_MODE				CHAR(1)
DECLARE		@idx					INT
DECLARE		@Q_Cnt					INT				= NULL
DECLARE		@Question_id			BIGINT
DECLARE		@Passage_id				BIGINT			= NULL
DECLARE		@siteid					CHAR(1)			= NULL
DECLARE		@TestYear				CHAR(4)
DECLARE		@Term					CHAR(1)
DECLARE		@MidEnd					CHAR(1)
DECLARE		@grade_id				INT
DECLARE		@course_id				INT
DECLARE		@textbook_id			INT
DECLARE		@rootpart_id			INT
DECLARE		@subpart_id				INT				= NULL
DECLARE		@detailpart_id			INT				= NULL
DECLARE		@type_id				INT				= NULL
DECLARE		@kind_id				INT				= NULL
DECLARE		@difficulty_id			INT				= NULL
DECLARE		@scl_name				VARCHAR(255)	= NULL
DECLARE		@scl_id					INT				= NULL
DECLARE		@rg_id					INT				= NULL
DECLARE		@Regdate				DATETIME
DECLARE		@topic_id				INT				= NULL
DECLARE		@Team					VARCHAR(50)		= NULL
DECLARE		@Academy				VARCHAR(50)		= NULL
DECLARE		@ChapKind_ID			INT				= NULL
DECLARE		@IsExpectedQuestion		CHAR(1)			= NULL
DECLARE		@Question_Text			VARCHAR(max)	= NULL
DECLARE		@CodeNumber				BIGINT			= NULL
DECLARE		@Q_DateLastModified		FLOAT			= NULL
DECLARE		@A_DateLastModified		FLOAT			= NULL
DECLARE		@P_DateLastModified		FLOAT			= NULL
DECLARE		@HasCommentary			CHAR(1)			= NULL
DECLARE		@Purpose_ID				INT				= NULL
DECLARE		@Domain_ID				INT				= NULL
DECLARE		@Copyright				INT				= NULL
DECLARE		@Variety_ID				INT				= NULL
DECLARE		@Mem_ID					VARCHAR(50)		= NULL
DECLARE		@Pattern_ID				INT				= NULL
DECLARE		@Q_svc_YN				CHAR(1)			= NULL
DECLARE		@Q_Division				CHAR(2)			= NULL
DECLARE		@Bqd_div				VARCHAR(3)		= NULL
DECLARE		@rm						VARCHAR(500)	= NULL
DECLARE		@SYNC_RESULT			VARCHAR(50)		= null

	DECLARE CUR_1 CURSOR FAST_FORWARD FOR

	SELECT	[sync_serno], [iud_cd], [serno], [set_tstppno], [qst_id], [text_id], [esc_msc_hsc_div_cd], [yy], [term_cd], [mid_edterm_div_cd], [grade_id], [subjt_id], [pubr_id], [lunt_id], [sbpt_id], [smunit_id], [qst_tp_cd], [qst_kind_cd], [lvdifct_cd], [sch_nm], [sch_id], [regn_id], [reg_dtm], [cont_top_id], [team_info], [inst_nm], [byunt_attr_cd], [expect_qst_div], [qst_bdbk_cont], [sck_use_unt_cd_unono], [qst_upd_dd_val], [coransr_upd_dd_val], [text_upd_dd_val], [expl_incl_div], [purpose_cd], [dom_cd], [copyr_div], [frm_top_id], [mem_id], [patrn_id], [svc_yn], [dptmt_cd], [schrec_sat_div], [rm]	
	FROM		tqq_question_sync	 
	ORDER BY [sync_serno]
	
	OPEN CUR_1

	FETCH NEXT FROM CUR_1
	INTO	@SYNC_SERNO, @SYNC_MODE, @idx, @Q_Cnt, @Question_id, @Passage_id, @siteid, @TestYear, @Term, @MidEnd, @grade_id, @course_id, @textbook_id, @rootpart_id, @subpart_id, @detailpart_id, 
			@type_id, @kind_id, @difficulty_id, @scl_name, @scl_id, @rg_id, @Regdate, @topic_id, @Team, @Academy, @ChapKind_ID, @IsExpectedQuestion, @Question_Text, @CodeNumber, @Q_DateLastModified, 
			@A_DateLastModified, @P_DateLastModified, @HasCommentary, @Purpose_ID, @Domain_ID, @Copyright, @Variety_ID, @Mem_ID, @Pattern_ID, @Q_svc_YN, @Q_Division, @Bqd_div, @rm
			
	WHILE @@FETCH_STATUS=0
		BEGIN

		--### ÀÛ¾÷´Ü ###--
			
						
			-- TB_BANK_QUESTION
			IF @SYNC_MODE = 'I' BEGIN
			
				IF NOT EXISTS (SELECT * FROM [I-SYSTEM].[dbo].[tb_Bank_Question] WITH(NOLOCK) WHERE idx = @idx) BEGIN

					SET IDENTITY_INSERT [I-SYSTEM].[dbo].[tb_Bank_Question] ON;
				
					INSERT INTO [I-SYSTEM].[dbo].[tb_Bank_Question](idx, Q_Cnt, Question_id, Passage_id, siteid, TestYear, Term, MidEnd, grade_id, course_id, textbook_id, rootpart_id, subpart_id, detailpart_id, [type_id], kind_id, difficulty_id, scl_name, scl_id, rg_id, RegDate, topic_id, Team, Academy, ChapKind_ID, IsExpectedQuestion, Question_Text, CodeNumber, Q_DateLastModified, A_DateLastModified, P_DateLastModified, HasCommentary, Purpose_ID, Domain_ID, Copyright, Variety_ID, Mem_ID, Pattern_ID, Q_svc_YN, Q_Division, Bqd_div)
					VALUES(@idx, @Q_Cnt, @Question_id, @Passage_id, @siteid, @TestYear, @Term, @MidEnd, @grade_id, @course_id, @textbook_id, @rootpart_id, @subpart_id, @detailpart_id, @type_id, @kind_id, @difficulty_id, @scl_name, @scl_id, @rg_id, @Regdate, @topic_id, @Team, @Academy, @ChapKind_ID, @IsExpectedQuestion, @Question_Text, @CodeNumber, @Q_DateLastModified, @A_DateLastModified, @P_DateLastModified, @HasCommentary, @Purpose_ID, @Domain_ID, @Copyright, @Variety_ID, @Mem_ID, @Pattern_ID, @Q_svc_YN, @Q_Division, @Bqd_div)

					SET IDENTITY_INSERT [I-SYSTEM].[dbo].[tb_Bank_Question] OFF;
				END
				ELSE BEGIN
					SET @SYNC_RESULT = 'F(-8)';
				END
			END
			
			
			IF @SYNC_MODE = 'U' BEGIN
				
				IF EXISTS(SELECT * FROM [I-SYSTEM].[dbo].[tb_Bank_Question] WITH(NOLOCK) WHERE idx = @idx) BEGIN
					UPDATE		[I-SYSTEM].[dbo].[tb_Bank_Question]
					SET			Q_Cnt                = @Q_Cnt
								, Question_id        = @Question_id
								, Passage_id         = @Passage_id
								, siteid             = @siteid
								, TestYear           = @TestYear
								, Term               = @Term
								, MidEnd             = @MidEnd
								, grade_id           = @grade_id
								, course_id          = @course_id
								, textbook_id        = @textbook_id
								, rootpart_id        = @rootpart_id
								, subpart_id         = @subpart_id
								, detailpart_id      = @detailpart_id
								, [type_id]          = @type_id
								, kind_id            = @kind_id
								, difficulty_id      = @difficulty_id
								, scl_name           = @scl_name
								, scl_id             = @scl_id
								, rg_id              = @rg_id								
								, topic_id           = @topic_id
								, Team               = @Team
								, Academy            = @Academy
								, ChapKind_ID        = @ChapKind_ID
								, IsExpectedQuestion = @IsExpectedQuestion
								, Question_Text      = @Question_Text
								, CodeNumber         = @CodeNumber
								, Q_DateLastModified = @Q_DateLastModified
								, A_DateLastModified = @A_DateLastModified
								, P_DateLastModified = @P_DateLastModified
								, HasCommentary      = @HasCommentary
								, Purpose_ID         = @Purpose_ID
								, Domain_ID          = @Domain_ID
								, Copyright          = @Copyright
								, Variety_ID         = @Variety_ID
								, Mem_ID             = @Mem_ID
								, Pattern_ID         = @Pattern_ID
								, Q_svc_YN           = @Q_svc_YN
								, Q_Division         = @Q_Division
								, Bqd_div            = @Bqd_div
					WHERE		idx = @idx

				END
				ELSE BEGIN
					SET @SYNC_RESULT = 'F(-7)';
				END
			END
			
			
			IF @SYNC_MODE = 'D' BEGIN
			
				IF EXISTS(SELECT * FROM [I-SYSTEM].[dbo].[tb_Bank_Question] WITH(NOLOCK) WHERE idx = @idx) BEGIN
					DELETE FROM [I-SYSTEM].[dbo].[tb_Bank_Question]
					WHERE idx = @idx

				END
				ELSE BEGIN
					SET @SYNC_RESULT = 'F(-4)';
				END
			END



			FETCH NEXT FROM CUR_1
			INTO	@SYNC_SERNO, @SYNC_MODE, @idx, @Q_Cnt, @Question_id, @Passage_id, @siteid, @TestYear, @Term, @MidEnd, @grade_id, @course_id, @textbook_id, @rootpart_id, @subpart_id, @detailpart_id, 
					@type_id, @kind_id, @difficulty_id, @scl_name, @scl_id, @rg_id, @Regdate, @topic_id, @Team, @Academy, @ChapKind_ID, @IsExpectedQuestion, @Question_Text, @CodeNumber, @Q_DateLastModified, 
					@A_DateLastModified, @P_DateLastModified, @HasCommentary, @Purpose_ID, @Domain_ID, @Copyright, @Variety_ID, @Mem_ID, @Pattern_ID, @Q_svc_YN, @Q_Division, @Bqd_div, @rm
				
		END
		CLOSE CUR_1
		DEALLOCATE CUR_1
```


#쿼리 [[query]]