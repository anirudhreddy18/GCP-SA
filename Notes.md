# GCP

**Login: Credentials created location $HOME/.config/gcloud/application_default_credentials.json**

- gcloud init   -> setup configuration
- gcloud auth application-default login -> install Application default Credentials file
- gloud auth service-account -> use service account to authenticate.
- gcloud config list -> lists all the config ex: dev, test, prod etc.
  
GCP Terminology: Region, Zone, account(user/SA), project

Other CLI tools: gsutil, Kubectl

Cloud Shell: files in home directory $HOME will persist b/w sessions, (scripts, config files etc.)

## Cloud run
* Container to production in seconds
* Fully managed server less platform, Pay per use(CPU, Requests, Networking)
* KNative
* simple container apps, for complicated use Kubernetes

### Usecases
  1. Web API’s, Microservices, Event Driven Apps
  2. Anthos -> run K8s cluster anywhere on Prem , gcp, public cloud or combination
  3. Cloud run for Anthos -> deploy to anthos clusters running on Prem or GCP.**
  4. AWS Equivalent : ECS
  5. Deploy 
      - Docker container
      - From source repo builds

**Type 1**
- Event driven & streaming
- Web apps
- APIS and microservices

**Batch Jobs**
 - DB migration
 - processing files in bucket
 -  send invoices
 - Database query & upload results.

EDA:  - 60+ cloud sources
 - Event Arc
 - Cloud Storage via Cloud Audit Logs
 - Custom apps publishing to Pub/Sub
 
EDA Cloud Run for Anthos -> Equivalent: KNative Serverless functions that run on K8s
 - 60+ cloud sources
 - Cloud Storage via Cloud Audit Logs
 - Custom apps publishing to Pub/Sub
 - Custom Events

## GKE
  - cluster: group of compute engine instances. Need to create a cluster to run workloads
  - master: manages the cluster
  - worker node: all the workloads run here
  - Zonal Cluster: single control plane, run in same zone
  - Multi-zonal cluster: single control plane, but nodes running in multiple zones.
  - Regional Cluster: Replicas of control plane in multiple zones of region. Nodes also run in same zone as control plane. HA
  - Private Cluster: VPC-native cluster. Nodes only have internal IP’s.
  - Alpha cluster: test new K8s features.
  - Ingress: Provide external access to services in cluster. Provides load balancing & SSL.
  - Provides pod & cluster Autoscaling
  - gcloud container clusters get-credentials CLUSTER --region us-central1 --project PROJECT_ID
  - can add more than 1 node pool. Within Node pool, you can configure instance types etc.
 
### Roles
 - Basic -> viewer, owner, admin. Gives read access to all GCP Services etc. Not recommended
 - Predefined -> storageAdmin etc.
 - Custom -> Create a role, add permissions. Example: object.get, object.list

### Service Accounts
 - similar to AWS Roles, you can use Service Accounts to launch VM’s etc. Create a service account, add roles to Service account.
  Usecase1: Use that role when launching a VM. Users wanting to use service account to launch a VM, should have necessary permissions to have access service account. Similar to Sts:AssumeRole.
 - They don’t use passwords, uses Public/private Keys. Automatically rotated
 - Usecase 2:On premise -> GCP(long lived). Create a service account with key, use that key to authenticate. Export Google_application_credentials=/path/to/file. Use client libraries with Application Default Credentials(ADC).
 - Usecase3: on Prem to GCP API(short lived). Uses OAuth2 , Open ID, JWT Tokens. When a member needs elevated role, he can assume service account role. Similar to Deleting resources in EC2, assume role & then delete the instance. Ex2: A service in GCP needs to access another service in Another cloud (Open ID connect tokens).
- Across Projects: In project B, add the service account from Project A and then assign Storage Object Viewer permission on the bucket.
- can be used as identity(user)
- can be used as policy(user can impersonate SA) - similar to AWS Role for elevated access
- can be assigned to Compute Engine to make API calls to other GCP Services (similar to AWS Roles assigned to Lambda/EC2).

## VPC
  - VPC is global & subnets are created per region us-central-1
  - Each project has a default VPC

  ### When creating subnet:
   - Enable private google access -> Allows VM’s to connect to other Google API’s. No NAT Gateway needed, traffic never leaves Google network.
   - Flow logs: troubleshoot any VPC related network Issues
   - Resources with in a VPC can talk to each other using private IP’s. If you want them in separate networks use a separate VPC.
   - by default uses hub & spoke -> it means all VPC can connect to 1 central VPC. NO transitive routing
   - Full Mesh Architecture VPC Peering -> connect all vpcs, doesn’t scale well. N * n-1 /2 connections
   - need hub & spoke with transitive routing -> use Transit Gateway

  ### Connect other VPC’s
   - VPC Network Peering: Different VPC communicating with each other. connect to services using internal IP’s in different projects or across organizations efficiently. Cannot overlap CIDR ranges.    
  - All communication happen in Google network
  - Not accessible from Internet
  - No data transfer charges
  - AWS Transit gateway/Shared VPC -> connect to other VPC’s on GCP & on prem resources using cloud VPN. Ping other services using private IP’s.A host project has parent VPC. Service projects are attached to Child. 
 

  ### On Prem Connections
  1. Cloud VPN -> connect from GCP to onprem resources using VPN.
  2. AWS Direct Connect/Cloud Interconnect = connect on premises resources & cloud. private Network, dedicated Interconnect, partner Interconnect. 
        - connect from GCP to onprem resources on private Network which is fast.
        - connect on perm resources directly to VPC.
  3. Best practices: fall back mechanism use cloudhub VPN & Primary as Interconnect. 

  ### VPC Endpoints
   - AWS Interface endpoints -> GCP Private Service Connect. If no VPC endpoints then you have to go through internet where speed is slow & you pay data coming out of cloud. It’s is a private link that can   improve latency. It uses private link in AWS Network. This is highly scalable compared to peering.
   - AWS Gateway endpoints -> needs to be setup using private Service connect. Traffic never leaves the VPC.
  

  ### Firewall Rules:
  - Ingress rule, egress rules 
  - can be applied within network
  -  they are global, controls ingress & egress traffic & also protects VM instances.
  - same rule applied to all instances, apply tags to specific VM & using a service account.
  - cannot share among other networks (ex: VPC Peering)

  ### AWS Services:
  Interface/gateway endpoints, transit gateways, VPC peering, Direct Connect, NAT Gateways, Internet Gateway, Security Groups, Subnet ACLs
    
## Pub/Sub
 - Event Driven
 - Streaming Analytics (pair with Dataflow & send data to BigQuery)
 - pub/sub + Dataflow -> Streaming data (AWS Kinesis)

## Big Data & Analytics Services
 - Pub/sub
 - BigQuery -> Data warehouse, OLAP, AWS Redshift
 - Google CLoud Studio -> Managed visual analytics Service
 - Dataflow -> data pipelines (stream + batch) use cases, ETL & more
 - Dataproc -> Apache spark & Hadoop clusters
 - Dataprep -> clean & prepare data
 - Datalab -> Explore. & Visualize data on Jupyter notebooks
 - Cloud Composer -> workflow orchestration service across clouds & on premise Data centers
 - data lake -> single platform with combination of solutions for data storage, data management & Analytics.

## Anthos Service Mesh(Istio)
  Sidecar container to implement common Microservice features.
  security, observability, and reliability
  - Mutual TLS with end to end encryption
  - Metrics, logging & tracing
  - Authentication & Authorization
  - Role based Access to other micro services or fine grained control over specific routes
  - A/B, canary release strategies.
  - circuit breakers
  - Service Discovery & load balancing features(Sidecar) 
  - cloud logging & cloud monitoring support.
  
## Event Arc 
  1. Cloud events common specification
  2. Event Provider:
     - Direct: pub/sub, cloud storage, cloud IOT, Cloud Memory storage etc.
     - Indirect: uses audit logs entries(GCE, GKE etc)
  3. Event Destination: Cloud functions, cloud run, GKE, Workflows.

  ### Uses   
  - partner or 3rd party events - create event driven apps
  - schema registry
  - multiple consumers
  - create rules & set consumers
  - Schedulers
  - publish custom events for micro services.

Event Driven Architectures
  * Increases in Traffic
  * durabilty event is not lost when app is down
  * increase availability
  * easily add another consumer without producer worrying
  * Decoupling

## APIGEE
 * Features: Authentication & Authorization, routing/multiple versions, latency, caching, Aggregation, Rate limiting.
 * Design, secure, publish, analyze, monitor, monetize, scale 
 * AWS: AWS API Gateway
 * On premise, google cloud & anywhere
 * similar to Mulesoft/Anypoint studio
 * Auto discovery b/w apps to integrate & connect
 * metrics / monitoring
 * caching with cloud CDN
 * Developer Portals(API Keys etc.)
 * Security policies
 * proxies -> similar to AWS API gateway
 * Assign a public IP Address to backend services, apply policies as proxy
 * After product is available, register as app developer, create api key & then send requests.

## Workflows
  - used for orchestration (mostly synchronous or http). Can also be used for async work processes using SQS & task tokens.
  - choreography uses Event Driven style no step functions/workflows needed.
  - use orchestration within Microservices & uses choreography for interacting with micro services
  - Serverless workflows involve developing microservices using serverless functions. Each micro service can have 1 or more serverless functions that are delayed together. 
  - SAGA rollback transactions in distributed systems.
  - orchestration spring boot:  uses request queue & response queue. Orchestrator sends request & then reads response from another topic/queue.

## API Gateway
  Used for serverless, setup is simple.
  - Cloud functions, cloud run & App Engine
  - Serverless
  - metrics, tracing & logging

## Identity Platform
  Authentication & Authorization for web apps, mobile etc.
  SAML, OIDC, etc.
  AWS : AWS Cognito

## Service Directory
  - Service Discovery for on perm, cloud apps
  - AWS: AWS Cloud Map

## HA
- Cloud SQL: Multi AZ regional with standup in a different zone
- GKE : multi master with regional clusters
- Managed Services: cloud run, cloud function etc.
- Compute/VM’s : global load balancing with instances distributed across multiple regions.
- Global > multi -regional > regional > zonal
- Disk’s: Live resizing, use regional persistent disks
- Cloud Datastore: use multi-region locations
- Network Tier: Premium Network Tier
- Hybrid Connectivity: Dedicated Interconnect > Partner Interconnect > VPN

## Scalability
  - MIG or instance groups - increase VM’s by Autoscaling
  - GKE: increase pods., cluster Autoscaling - increase nodes.
  - All Serverless services support Autoscaling.
  - SQL databases needs to be vertically scaled(cloud SQL, cloud spanner, BigTable, Data proc) not server less.

## Security
  - Cloud KMS - Digital signatures. Public & private key
  - Cloud Armor - DDOS Attacks, OWASP top 10 attacks(XSS, SQL)
  - Secret Manager - store passwords, rotate keys with cloud functions, auditing with cloud Audit logs, Encrypted by default.

## Databases
  OLTP => customer placing an order, managing inventory etc. These transactions are usually short & high volume, ACID characteristics.
  1. Availability -> having standby instances in different zones or regions. Failover to standby when master is not available.
  2. Durability -> standbys, snapshots, transaction logs & replicas in different zones or regions.
  3. RTO - recovery time objective -> max time app can be down
  4. RPO -> recovery point objective -> max data loss 
      - High: hot standby, sync replication, failover to standby
      - Medium: standby min infra, sync replication, failover to standby & increase infrastructure
      - Low: snapshot with transaction logs, create new DB with snapshot
  5. Read replicas -> for analytics applications
  6. Consistency
      - Strong -> master & all replicas are in sync. Slow but useful for financial transactions
      - Eventual -> async replication b/w master & replicas. Ok with twitter, Facebook apps etc. slight delay with few secs
      - Read after Write -> Inserts are immediately available(sync), update use eventual consistency
  7. DB considerations
      - fixed schema or schema less
      - transaction properties like ACID
      - latency
      - how much data will be stored
      - volume level of transactions.
  8. Relational -> OLAP & OLTP, predefined schema, strong transactional capabilities. Most traditional apps, ERP, com, e-commerce, banking apps.
  ### cloud SQL  
  - Structured data, predefined schemas, ACID Transactions, Strong Consistency, read heavy workloads, SQL language.
  - Limitation: Postgres, mysql, Sql Server only regional(no db across regions).
  - Limitation: Up to few TB’s(30TB) of data. 
  - Don’t worry about os patching, backups, Point in time recovery or archives. 
  - Automatic encryption for tables & backups.
  - Automatic storage increase.
  - Also schedule backups. Backups can be replicated across regioons & new DB can be spun up in another region.
  - Use AWS DMS to migrate to gcp
  - Read scalability use read replicas. HA config doesn’t increase scalability. Increase writes use vertical scaling.

  ### Cloud SQL Auth Proxy
  - Architecture: Client app has side car container running called Proxy client, and there is another side car container called proxy client close to DB instance. Communication is secure using SSL/TLS.
  - Use cloud SQL proxy to connect from GKE, GAE, cloud functions etc. 
  - Connection: Public IP(not recommended), Private IP(works with in same network, safe in shared VPC), Cloud SQL Suth Proxy(GCP recommended).
  - Params needed: SQL instance, port, Service Account credentials.

  ### SPANNER  
  - huge volumes TB’s, unlimited scaling ,high availability(99.999 %), global users(DB runs in multiple regions). Uses horizontal scaling.
  - Interleaved tables.
    
  ### OLAP
  Focussed on query, reporting & Analytics. Data sources are OLTP Systems
  - reporting apps, data warehouses, BI apps, Analytics streams
  #### BigQuery   
  - Datasets -> collection of views & tables.
  - Tables -> supports column, stores data in columns, supports scalar(primitive data types) & nested structures(Array, Struct).
  - Views -> can be subset or joining of multiple tables. Can be materialized
  - Virtual Tables -> actual data resides in bigtable, bigquery creates virtual table & let's you query on this data.
  - IAM roles can be applied at dataset, table or view level.
  - Jobs: Data loading or query jobs.
  - Partitions: with partitions, less data to scan. A table is split into multiple partitions. BigQuery charges based on data scanned. 
     Types: Timestamp/date based on custom column, integer based, timebased(default/inbuilt psuedo column)
  - Clustering Tables: used clustered column similar to cassandra again to limit amount of data being scanned. Helpful to add to the where clause & also for aggregation on columns.
  - Load Data: Cloud Storage, Other GCP Services, Stream Data pipelines(Avro preferred), Dataflow(ETL), DML Bulk Loads, local machine etc.
  - Big Query Transfer Service.
  - Row & column level security - if we have sensitive data fields & we dont want users viewing them. Tags need to be created in Data Catalog Svc.

  ### NOSQL 
   1. not only SQL, tradeoff for scalability & high performance, easily scalable to PB’s of data.
   2. Cloud Firestore(Data store) -> 
      - Managed Serverless nosql document DB. 
      - ACID Transactions
      - Indexes, SQL like queries
      - Firestore: strong consistency
      - Small to medium DB’s
   3. Cloud Big Table - wide column DB, not server less. streaming, Large analytical & operational workloads.

 ### Data Engineering
  - ETL Jobs -> Migrate from onprem to bigquery, Sync data b/w two systems.
  - Data Pipelines -> ETL Jobs, Data Enrichment(adding new attributes), real time analysis.
  - ELT -> Extract data & load to staging area in Data warehouse -> Apply Transform & then again load to Data Warehouse or datamart.
  - Analytics: Descriptive -> Analyze real time data& historical data. predictive, predictive & action.
  - cloud data fusion -> code free ETL/ELT pipelines.
  - Cloud composer -> Apache Airflow. DAG workflows
  - Cloud Dataflow - Apache beeam, stream & btach processing. ETL jobs & windowing operations.
  - Cloud DataProc - managed hadoop & spark. ETL/ELT Jobs. Supports stream processing as well. MIgrate from onprem hadoop. Large Scale batch  & stream processing & ML.

 
      
### Load Balancing
 1. Features:
    - distribute load across instances in multiple regions & zones. Enables High Availability
    - health check (resiliency)
    - works with autoscaling
    - global load balancing with single cast IP
 2. Layers:
     - layer 3 IP: transfers bits & bytes
     - layer 4 TCP, TLS, UDP - delivery guarantee
     - layer 7 http, https ,smtp-> application layer Most apps fall under this layer. Applications at layer 7 use layers beneath them. Ex: Https uses TCP layer underneath.
3. Client to LB over internet : TLS/SSL 
4. Load balancer to VM -> internal google network. Http is OK
5. SSL offloading -> client to LB use https & then use http. So offloading load of your VM instances..

 ### Components:
   - Frontend -> IP which clients use to hit LB.  
   - Path -> ALB use based on host or path based routing
   - Backend -> can be MIG, Containers etc. A single backend service can contain multiple backends. 
 
   - can server requests by user Geo. For low latency
   - Can serve requests only from 1 region, if other region is down.
   - Global routing uses premium networking tier.

## Cloud Functions(Lambda)
  Code will used to create container image & then deployed to Cloud Run(V2)
   
* Autoscaling -> new containers get created. When there is an invocation, there will a new container that will spin up & process the request. 3 concurrent requests -> 3 containers. If a container finished processing the request, then existing container can be used.
* Cold start: a new container instance running & copying code will take time. Solution: Have some instances already running like for example 3 (increases cost)
* 2nd Gen Adds important feature: one function can handle multiple concurrent requests. Max 1000.  Check code on how to use concurrency.
* Trigger Bucket, Trigger Http, Trigger Topic, Trigger event filters (in EventARC)

## Cloud KMS
   * Data at rest (stored in Hard drive, DB etc)
   * Data in Transit (data to/from the internet, data copied from on Prem to cloud, app talking to DB)
   * Data in Use (Active Data in non persistent state) data in RAM
   * symmetric -> only one key used for both encryption & decryption
   * Asymmetric -> public & private Key
   * Google managed Key
   * User Managed key -> created in GCP
   * Customer Managed Key -> created on Prem  
 

## IAM And Organizations
  - organization -> Folder(Department(Product Suite) -> Team -> Product) -> Project(Dev, Test, Prod) -> Resources
  - Identities: Users, groups, Service Accounts, External Identities
  - Roles : Assigning Permissions
  - Policies: Binding roles to Identities.
  - User -> Gmail Account
  - Service Account -> Non human accounts
  - Groups -> Collection of Users & Service Accounts.
  - External Identities -> Azure AD Users.
  
*  Always follow minimum privileged required to do job.
* All Resources are created in Projects.
* Separate projects per environment. DEV project, Production Project. Provide Dev’s complete access to DEV project.
* Create separate folders for each department.
* Billing accounts can be setup per Department.
* Least privilege access
* Review cloud audit logs to IAM policies & Service Accounts.
* Store IAM logs in Cloud Buckets for long term storage.
* Use IAM Groups when possible.
* Root User AWS => Super Admin
* Policy Binding -> Mapping between User & Role in a project.
* Federate GCP Account with Identity Provider & single sign on. Azure AD with Cloud Identity 
* Organization policies -> deny/restrict access to all users in all projects. Ex: Deny creation of Service accounts, deny resources in certain regions.
* IAM Policy can be set as organization, folder, Project, resource level.
* You cant restrict policy at lower level, if permission is given at a higher level.
* Sub set of objects in GCS Bucket -> Use ACL’s
* GCP ROLES -> AWS policies
* GCP Policy -> AWS Role Binding
* Roles -> it’s all about permissions. Can have multiple permissions
* Policy -> Assigning role/roles to User. 

## Async Communication 
  * Tools: SQS, Event Bridge, Kinesis/Kafka, S3(payload larger than 256 KB) 
  * Availability -> App is still available, when downstream svc is down
  * Decoupling -> 
  * Scalability. ->
  * Durability. -> Message is not lost,  when downstream svc is down
  **Pub sub** 
  1. Highly available, Highly Scalable. SQS & SNS equivalent. Supports both push & pull
  2. Topic
  3. Subscription 
  4. Usecases
     - Convert from sync to async workflows
     - FIFO ordering
     - Exactly once processing with dataflow
   
  5. Dataflow
     - provides batch data processing & unified streaming 
     - Ex: send data from pub/sub to mongodb, Bigtable, cloud storage etc.
     - Usecases: real time fraud detection, sensor data processing, log data processing, batch processing
     - Based on Apache Beam
     - Serverless & Autoscaling
     - Equivalent services: AWS Kinesis, Kafka
  6. **Scenarios**
     - When an object is uploaded to bucket, directly invoke a cloud function
     - When an object is uploaded to bucket, setup a notification to pub/sub & then respond to that event using Cloud function/cloud run.
     - When an object is uploaded to bucket, directly trigger a cloud run service uses eventArc.   

## Cloud Operations
 1. Cloud Monitoring => prometheus & grafana
    - emit metrics for apps
    - create graphs & dashboards
    - create alerts 
    - Uptime checks - checks whether the app is up or not
    - SLO Monitoring
 2. Cloud Logging  =>(splunk)
    - Dashboard
    - logs metrics
    - logs router 
    - logs explorer
    - Auto for GKE, App Engine, Cloud run.
 3. Cloud Audit Logs => AWS Cloud Trail
    - Admin Activity Logs -> API calls that modify configuration of resources.
    - Data Access logs => not enabled by default. Read configuration of resources.
    - System Event Logs => Google cloud Administrative Actions.
    - Policy Denied Logs => user or SA denied access.
 4. Export/Routing logs
    - Required bucket -> for all logs above, no charge
    - _default bucket -> for application logs, charged based amount of logs, can change retention. For long term retention, use storage bucket or Big Query.
 5. Logs routing sink -> sent logs to other services like cloud storage, big query, pub/sub, splunk etc.
 6. Cloud Trace
   - collect latency data 
   - use traceId to view all the requests across multiple different microservices(use Cloud Trace API)
 7. Cloud Profiler => Used to identify perfomance bottlenecks
 8. Error Reporting => identify & manage top errors or recent errors.

## SRE
  1. SRE focuses on every aspect of application: availability, performance, latency, efficiency, change management, monitoring, emergency response & capacity planning.
 2. Key principles:
   * SLO - measure by availability for example
   * Share ownership with Dev’s - monitoring, emergency response & capacity planning
3. Key Metrics:
  - SLI: quantitative measure of a service - LATTES
  - SLO: take one from LATTES & set a target for it, for example: 99% availability. SLI + target
  - SLA: SLO + consequences(contract) - if SLO is not met, then there will be consequences.
  - Error Budgets: 100% - SLO
4. Best Practices:
  - load shedding/rate limiting
  - cascading failures -> circuit breaker/
  - penetration testing -> ethical hacking to find out vulnerabilities
  - load Testing -> Jmeter
  - Chaos Testing -> when one or more parts of system fail. Add huge stress on layers.

## Object Storage
 1. Low latency on all storage classes
 2. Strorage classes
    * Standard -> frequently used
    * nearline -> once in 30 days
    * coldline -> once a quarter
    * Archive-> once a year
3. Files are automatically on Server side, before storing it on disk
4. in Transit use https
5. Encryption Server side
   - Default - Keys automatically managed by Google
   - Custom Keys -> Keys are managed by user in KMS. GCS SA needs to be assigned permissions to Keys
6. Client Side Encryption
7. File is encrypted on the client side, before uploading

* SA account is associated to GCS storage by default, assign correct permissions to it ex: pub/sub publisher, KMS keys
* Each file contains a metadata, storage class is stored as key value etc. Users can store their own metadata
* Retention policy set it to lock for compliance needs.
* Transfer service is recommended for transferring data more than 1 TB from onprem or anywhere. gsutil for smaller data. Larger data use Physical appliances.
* ACLS controls access to individual objects, IAM is for all objects inside bucket. Access can be granted by IAM or ACL.
* Limited time to access objects without having google accounts - use signed URL
* ACL to control access to bucket & objects. Legacy method also can use IAM
* Lifecycle policies to move b/w different storage options.
* Use gsutil for less than 1TB. Cloud Transfer Svc > 1TB. MOre than 100 or 480 TB use Storage Appliance.

## Releases
  - Recreate : take down V1 & deploy V2. Causes down time.
  - Canary: roll out few instances of V2. Traffic is sent to both V1 & V2 instances. Then rollout V2 to all instances.
  - A/B: Rollout v2 feature to subset of users. Then decide to rollout to all or rollback.
  - Rolling: Update 1 instances at a time or a subset. Do it until all instances are replaced in V2
  - Rolling Batch: Add extra instances & then do a rolling update on each instances 1 at a time or subset. Restart rolling PCF.
  - Blue green: v1 is live, create v2 environment test with checkout url, switch to V2.
  - Shadow testing - mirror traffic from v1 to v2 or capture traffic & sent it to v2 to test with active production traffic.
  - Feature flag: enables true continous delivery by turning on/off, Useful for rollbacks, turn code on/off.  
  GKE: rolling update default, rollout restart for restarting pods, blue green with ingress/service, canary with Istio.

## Splunk Add on
  Pull -> setup sink from logging. Sink will send logs to Pub/sub. Splunk pulls from Pub/sub 
  Push -> setup sink from logging. Sink will send logs to Pub/sub. Dataflow will push to Splunk(similar to kafka Sink)
  
