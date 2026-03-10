
📌 **Project Overview**

This project demonstrates an end-to-end data engineering pipeline built using **Microsoft Azure services and Apache Spark.

The pipeline ingests raw data from an external source, processes it using PySpark and Spark SQL, and stores the transformed data in a data lake using a Medallion Architecture (Bronze → Silver → Gold). The processed data is then queried using serverless SQL and visualized in Microsoft Power BI.

This project simulates a real-world cloud data engineering workflow, including:
1. Data ingestion
2. Data transformation
3. Data storage in a scalable data lake
4. Querying with serverless SQL
5. Data visualization

🎥 **Project Reference**

This project was built by following the tutorial:
https://www.youtube.com/watch?v=0GTZ-12hYtU
The tutorial demonstrates how to build a complete Azure SparkSQL pipeline using Databricks and cloud storage.

🏗️ **Architecture**

Typical workflow of the pipeline:

Data Source > Azure Data Lake Storage > Azure Databricks(PySpark / SparkSQL) > Processed Data(Silver / Gold Layer) > Analytics / Querying

<img width="561" height="245" alt="image" src="https://github.com/user-attachments/assets/5dbf6dde-5efb-46c4-88f7-5de3dcf6597a" />

**Architecture Layers:**

1. Raw Layer (Bronze) - Stores raw ingested data.
2. Processed Layer (Silver) -Data cleaning and Data transformation.
3. Curated Layer (Gold) - Analytics-ready datasets and Aggregated tables.

⚙️ **Tech Stack**

1. Microsoft Azure
2. Azure Data Factory
3. Azure Data Lake Storage Gen2
4. Azure Databricks
5. Apache Spark
6. Spark SQL
7. Azure Synapse Analytics
8. Microsoft Power BI

**Implementation Steps**

This section describes the step-by-step implementation of the pipeline.

**Azure Setup**

1.Login to the Microsoft Azure Portal.
2. Create a Resource Group to organize all resources used in this project.

**Create Data Lake Storage**

1. Create a Storage Account and enable Hierarchical Namespace to use it as a Data Lake.
2. Inside the storage account, create three containers representing the Medallion Architecture:

   1. Bronze → Raw data
   2. Silver → Cleaned and transformed data
   3. Gold → Analytics-ready data

This structure helps implement a scalable data lakehouse architecture.

**Create Data Ingestion Pipeline**

1. Create Azure Data Factory.
2. Open Azure Data Factory Studio and create the following Linked Services:
   1. HTTP (for external data source)
   2. Azure Data Lake Storage Gen2
4. Create a Copy Data Pipeline to ingest data into the Bronze container.

There are two approaches to copy data from the source:
1. Static Copy - Hardcode the Relative URL of the data source.
2. Dynamic Copy - Use pipeline parameters instead of hardcoding the Relative URL.

<img width="940" height="454" alt="image" src="https://github.com/user-attachments/assets/54330490-1207-4c42-ac59-64e1c1c30049" />
<img width="940" height="404" alt="image" src="https://github.com/user-attachments/assets/0e6c5a70-468b-4434-a890-6d37ae336419" />
<img width="940" height="728" alt="image" src="https://github.com/user-attachments/assets/a48b0205-4bf9-4c4d-a4e0-c88eb2dd2601" />
<img width="940" height="262" alt="image" src="https://github.com/user-attachments/assets/d4030f0e-0c86-48c0-a0b2-29a410b898fe" />


4. Add the following activities:
   1. Lookup Activity → Retrieves metadata or dataset information
   2. ForEach Activity → Iterates through files dynamically
   3. This enables dynamic ingestion of multiple datasets.

<img width="940" height="328" alt="image" src="https://github.com/user-attachments/assets/e82f6c92-18ff-4c28-af6a-bbbab871367b" />


**Data Processing with Databricks**

1. Create Azure Databricks.
2. Create a Compute Cluster to run Spark workloads.
3. Register an Azure Active Directory Application to enable secure access to the storage account.
4. Assign the role: Storage Blob Data Contributor - This allows read/write access to Azure Data Lake.

**Data Transformation**

1. Create a PySpark Notebook in Databricks.
2. Configure connection details to connect Azure Data Lake Storage with Databricks.

Example tasks performed in PySpark:
    1. Read data from Bronze container
    2. Perform data cleaning
    3. Apply data transformations
    4. Write transformed data into the Silver container

**Data Warehouse Layer**

1. Create Azure Synapse Analytics Workspace.
2. Add the Managed Identity of Synapse to the Storage Account for data access.
3. Open Synapse Studio and create a Serverless SQL Database.
4. Create a schema named: gold
5. Query Data from Data Lake - Use OPENROWSET to query data directly from the Data Lake.

Example:

SELECT *
FROM OPENROWSET(
    BULK 'https://<storage-account>.dfs.core.windows.net/silver/',
    FORMAT = 'PARQUET'
) AS result

6. Create a View and store it inside the Gold Schema.
7. Create the following objects in Synapse:
   1. Master Key
   2. Database Scoped Credential using Managed Identity
   3. External Data Source
   4. Bronze container path
   5. Gold container path
   6. External Data Format(PARQUET and Compression enabled)

9. Create an External Table in the Gold Schema for analytics queries.

**Data Visualization**

1. Install Microsoft Power BI.
2. Connect Power BI to Synapse:
   1. Select Get Data
   2. Choose Azure → Azure Synapse Analytics SQL
   3. Enter the SQL Endpoint from Synapse workspace.
   4. Load the required tables and columns.
   5. Create interactive dashboards and reports.
