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

In this hack, we will explore the capabilities of PGVector.

Steps 
* [Tutorial we will be following](https://aws.amazon.com/blogs/database/building-ai-powered-search-in-postgresql-using-amazon-sagemaker-and-pgvector/)
     * Install the PGVector extension
          * `CREATE EXTENSION vector`
          * `SELECT typname FROM pg_type WHERE typname = 'vector';`
     * Crete a table
          * `CREATE TABLE test_embeddings(product_id bigint, embeddings vector(3) );`
          * `INSERT INTO test_embeddings VALUES`
          * `(1, '[1, 2, 3]'), (2, '[2, 3, 4]'), (3, '[7, 6, 8]'), (4, '[8, 6, 9]');`
          * `SELECT product_id, embeddings, embeddings <-> '[3,1,2]' AS distance`
          * `FROM test_embeddings`
          * `ORDER BY embeddings <-> '[3,1,2]';`
          * Clean up!
               * `DROP TABLE test_embeddings;`

### PART 3: CLEAN-UP

* (Delete the EC2 ans DB instance)[https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_GettingStarted.CreatingConnecting.PostgreSQL.html#CHAP_GettingStarted.Deleting.PostgreSQL]

### Resources 
* 


