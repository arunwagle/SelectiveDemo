# SelectiveDemo

## For BIGSQL:

    Prerequiste
    Following users are provisioned:
    bigsql/passw0rd
    guest/guest-password

<b>Load Data in Hadoop</b>
            
Formats - Parquet, ORC
Create Hive Table
Design consideration
- Define partition keys
- Define bucketing if required


1. Create Database mortgage;
- DROP DATABASE IF EXISTS mortgage_sm CASCADE;
- Create DATABASE IF EXISTS mortgage_sm CASCADE;

- DROP DATABASE IF EXISTS mortgage_test CASCADE;
- Create DATABASE IF EXISTS mortgage_test CASCADE;


2a.PARQUET TABLE
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

2b. 
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

3.  SPARK Commands
/usr/iop/4.2.0.0/spark/bin/spark-submit \
--deploy-mode client \
--class com.ibm.demos.mortgage.cfpb.hmda.LoadCSVDriver \
--master yarn \
--conf 'spark.driver.extraJavaOptions=-DS3N_URL=s3a://awbucket1/data/HMDA/Selective-Demo/Mortgage_Report_Data_2015_1.csv -Dformat=PARQUET -D db=mortgage-sm' \
/ibm/oozie-cfpb-mortgage-hmda/lib/ibm-demos-1.0-SNAPSHOT-jar-with-dependencies.jar

/usr/iop/4.2.0.0/spark/bin/spark-submit \
    --deploy-mode client \
    --class com.ibm.demos.mortgage.cfpb.hmda.LoadCSVDriver \
    --master yarn \
    --conf 'spark.driver.extraJavaOptions=-DS3N_URL=s3a://awbucket1/data/HMDA/Selective-Demo/Mortgage_Report_Data_2015_sm.csv -Dformat=ORC -D db=mortgage-test' \
    /ibm/oozie-cfpb-mortgage-hmda/lib/ibm-demos-1.0-SNAPSHOT-jar-with-dependencies.jar


4. For BigSQL Demo purpose -
    -- Grant access user bigsql access to table
    CALL SYSHADOOP.HCAT_SYNC_OBJECTS('mortgage', 'MORTGAGE_REPORT_DATA_PARQUET', 'a', 'REPLACE', 'CONTINUE')

5. Access Control
    Cannot be done on the demo env, so talk about it.
    - Explain use case with example

6. Data Connect
    - Show Connections to Apache Hive
    - Cleanse data
    - Move data to DashDB

7. DSX

8. Watson Analytics

