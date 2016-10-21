# SelectiveDemo

## For BIGSQL: ##

    Prerequiste
    Following users are provisioned:
    bigsql/passw0rd
    guest/guest-password

<b>Demo</b>
            
    Formats - Parquet, ORC
    Create Hive Table
    Design consideration
        Define partition keys
        Define bucketing if required


<b>Create Databases</b><br/>
Create <b>mortgage_sm</b> database

        DROP DATABASE IF EXISTS mortgage_sm CASCADE;
        Create DATABASE mortgage_sm ;
    
<br/>Create <b>mortgage_test</b> database

        DROP DATABASE IF EXISTS mortgage_test CASCADE;
        Create DATABASE mortgage_test;


<b>PARQUET TABLE</b>

    CREATE EXTERNAL TABLE IF NOT EXISTS mortgage_test.MORTGAGE_REPORT_DATA_PARQUET(
     TRACT_TO_MSAMD_INCOME varchar(100), RATE_SPREAD varchar(100),
     POPULATION INT, MINORITY_POPULATION varchar(100),
     NUMBER_OF_OWNER_OCCUPIED_UNITS INT, NUMBER_OF_1_TO_4_FAMILY_UNITS INT,
     LOAN_AMOUNT_000S INT, HUD_MEDIAN_FAMILY_INCOME INT,
     APPLICANT_INCOME_000S INT, STATE_NAME varchar(100),
     STATE_ABBR varchar(100), SEQUENCE_NUMBER INT,
     RESPONDENT_ID varchar(100), PURCHASER_TYPE_NAME varchar(100),
     PROPERTY_TYPE_NAME varchar(100), PREAPPROVAL_NAME varchar(100),
     OWNER_OCCUPANCY_NAME varchar(100), MSAMD_NAME varchar(100),
     LOAN_TYPE_NAME varchar(100), LOAN_PURPOSE_NAME varchar(100),
     LIEN_STATUS_NAME varchar(100), HOEPA_STATUS_NAME varchar(100),
     EDIT_STATUS_NAME varchar(100), DENIAL_REASON_NAME_3 varchar(100),
     DENIAL_REASON_NAME_2 varchar(100), DENIAL_REASON_NAME_1 varchar(100),
     COUNTY_NAME varchar(100), CO_APPLICANT_SEX_NAME varchar(100),
     CO_APPLICANT_RACE_NAME_5 varchar(100), CO_APPLICANT_RACE_NAME_4 varchar(100),
     CO_APPLICANT_RACE_NAME_3 varchar(100), CO_APPLICANT_RACE_NAME_2 varchar(100),
     CO_APPLICANT_RACE_NAME_1 varchar(100), CO_APPLICANT_ETHNICITY_NAME varchar(100),
     CENSUS_TRACT_NUMBER varchar(100), AS_OF_YEAR INT,
     APPLICATION_DATE_INDICATOR INT, APPLICANT_SEX_NAME varchar(100),
     APPLICANT_RACE_NAME_5 varchar(100), APPLICANT_RACE_NAME_4 varchar(100),
     APPLICANT_RACE_NAME_3 varchar(100), APPLICANT_RACE_NAME_2 varchar(100),
     APPLICANT_RACE_NAME_1 varchar(100), APPLICANT_ETHNICITY_NAME varchar(100),
     AGENCY_NAME varchar(100), AGENCY_ABBR varchar(100),
    ACTION_TAKEN_NAME varchar(100) )
    STORED AS PARQUET;

<b>ORC</b>
    
    CREATE EXTERNAL TABLE IF NOT EXISTS mortgage_sm.MORTGAGE_REPORT_DATA_ORC(
      TRACT_TO_MSAMD_INCOME varchar(100), RATE_SPREAD varchar(100),
      POPULATION INT, MINORITY_POPULATION varchar(100),
      NUMBER_OF_OWNER_OCCUPIED_UNITS INT, NUMBER_OF_1_TO_4_FAMILY_UNITS INT,
      LOAN_AMOUNT_000S INT, HUD_MEDIAN_FAMILY_INCOME INT,
      APPLICANT_INCOME_000S INT, STATE_NAME varchar(100),
      STATE_ABBR varchar(100), SEQUENCE_NUMBER INT,
      RESPONDENT_ID varchar(100), PURCHASER_TYPE_NAME varchar(100),
      PROPERTY_TYPE_NAME varchar(100), PREAPPROVAL_NAME varchar(100),
      OWNER_OCCUPANCY_NAME varchar(100), MSAMD_NAME varchar(100),
      LOAN_TYPE_NAME varchar(100), LOAN_PURPOSE_NAME varchar(100),
      LIEN_STATUS_NAME varchar(100), HOEPA_STATUS_NAME varchar(100),
      EDIT_STATUS_NAME varchar(100), DENIAL_REASON_NAME_3 varchar(100),
      DENIAL_REASON_NAME_2 varchar(100), DENIAL_REASON_NAME_1 varchar(100),
      COUNTY_NAME varchar(100), CO_APPLICANT_SEX_NAME varchar(100),
      CO_APPLICANT_RACE_NAME_5 varchar(100), CO_APPLICANT_RACE_NAME_4 varchar(100),
      CO_APPLICANT_RACE_NAME_3 varchar(100), CO_APPLICANT_RACE_NAME_2 varchar(100),
      CO_APPLICANT_RACE_NAME_1 varchar(100), CO_APPLICANT_ETHNICITY_NAME varchar(100),
      CENSUS_TRACT_NUMBER varchar(100), AS_OF_YEAR INT,
      APPLICATION_DATE_INDICATOR INT, APPLICANT_SEX_NAME varchar(100),
      APPLICANT_RACE_NAME_5 varchar(100), APPLICANT_RACE_NAME_4 varchar(100),
      APPLICANT_RACE_NAME_3 varchar(100), APPLICANT_RACE_NAME_2 varchar(100),
      APPLICANT_RACE_NAME_1 varchar(100), APPLICANT_ETHNICITY_NAME varchar(100),
      AGENCY_NAME varchar(100), AGENCY_ABBR varchar(100),
      ACTION_TAKEN_NAME varchar(100) )
      STORED AS ORC;

<b>SPARK Commands</b>
    Run Spark Application to generate PARQUET format
    
        /usr/iop/4.2.0.0/spark/bin/spark-submit \
        --deploy-mode client \
        --class com.ibm.demos.mortgage.cfpb.hmda.LoadCSVDriver \
        --master yarn \
        --conf 'spark.driver.extraJavaOptions=-DS3N_URL=s3a://awbucket1/data/HMDA/Selective-Demo/Mortgage_Report_Data_2015_1.csv -Dformat=PARQUET -Ddb=mortgage-sm' \
        /ibm/oozie-cfpb-mortgage-hmda/lib/ibm-demos-1.0-SNAPSHOT-jar-with-dependencies.jar

    Run Spark Application to generate PARQUET format
    
        /usr/iop/4.2.0.0/spark/bin/spark-submit \
            --deploy-mode client \
            --class com.ibm.demos.mortgage.cfpb.hmda.LoadCSVDriver \
            --master yarn \
            --conf 'spark.driver.extraJavaOptions=-DS3N_URL=s3a://awbucket1/data/HMDA/Selective-Demo/Mortgage_Report_Data_2015_sm.csv -Dformat=ORC -Ddb=mortgage-test' \
            /ibm/oozie-cfpb-mortgage-hmda/lib/ibm-demos-1.0-SNAPSHOT-jar-with-dependencies.jar


<b>For BigSQL Demo purpose </b>
    <br><i>SYNC Data</i></br>
    
        CALL SYSHADOOP.HCAT_SYNC_OBJECTS('mortgage_sm', 'MORTGAGE_REPORT_DATA_ORC', 'a', 'REPLACE', 'CONTINUE')
        CALL SYSHADOOP.HCAT_SYNC_OBJECTS('mortgage_test', 'MORTGAGE_REPORT_DATA_PARQUET', 'a', 'REPLACE', 'CONTINUE')
        
    <br><i>Queries</i></br>
    
        Select count(*) FROM MORTGAGE_SM.MORTGAGE_REPORT_DATA_ORC;
        
        Select count(*) FROM MORTGAGE_TEST.MORTGAGE_REPORT_DATA_PARQUET;
        
        SELECT LOAN_TYPE_NAME, LOAN_PURPOSE_NAME, POPULATION, LOAN_AMOUNT_000S, APPLICANT_INCOME_000S, STATE_ABBR, STATE_NAME, SEQUENCE_NUMBER, PURCHASER_TYPE_NAME, AGENCY_NAME FROM MORTGAGE_SM.MORTGAGE_REPORT_DATA_ORC;
        
        SELECT LOAN_TYPE_NAME, LOAN_PURPOSE_NAME, POPULATION, LOAN_AMOUNT_000S, APPLICANT_INCOME_000S, STATE_ABBR, STATE_NAME, SEQUENCE_NUMBER, PURCHASER_TYPE_NAME, AGENCY_NAME FROM MORTGAGE_TEST.MORTGAGE_REPORT_DATA_PARQUET;
        
        GRANT SELECT ON mortgage.MORTGAGE_REPORT_DATA_PARQUET TO LOAN_MGR;

    <br><i>Access Control Use case. This cannot be demoed right now</i></br>

        CREATE ROLE LOAN_MGR;
        
        GRANT ROLE LOAN_MGR TO USER bigsql;

        CREATE MASK TRACT_TO_MSAMD_INCOME_MASK ON mortgage_test.MORTGAGE_REPORT_DATA_PARQUET FOR
        COLUMN TRACT_TO_MSAMD_INCOME RETURN
        CASE WHEN VERIFY_ROLE_FOR_USER (SESSION_USER, 'LOAN_MGR') = 1
        THEN "N/A"
        ELSE TRACT_TO_MSAMD_INCOME
        END
        ENABLE;

        -- Enable Access Control
        ALTER TABLE mortgage_test.MORTGAGE_REPORT_DATA_PARQUET ACTIVATE COLUMN ACCESS CONTROL;

<b>Access Control</b>
    
    Cannot be done on the demo env, so talk about it.
    Explain use case with example

<b>Data Connect</b>
    
    Show Connections to Apache Hive
    Cleanse data
    Move data to DashDB

<b>For DSX Demo</b>

<b>For Watson Analytics Demo</b>

