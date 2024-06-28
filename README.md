# Azure-PostgreSQL-vs-AWS-PostgreSQL-Hands-On-Hack

## Overview

### TOOLS
* Install pgAdmin 4
* WSL
* Windows Powershell

### HACK PRE-REQUISITES 

* Clone this repository or download the zip file.
* Navigate to [Amazon Console](https://gpsus.signin.aws.amazon.com/console)
* Pin EC2, Amazon RDS, and Amazon SageMaker for easy access (Optional)
* The tutorial we will follow to [create and connect to a PostgreSQL DB instance](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_GettingStarted.CreatingConnecting.PostgreSQL.html)
    * [Create an EC2 instance](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_GettingStarted.CreatingConnecting.PostgreSQL.html#CHAP_GettingStarted.Creating.RDSPostgreSQL.EC2)
        * Note: Your .pem key should be downloaded after this step and stored somewhere locally
    * [Create a PostgreSQL DB instance](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_GettingStarted.CreatingConnecting.PostgreSQL.html#CHAP_GettingStarted.Creating.PostgreSQL)


### PART 1: NETWORKING CONFIGURATIONS + PG ADMIN 4

In this hack, we will work on the network configurations and try connecting to the database.

Configurations
   * PostgreSQL RDS DB
        * Once the status shows Available, go to the Database instance and ensure that it has "Public" access by clicking on the Modify button. [Public Access](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Overview.DBInstance.Modifying.html)
        * [Check Security Permissions](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Overview.DBInstance.Modifying.html)
             * Navigate to your RDS instance -> VPC Security Groups -> Choose your rds security group -> Scroll down to Inbound and Outbound rules. 
                  * Inbound Rules: Connectivity should include the already exisiting connection from the EC2 instance. Please also add in the connectivity for the SG for inbound traffic on the PostgreSQL port to access the PostgreSQL DB. We want to allow all ipv4 and ipv6 addresses. Add the two rules below.
                      * Type: PostgreSQL and Source: Anywhere-IPv4
                      * Type: PostgreSQL and Source: Anywhere-IPv6
                  * Outbound Rules: Not needed.
             * Navigate to your RDS instance -> VPC Security Groups -> Choose your ec2 security group -> Scroll down to Inbound and Outbound rules.
                  * Inbound Rules: Not needed.
                  * Outbound Rules: You should have the exisiting SG rule to allow connections to your RDS instance.
        * Networing Configurations
             * Navigate to your Amazon PostgreSQL RDS Database
                  * Examine Networking: Go to VPC Security Group
                      * Inbound Rules: It should have connectivity from everywhere to this security group.
                      * Outbound Rules: Empty! There is no traffic that goes out from the security group from this subnet.
                  * Navigate to Linux shell (WSL) to validate
                       * `dig "ENTER DB-HOST NAME` : The IP address should be public -> 52.8.215.180
                       * `nc -v "ENTER DB-HOST NAME" "ENTER PORT NUMBER"` : Validate TCP/IP connectivity and see if you can connect or not 
                  * Go back to console. Your RDS instance should be associated with a VPC and two subnet groups.
                       * Check the routing table configuration in the VPC -> Main routing table
                       * You should have 2 routes: one local, one global pointing to the internet gateway. So the VPC itself has outbound connectivity to the internet.
                       * Navigate to the subnet routing table -> Subnets -> Route table. You should see local and igw route.
                            * If you do not, then go to Route table ID -> Edit routes -> Destination: `0.0.0.0/0` and Target: Internet Gateway `igw-xxxx`
                       * Go back to the VPC routing table-> Subnet Associations -> Associate the RDS subnets with this main table. This is how we can establish connectivity since we fixed outbound rules. This way, we allow outbound packets to go out from this subnet and provided a route to the internet.

### Two Ways to connect to your PostgreSQL RDS RB       
   * PGAdmin 4
   * [Tutorial](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_GettingStarted.CreatingConnecting.PostgreSQL.html#CHAP_GettingStarted.Connecting.PostgreSQL)
     * Add New Server 
       * Provide a name for your server
          * Provide your connection db host name
          * Confirm the right port, username 
          * Provide your db password
          * Hit Save
   * PSQL 
     * Connect to your EC2 instance `ssh -i location_of_pem_file ec2-user@ec2-instance-public-dns-name`
     * Latest bug fixes and security updates `sudo dnf update -y`
     * Install psql command line client from PostgreSQL on Amazon Linux 2023 `sudo dnf install postgresql15`
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
     * New datatype called vector is installed
          * `SELECT typname FROM pg_type WHERE typname = 'vector';`
     * Crete a table for storing three dimensional vectors
          * `CREATE TABLE test_embeddings(product_id bigint, embeddings vector(3) );`
     * Insert sample data
          * `INSERT INTO test_embeddings VALUES`
          * `(1, '[1, 2, 3]'), (2, '[2, 3, 4]'), (3, '[7, 6, 8]'), (4, '[8, 6, 9]');`
     * Query data using Euclidean distance 
          * `SELECT product_id, embeddings, embeddings <-> '[3,1,2]' AS distance`
          * `FROM test_embeddings`
          * `ORDER BY embeddings <-> '[3,1,2]';`
     * Test the above command with cosine distance `<=>`, and L1 distance `<+>` also!
     * Clean up!
          * `DROP TABLE test_embeddings;`

### PART 3: AI-powered search in PostgreSQL using AWS SageMaker and pgvector

Steps:
* [Tutorial here](https://github.com/aws-samples/rds-postgresql-pgvector/tree/main)
   * Navigate to Amazon Sagemaker -> Notebooks -> Git repositories -> Clone a public Git repository to this notebook instance only -> Add the link from above 
   * Once created -> Open Jupyter
   * Run each cell
   * Replace the db credentials with your RDS PostgreSQL credentials

### PART 4: RAG with Amazon Bedrock, PostgreSQL, and PGVector 
* [Main Workshop Link](https://ws-assets-prod-iad-r-iad-ed304a55c2ca1aee.s3.us-east-1.amazonaws.com/31d6eec7-687f-495c-8ad6-aa7f38c803fb/pgvector/index.html)
* [RAG Notebook](https://catalog.workshops.aws/pgvector/en-US/4-retrieval-augmented-generation/question-answering-using-amazon-bedrock-llms/task2)

### PART 5: CLEAN-UP

* (Delete the EC2 ans DB instance)[https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_GettingStarted.CreatingConnecting.PostgreSQL.html#CHAP_GettingStarted.Deleting.PostgreSQL]

### Resources 
* [PGAdmin 4 Download](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_ConnectToPostgreSQLInstance.html#USER_ConnectToPostgreSQLInstance.pgAdmin)
* [PGVector](https://github.com/pgvector/pgvector)



