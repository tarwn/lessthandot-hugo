---
title: How to insert information into multiple related tables and return ID using SQLDataSource
author: Naomi Nosonovsky
type: post
date: 2009-08-07T00:22:54+00:00
excerpt: |
  In the ASP.NET forums I'm frequent the question of inserting information into multiple related tables is asked very often. I want to share my solution of this problem. The answer is based on this thread ASP.NET thread
  
  Please also take a look at this&hellip;
url: /index.php/webdev/webdesigngraphicsstyling/how-to-insert-information-into-multiple/
views:
  - 78933
rp4wp_auto_linked:
  - 1
categories:
  - Web Design, Graphics and Styling

---
In the ASP.NET forums I am frequent the question of inserting information into multiple related tables is asked very often. I want to share my solution of this problem. The answer is based on this thread [ASP.NET thread][1]

Please also take a look at this [article][2] showing how to get ID of the newly inserted record.

Also, one more word about TRY/CATCH logic in this procedure. I took this idea from [Kevin Goff&#8217;s article][3] Touch/Peel/Stand, then Try/Catch/Raise<!--more-->

You can also review this article [Retrieving the Just-Inserted ID of an IDENTITY Column Using a SqlDataSource Control][4] dealing with the similar problem.

First, I show the whole stored procedure code. Don&#8217;t be surprised about the non-normalized data, this is what we were dealing with.

<pre>/****** Object:  StoredProcedure [dbo].[PersonInsert]    Script Date: 06/25/2008 15:38:00 ******/
        SET ANSI_NULLS ON
        GO
        SET QUOTED_IDENTIFIER ON
        GO
        
        IF EXISTS
        (
                SELECT  1
                FROM    dbo.sysobjects
                WHERE   id                                 = object_id (N'[dbo].[PersonInsert]')
                    AND OBJECTPROPERTY(id, N'IsProcedure') = 1
        )
DROP PROCEDURE [dbo].[PersonInsert]
GO
-- =============================================
        -- Author:  Naomi 
        -- Create date: 06-27-2008
        -- Description: Inserts Person's Information based on PersonType
-- =============================================
CREATE PROCEDURE [dbo].[PersonInsert]
@NewPersonID         INT     = NULL Output,
        @PersonType  CHAR(1) = 'A', -- default is Adult Volunteer
        @SiteID      INT,
        @FirstName   VARCHAR(25),
        @MiddleName  VARCHAR(25) = NULL,
        @LastName    VARCHAR(30),
        @Gender      CHAR(1),
        @DOB         DATETIME,
        @UserName    VARCHAR(25) = NULL,
        @Pwd         VARCHAR(20) = '',
        @Title       VARCHAR(50) = NULL,
        @Address1    VARCHAR(50),
        @Address2    VARCHAR(25) = NULL,
        @City        VARCHAR(22),
        @Zip         CHAR(7),
        @State       CHAR(2),
        @Email       VARCHAR(75) = NULL,
        @SecondEmail VARCHAR(75) = NULL,
        @ScreenName  VARCHAR(25) = NULL,
        @HomePhone   VARCHAR(25),
        @CellPhone   VARCHAR(25)       = NULL,
        @Comment     VARCHAR(MAX)      = NULL,
        @FComment    VARCHAR(MAX)      = NULL,
        @MotherID    INT               = 0,
        @FatherID    INT               = 0,
        @Father      VARCHAR(50)       = '',
        @Mother      VARCHAR(50)       = '',
        @DefaultPicture VARBINARY(MAX) = NULL,
        @School           VARCHAR(25)            = '',
        @Grade            CHAR(2)                = '',
        @MedicalConcerns  VARCHAR(150)           = NULL,
        @DietRestrictions VARCHAR(75)            = 'NONE',
        @DietaryRest      VARCHAR(75)            = 'NONE',
        @CurrentPoints    INT                    = 0,
        @TotalPoints      INT                    = 0,
        @MitzvahRewardsID INT                    = NULL,
        @Medications      VARCHAR(50)            = 'NONE',
        @NonActivities    VARCHAR(50)            = NULL,
        @LTetnus smalldatetime                   = NULL,
        @Allergies VARCHAR(75)                   = 'NONE',
        @TylenolAllowed bit                      = NULL,
        @AspirinAllowed bit                      = NULL,
        @MedicalInsurance       VARCHAR(30)            = NULL,
        @PolicyNum              VARCHAR(18)            = NULL,
        @EmergencyContact       VARCHAR(50)            = NULL,
        @RelationshipContact    CHAR(10)               = NULL,
        @EmergencyContactPhone  VARCHAR(25)            = NULL,
        @EmergencyContact1      VARCHAR(50)            = NULL,
        @RelationshipContact1   CHAR(10)               = NULL,
        @EmergencyContactPhone1 VARCHAR(25)            = NULL,
        @Physician              VARCHAR(30)            = NULL,
        @PhysicianPhone         VARCHAR(25)            = NULL,
        @Hospital               VARCHAR(18)            = NULL,
        @ToiletTrained bit                             = NULL,
        @Pets                      VARCHAR(35)                              = NULL,
        @TherapiesDetails          VARCHAR(MAX)                             = NULL,
        @NamesAgesSiblings         VARCHAR(200)                             = NULL,
        @ChildDescription          VARCHAR(200)                             = NULL,
        @ChildsCommunicationSkills VARCHAR(100)                             = NULL,
        @FavActivities             VARCHAR(75)                              = NULL,
        @LFavActivities            VARCHAR(75)                              = NULL,
        @GainFromFC                VARCHAR(50)                              = NULL,
        @HeardAboutFC              VARCHAR(60)                              = NULL,
        @WantFriendAtHome bit                                               = 0,
        @Interviewed bit                                                    = 0,
        @PublishPictures bit                                                = 0,
        @Occupation nvarchar(50)                                            = NULL,
        @BusinessName nvarchar(75)                                          = NULL,
        @BusAdd1 nvarchar(50)                                               = NULL,
        @BusAdd2 nvarchar(25)                                               = NULL,
        @BusCity nvarchar(22)                                               = NULL,
        @BusState      CHAR(2)                                                   = NULL,
        @BusZip        CHAR(7)                                                   = NULL,
        @BusEMail      VARCHAR(70)                                               = NULL,
        @Pager         VARCHAR(25)                                               = NULL,
        @Fax           VARCHAR(25)                                               = NULL,
        @BusinessPhone VARCHAR(25)                                               = NULL,
        @Anniversary   DATETIME                                                  = NULL,
        @Affiliation nvarchar(70)                                                = NULL,
        @MaritalStatus CHAR(1)                                                   = NULL,
        @SpouseID      INT                                                       = 0,
        @SpouseFN      VARCHAR(25)                                               = '',
        @SpouseLN      VARCHAR(30)                                               = '',
        @IsActive bit                                                            = 1,
        @HebrewBDay nvarchar(MAX)                                                ='',
        @SchoolPhone VARCHAR(25)                                                 = '',
        @FCellPhone  VARCHAR(25)                                                 ='',
        @MCellPhone  VARCHAR(25)                                                 ='',
        @FAddress    VARCHAR(MAX)                                                ='',
        @MAddress    VARCHAR(MAX)                                                ='',
        @IsMember bit                                                            = NULL,
        @StartDate smalldatetime                                                 = NULL,
        @PaymentMethod VARCHAR(MAX)                                              = NULL,
        @BillQuaterly bit                                                        = NULL
AS
        BEGIN
                -- SET NOCOUNT ON added to prevent extra result sets from
                -- interfering with SELECT statements.
                SET NOCOUNT ON;
                IF EXISTS
                (
                        SELECT  1
                        FROM    dbo.People
                        WHERE   UserName = @UserName
                            AND SiteID   = @SiteID
                )
                BEGIN
                        RAISERROR (N'UserName %s already exists. Please choose another username.',
                        16, -- Severity,
                        1,  -- State,
                        @UserName)
                        RETURN -1
                END
                IF EXISTS
                (
                        SELECT  1
                        FROM    dbo.People
                        WHERE   FirstName = LTRIM(@FirstName)
                            AND LastName  = LTRIM(@LastName)
                            AND DOB       = @DOB
                            AND SiteID    = @SiteID
                )
                BEGIN
                        RAISERROR (N'User %s %s already exists.',
                        16, -- Severity,
                        1,  -- State,
                        @FirstName, @LastName)
                        RETURN -1
                END
                BEGIN TRY
                        --set @DefaultPicture = CONVERT(varbinary(max),@DefaultPicture)
                        BEGIN TRANSACTION
                                -- All types first insert into People table
                                INSERT
                                INTO    People
                                        (
                                                IsActive   ,
                                                FirstName  ,                                                
                                                MiddleName ,
                                                LastName   ,
                                                Gender     ,
                                                DOB        ,
                                                Address1   ,
                                                Address2   ,
                                                City       ,
                                                State      ,
                                                Zip        ,
                                                Email      ,
                                                SecondEmail,
                                                ScreenName ,
                                                HomePhone  ,
                                                CellPhone  ,
                                                Comment    ,
                                                UserName   ,
                                                Pwd        ,
                                                Mother     ,
                                                Father     ,
                                                PersonType ,
                                                SiteID     ,
                                                DefaultPicture
                                        )
                                        VALUES
                                        (
                                                ISNULL(@IsActive, 1)                   , 
                                                LTRIM(@FirstName)                      ,
                                                LTRIM(ISNULL(@MiddleName,''))          ,
                                                LTRIM(@LastName)                       ,
                                                @Gender                                ,
                                                @DOB                                   ,
                                                @Address1                              ,
                                                @Address2                              ,
                                                @City                                  ,
                                                @State                                 ,
                                                @Zip                                   ,
                                                @Email                                 ,
                                                @SecondEmail                           ,
                                                @ScreenName                            ,
                                                @HomePhone                             ,
                                                @CellPhone                             ,
                                                @Comment                               ,
                                                @UserName                              ,
                                                @Pwd                                   ,
                                                isnull(@MotherID,0)                    ,
                                                isnull(@FatherID, 0)                   ,
                                                dbo.GetPersonType(@PersonType, @Gender),
                                                @SiteID                                ,
                                                @DefaultPicture)

                                --print 'Inserted new record into People'
                                SET @NewPersonID = scope_identity()
                                IF @PersonType  IN ('O','W','B','G','E','F','V','M')
                                -- Teen Volunteer or Teen Bnei Mitzvah or Volunteer In Training or Friends
                                BEGIN
                                        IF @PersonType IN ('O','W','B','G') -- Volunteer In Training or Friends
                                        BEGIN
                                                INSERT
                                                INTO    Friends
                                                        (
                                                                School                   ,
                                                                Grade                    ,
                                                                MedicalConcerns          ,
                                                                Medications              ,
                                                                NonActivities            ,
                                                                LTetnus                  ,
                                                                Allergies                ,
                                                                DietaryRest              ,
                                                                TylenolAllowed           ,
                                                                AspirinAllowed           ,
                                                                MedicalInsurance         ,
                                                                PolicyNum                ,
                                                                EmergencyContact         ,
                                                                EmergencyContactPhone    ,
                                                                RelationshipContact      ,
                                                                EmergencyContact1        ,
                                                                EmergencyContactPhone1   ,
                                                                RelationshipContact1     ,
                                                                Physician                ,
                                                                PhysicianPhone           ,
                                                                Hospital                 ,
                                                                ToiletTrained            ,
                                                                Pets                     ,
                                                                TherapiesDetails         ,
                                                                NamesAgesSiblings        ,
                                                                ChildDescription         ,
                                                                ChildsCommunicationSkills,
                                                                FavActivities            ,
                                                                LFavActivities           ,
                                                                GainFromFC               ,
                                                                HeardAboutFC             ,
                                                                WantFriendAtHome         ,
                                                                Interviewed              ,
                                                                Comment                ,
                                                                PublishPictures          ,
                                                                FriendID
                                                        )
                                                        VALUES
                                                        (
                                                                isnull(@School,'')                   ,
                                                                LTRIM(RTRIM( isnull(@Grade,'')))     ,
                                                                isnull(@MedicalConcerns,'NONE')      ,
                                                                isnull(@Medications,'NONE')          ,
                                                                isnull(@NonActivities,'')            ,
                                                                @LTetnus                             ,
                                                                isnull(@Allergies,'NONE')            ,
                                                                isnull(@DietaryRest,'NONE')          ,
                                                                @TylenolAllowed                      ,
                                                                @AspirinAllowed                      ,
                                                                isnull(@MedicalInsurance,'')         ,
                                                                isnull(@PolicyNum,'')                ,
                                                                isnull(@EmergencyContact,'')         ,
                                                                isnull(@EmergencyContactPhone,'')    ,
                                                                isnull(@RelationshipContact,'')      ,
                                                                isnull(@EmergencyContact1,'')        ,
                                                                isnull(@EmergencyContactPhone1,'')   ,
                                                                isnull(@RelationshipContact1,'')     ,
                                                                isnull(@Physician,'')                ,
                                                                isnull(@PhysicianPhone,'')           ,
                                                                isnull(@Hospital,'')                 ,
                                                                @ToiletTrained                       ,
                                                                isnull(@Pets,'')                     ,
                                                                isnull(@TherapiesDetails,'')         ,
                                                                isnull(@NamesAgesSiblings,'')        ,
                                                                isnull(@ChildDescription,'')         ,
                                                                isnull(@ChildsCommunicationSkills,''),
                                                                isnull(@FavActivities,'')            ,
                                                                isnull(@LFavActivities,'')           ,
                                                                isnull(@GainFromFC,'')               ,
                                                                isnull(@HeardAboutFC,'')             ,
                                                                @WantFriendAtHome                    ,
                                                                @Interviewed                         ,
                                                                isnull(@Comment,'')                  ,
                                                                @PublishPictures                     ,
                                                                @NewPersonID
                                                        )
                                        END
                                        IF @PersonType IN ('O', 'W','E','F','V','M')
                                        --   Volunteer In Training or Teen Volunteer or Teen Bnei Mitzvah
                                        BEGIN
                                                INSERT
                                                INTO    Volunteers
                                                        (
                                                                Grade           ,
                                                                School          ,
                                                                Medications     ,
                                                                DietRestrictions,
                                                                Allergies       ,
                                                                PersonID        
                                                        )
                                                        VALUES
                                                        (
                                                                LTRIM(RTRIM(@Grade))       ,
                                                                @School                    ,
                                                                isnull(@Medications,'NONE'),
                                                                isnull(@DietaryRest,'NONE'),
                                                                isnull(@Allergies,'NONE')  ,
                                                                @NewPersonID               
                                                        )
                                        END
                                END
                                ELSE -- Regular Person / Donors / Adult Volunteer
                                BEGIN
                                        -- First we update Spouse's information with this new PersonID
                                        IF COALESCE(@SpouseID,0) > 0 -- we're trying to set new SpouseID
                                        UPDATE AdultInfo
                                        SET     SpouseID = @NewPersonID
                                        WHERE   PersonID =
                                                (
                                                        SELECT  SpouseID
                                                        FROM    AdultInfo
                                                        WHERE   PersonID             = @SpouseID
                                                            AND COALESCE(SpouseID,0) = 0
                                                )
                                        -- What if we had Spouse pointing already to somebody else ?
                                        IF EXISTS
                                        (
                                                SELECT  SpouseID
                                                FROM    AdultInfo
                                                WHERE   PersonID             = @SpouseID
                                                    AND COALESCE(SpouseID,0) > 0
                                                    AND SpouseID            <> @NewPersonID
                                        )
                                        BEGIN
                                                -- THROW an Error here
                                                RAISERROR (N'The Spouse information is incorrect.',
                                                16, -- Severity,
                                                1   -- State,
                                                )
                                                -- Execution automatically stops here and we'll be now in the CATCH block
                                        END
                                        ELSE
                                        BEGIN
                                                INSERT
                                                INTO    AdultInfo
                                                        (
                                                                PersonID     ,
                                                                Title        ,
                                                                Occupation   ,
                                                                BusinessName ,
                                                                BusAdd1      ,
                                                                BusAdd2      ,
                                                                BusCity      ,
                                                                BusState     ,
                                                                BusZip       ,
                                                                Pager        ,
                                                                Fax          ,
                                                                BusinessPhone,
                                                                SpouseFN     ,
                                                                SpouseLN     ,
                                                                SpouseID     ,
                                                                Anniversary  ,
                                                                MaritalStatus
                                                        )
                                                        VALUES
                                                        (
                                                                @NewPersonID  ,
                                                                @Title        ,
                                                                @Occupation   ,
                                                                @BusinessName ,
                                                                @BusAdd1      ,
                                                                @BusAdd2      ,
                                                                @BusCity      ,
                                                                @BusState     ,
                                                                @BusZip       ,
                                                                @Pager        ,
                                                                @Fax          ,
                                                                @BusinessPhone,
                                                                @SpouseFN     ,
                                                                @SpouseLN     ,
                                                                @SpouseID     ,
                                                                @Anniversary  ,
                                                                @MaritalStatus
                                                        )
                                        END
                                END
                                --All person's types processed
                                IF @SiteID = 1 AND COALESCE(@IsMember,0) > 0
                                INSERT
                                INTO    MEMBERSHIP
                                        (
                                                PersonID     ,
                                                IsMember     ,
                                                StartDate    ,
                                                PaymentMethod,
                                                BillQuaterly
                                        )
                                        VALUES
                                        (
                                                @NewPersonID  ,
                                                @IsMember     ,
                                                @StartDate    ,
                                                @PaymentMethod,
                                                @BillQuaterly
                                        )
                                COMMIT TRANSACTION
                        END TRY
                        BEGIN
                                CATCH
                                DECLARE @ErrorSeverity INT,
                                        @ErrorNumber   INT,
                                        @ErrorMessage nvarchar(4000),
                                        @ErrorState INT,
                                        @ErrorLine  INT,
                                        @ErrorProc nvarchar(200)
                                        -- Grab error information from SQL functions
                                SET @ErrorSeverity = ERROR_SEVERITY()
                                SET @ErrorNumber   = ERROR_NUMBER()
                                SET @ErrorMessage  = ERROR_MESSAGE()
                                SET @ErrorState    = ERROR_STATE()
                                SET @ErrorLine     = ERROR_LINE()
                                SET @ErrorProc     = ERROR_PROCEDURE()
                                SET @ErrorMessage  = 'Problem updating person''s information.' + CHAR(13) + 'SQL Server Error Message is: ' + CAST(@ErrorNumber AS VARCHAR(10)) + ' in procedure: ' + @ErrorProc + ' Line: ' + CAST(@ErrorLine AS VARCHAR(10)) + ' Error text: ' + @ErrorMessage
                                -- Not all errors generate an error state, to set to 1 if it's zero
                                IF @ErrorState  = 0
                                SET @ErrorState = 1
                                -- If the error renders the transaction as uncommittable or we have open transactions, we may want to rollback
                                IF @@TRANCOUNT > 0
                                BEGIN
                                        --print 'Rollback transaction'
                                        ROLLBACK TRANSACTION
                                END
                                RAISERROR (@ErrorMessage , @ErrorSeverity, @ErrorState, @ErrorNumber)
                        END CATCH
                        RETURN @@ERROR
                END
                GO
                                </pre>

And the way to grab the ID of newly inserted person using SQLDataSource:

<pre>#region DataSource Inserted
protected void DataSource_Inserted(object sender, SqlDataSourceStatusEventArgs e) 
{

if (e.Command.Parameters["@NewPersonID"].Value != DBNull.Value)

{ this.NewPersonID = Convert.ToInt32(e.Command.Parameters["@NewPersonID"].Value); } 
else

{ this.NewPersonID = 0; } 
}
#endregion</pre>

Please also see [How to return Error Messages from the SQL Server to the application][5] for more discussion about actual implementation of this SP in the web application.

I want to also note, that in SQL Server 2008 and up we can simplify the insert into 2 tables by using [composable DML][6].

 [1]: http://forums.asp.net/p/1373630/2880551.aspx#2880551
 [2]: http://www.mikesdotnetting.com/Article/54/Getting-the-identity-of-the-most-recently-added-record
 [3]: http://www.tsck.net/DasBlog/CategoryView,category,T-SQL.aspx
 [4]: http://aspnet.4guysfromrolla.com/articles/100108-1.aspx
 [5]: /index.php/WebDev/WebDesignGraphicsStyling/how-to-return-error-messages-from-the-sq
 [6]: http://weblogs.sqlteam.com/peterl/archive/2009/04/08/Composable-DML.aspx