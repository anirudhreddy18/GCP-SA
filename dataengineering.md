 ### Data Engineering
  - Data warehouse: Single source of truth for reporting & analytics data. Aggregate data from multiple sources here. 
  - ETL Jobs -> Migrate from onprem to bigquery, Sync data b/w two systems.
  - Transform can be filtering, enrichment, transform. 
  - Data Pipelines -> ETL Jobs, Data Enrichment(adding new attributes), real time analysis.
  - ELT -> Extract data & load to staging area in Data warehouse -> Apply Transform & then again load to Data Warehouse or datamart.
  - Analytics: Descriptive -> Analyze real time data& historical data. predictive, predictive & action.
  - cloud data fusion -> code free ETL/ELT pipelines.
  - Cloud composer -> Apache Airflow. DAG workflows, ETL Pipelines.
  - Cloud Dataflow - Apache beam, stream & batch processing. Used for ETL jobs & windowing operations. One code base for batch & streaming
  - Cloud DataProc - managed hadoop & spark. Used for ETL/ELT Jobs & train ML Models. Used for large batch processing workloads mostly. Supports stream processing as well(alternate to dataflow). Migrate from onprem hadoop/spark to GCP easily.
  - Multi Cloud => avoid vendor lock in, save cost, use best service. Anthos: can run K8s workloads across multiple Cloud platforms, on premises etc.
  - Data Consistency -> strong consistency, eventual Consistency, read after write consistency.
      
