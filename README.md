# Azure-PostgreSQL-vs-AWS-PostgreSQL-Hands-On-Hack

## Overview

### TOOLS
* Windows Powershell
* Install pgAdmin 4 

### HACK PRE-REQUISITES 

* Clone this repository or download the zip file.

### PART 1: COMPUTE + POSTGRESQL DB

In this hack, we will create an EC2 instance, a PostgreSQL RDS DB, and finally connect to the database.

Steps
* Navigate to [Amazon Console](https://gpsus.signin.aws.amazon.com/console)
* Pin EC2, Amazon RDS, and Amazon SageMaker for easy access (Optional)
* The tutorial we will follow to [create and connect to a PostgreSQL DB instance](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_GettingStarted.CreatingConnecting.PostgreSQL.html)
    * [Create an EC2 instance](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_GettingStarted.CreatingConnecting.PostgreSQL.html#CHAP_GettingStarted.Creating.RDSPostgreSQL.EC2)
        * Note: Your .pem key should be downloaded after this step and stored somewhere locally
    * [Create a PostgreSQL DB instance](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_GettingStarted.CreatingConnecting.PostgreSQL.html#CHAP_GettingStarted.Creating.PostgreSQL)
        * Once the status shows Available, go to the Database instance and ensure that it has "Public" access by clicking on the Modify button. [Public Access](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Overview.DBInstance.Modifying.html)
        * [Check Security Permissions](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Overview.DBInstance.Modifying.html)
    * [Connect to a PostgreSQL RDS Database](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_GettingStarted.CreatingConnecting.PostgreSQL.html#CHAP_GettingStarted.Connecting.PostgreSQL)
      * [PGAdmin 4](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_ConnectToPostgreSQLInstance.html#USER_ConnectToPostgreSQLInstance.pgAdmin)
      * PSQL 
          * Connect to your EC2 instance `ssh -i location_of_pem_file ec2-user@ec2-instance-public-dns-name`
          * Latest bug fixes and security updates `sudo dnf update -y`
          * Install psql command line client from PostgreSQL on Amazon Linux 2023 `				1) sudo dnf install postgresql15`
          * Connect to the PostgreSQL DB instance  `psql --host=endpoint --port=5432 --dbname=postgres --username=postgres`
          * Practice some commands
              * `SELECT CURRENT_TIMESTAMP;`
              * Note: Make sure you are running on Postgres version later than 15.2 otherwise pgvector is not available as an extension on RDS `SHOW rds.extensions;`

### PART 2: USING PGVECTOR

In this hack, we will explore the Generative AI Studio inside Vertex AI component of the GCP console. 

Steps 
* Navigate to the three lines at the top left of the GCP Console. You will see a dropdown list. Scroll down until you find the Artificail Intelligence section. We want to click on Vertex AI. It may be helpful to pin this service so it shows up at the top of the drop down next time. 
* You will see the Generative AI Studio on the left sidebar. Click on Vision. Choose the "Gen-AI-Demo-Img" from the data folder and upload it to the UI.
* We can experiement with the two features "Caption" and "Visual Q&A"
  


### Resources 
* 


