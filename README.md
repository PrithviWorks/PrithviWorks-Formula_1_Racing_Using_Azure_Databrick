# Formula_1_Racing_Using_Azure_Databricks

## Project Overview:
This project aims to develop a data analysis system for Formula 1 race outcomes using Azure Databricks. It includes creating an ETL (Extract, Transform, Load) pipeline to gather Formula 1 racing data from ergast.com, a site dedicated to Formula 1 statistics, and then processing and storing it in Azure Data Lake Gen2. Data transformation and analysis are performed with Azure Databricks, while Azure Data Factory manages the entire workflow.

## Formula1 Overview
Formula 1 (F1) represents the pinnacle of single-seater auto racing globally, overseen by the FIA. It features cutting-edge cars with hybrid engines. The F1 season is annual, with races spanning a weekend, usually from Friday to Sunday, at various international circuits. Each race involves 10 teams, each with two drivers.

The F1 season generally includes 20 to 23 races, known as Grands Prix. Safety is crucial, with stringent rules and continuous technological improvements to protect drivers and fans. Pit stops are frequent, allowing teams to change tires and adjust cars.

Qualifying rounds on Saturdays set the starting grid for Sunday's race. Races consist of a variable number of laps, typically between 50 and 70, depending on the circuit's length. Pit stops are available for tire changes or car adjustments.

Race results determine driver and constructor standings. The driver leading the standings at season's end is named the drivers' champion, while the top team becomes the constructors' champion.



## Solution Architecture for the Problem Statement

![ov4](https://github.com/PrithviWorks/PrithviWorks-Formula_1_Racing_Using_Azure_Databrick/blob/main/Section-9/ov4.jpeg)

## ER Diagram
The structure of the database is shown in the following ER Diagram and explained in the [Database User Guide](http://ergast.com/docs/f1db_user_guide.txt)
![ER](https://github.com/PrithviWorks/PrithviWorks-Formula_1_Racing_Using_Azure_Databrick/blob/main/Section-9/formula1_ergast_db_data_model.png)

## Working Plan

### Source Data

We are referring to open-source data from the website Ergast Developer API. Data was available from 1950 till 2022.

| File Name     | File Type                |
|---------------|--------------------------|
| Circuits      | CSV                      |
| Races         | CSV                      |
| Constructors  | Single Line JSON         |
| Drivers       | Single Line Nested JSON  |
| Results       | Single Line JSON         |
| PitStops      | Multi Line JSON          |
| LapTimes      | Split CSV Files          |
| Qualifying    | Split Multi Line JSON Files |



### Execution Overview:

- Azure Data Factory (ADF) plays a crucial role in managing the execution and monitoring of Azure Databricks notebooks. Our workflow involves importing data from the Ergast API and storing it in Azure Data Lake Storage Gen2 (ADLS). Initially, the raw data is placed in the Bronze zone, which serves as a landing zone.

- To process the data in the Bronze zone, we use an Azure Databricks notebook. This notebook is responsible for transforming the data into delta tables using an upsert operation. Once this transformation is complete, ADF takes charge of moving the processed data to the ADLS Silver zone, which functions as a standardization zone.

- In the Silver zone, the ingested data undergoes further transformation through an Azure Databricks SQL notebook. This involves operations such as joining and aggregating tables to prepare the data for analytical and visualization purposes. Ultimately, the results of these transformations are loaded into the Gold zone, which serves as the analytical zone for further analysis and reporting.

###  ETL pipeline:
ETL flow comprises two parts:

- Ingestion: Process data from Bronze zone to Silver zone
- Transformation: Process data from Silver zone to Gold zone

In the first pipeline, data stored in JSON and CSV format is read using Apache Spark with minimal transformation saved into a delta table. The transformation includes dropping columns, renaming headers, applying schema, and adding audited columns (ingestion_date and file_source) and file_date as the notebook parameter. This serves as a dynamic expression in ADF.

In the second pipeline, Databricks SQL reads preprocessed delta files and transforms them into the final dimensional model tables in delta format. Transformations performed include dropping duplicates, joining tables using join, and aggregating using a window.

ADF is scheduled to run every Sunday at 10 PM and is designed to skip the execution if there is no race that week. We have another pipeline to execute the ingestion pipeline and transformation pipeline using file_date as the parameter for the tumbling window trigger.

![adf](https://github.com/PrithviWorks/PrithviWorks-Formula_1_Racing_Using_Azure_Databrick/blob/main/Section-22/analysis/Execute%20Pipeline.png)

### Azure Resources Used for this Project:
- Azure Data Lake Storage
- Azure Data Factory
- Azure Databricks
- Azure Key Vault

## Project Requirements:
The requirements for this project are broken down into six different parts which are

#### 1. Data Ingestion Requirements

- Ingest all 8 files into Azure data lake.
- Ingested data must have the same schema applied.
- Ingested data must have audit columns.
- Ingested data must be stored in columnar format (i.e., parquet).
- We must be able to analyze the ingested data via SQL.
- Ingestion Logic must be able to handle the incremental load.

#### 2. Data Transformation Requirements

- Join the key information required for reporting to create a new table.
- Join the key information required for analysis to create a new table.
- Transformed tables must have audit columns.
- We must be able to analyze the transformed data via SQL.
- Transformed data must be stored in columnar format (i.e., parquet).
- Transformation logic must be able to handle the incremental load.

#### 3. Data Reporting Requirements

- We want to be able to know Driver Standings.
- We should be able to know Constructor Standings.

#### 4. Data Analysis Requirements

- Find the Dominant drivers.
- Find the Dominant Teams.
- Visualize the Outputs.
- Create Databricks dashboards.

#### 5. Scheduling Requirements

- Scheduled to run every Sunday at 10 pm.
- Ability to monitor pipelines.
- Ability to rerun failed pipelines.
- Ability to set up alerts on failures

#### 6. Other Non-Functional Requirements

- Ability to delete individual records
- Ability to see history and time travel
- Ability to roll back to a previous version

## Visual Analysis
![1](https://github.com/PrithviWorks/PrithviWorks-Formula_1_Racing_Using_Azure_Databrick/blob/main/Section-22/analysis/Dominant%20Formula%201%20Drivers.png)

![2](https://github.com/PrithviWorks/PrithviWorks-Formula_1_Racing_Using_Azure_Databrick/blob/main/Section-22/analysis/Dominant%20Formula%201%20Drivers%20(2).png)

![3](https://github.com/PrithviWorks/PrithviWorks-Formula_1_Racing_Using_Azure_Databrick/blob/main/Section-22/analysis/Dominant%20Formula%201%20Drivers%20(3).png)

![4](https://github.com/PrithviWorks/PrithviWorks-Formula_1_Racing_Using_Azure_Databrick/blob/main/Section-22/analysis/Dominant%20Formula%201%20Drivers%20(4).png)

![5](https://github.com/PrithviWorks/PrithviWorks-Formula_1_Racing_Using_Azure_Databrick/blob/main/Section-22/analysis/Dominant%20Formula%201%20Drivers%20(5).png)

![6](https://github.com/PrithviWorks/PrithviWorks-Formula_1_Racing_Using_Azure_Databrick/blob/main/Section-22/analysis/Dominant%20Formula%201%20Drivers%20(6).png)

## Tasks performed:
•	Built a solution architecture for a data engineering solution using Azure Databricks, Azure Data Lake Gen2, Azure Data Factory, and Power BI.

•	Created and used Azure Databricks service and the architecture of Databricks within Azure.

•	Worked with Databricks notebooks and used Databricks utilities, magic commands, etc.

•	Passed parameters between notebooks as well as created notebook workflows.

•	Created, configured, and monitored Databricks clusters, cluster pools, and jobs.

•	Mounted Azure Storage in Databricks using secrets stored in Azure Key Vault.

•	Worked with Databricks Tables, Databricks File System (DBFS), etc.

•	Used Delta Lake to implement a solution using Lakehouse architecture.

•	Created dashboards to visualize the outputs.

•	Connected to the Azure Databricks tables from PowerBI.

## Spark (Only PySpark and SQL)
•	Spark architecture, Data Sources API, and Dataframe API.

•	PySpark - Ingested CSV, simple, and complex JSON files into the data lake as parquet files/ tables.

•	PySpark - Transformations such as Filter, Join, Simple Aggregations, GroupBy, Window functions etc.

•	PySpark - Created global and temporary views.

•	Spark SQL - Created databases, tables, and views.

•	Spark SQL - Transformations such as Filter, Join, Simple Aggregations, GroupBy, Window functions etc.

•	Spark SQL - Created local and temporary views.

•	Implemented full refresh and incremental load patterns using partitions.

## Delta Lake
•	Performed Read, Write, Update, Delete, and Merge to delta lake using both PySpark as well as SQL.

•	History, Time Travel, and Vacuum.

•	Converted Parquet files to Delta files.

•	Implemented incremental load pattern using delta lake.

## Azure Data Factory
•	Created pipelines to execute Databricks notebooks.

•	Designed robust pipelines to deal with unexpected scenarios such as missing files.

•	Created dependencies between activities as well as pipelines.

•	Scheduled the pipelines using data factory triggers to execute at regular intervals.

•	Monitored the triggers/ pipelines to check for errors/ outputs.



<h3>Technologies/Tools Used:</h3>
<ul>
  <li>Pyspark</li> 
  <li>Spark SQL</li> 
  <li>Delta Lake</li> 
  <li>Azure Databricks </li> 
  <li>Azure Data Factory</li> 
  <li>Azure Date Lake Storage Gen2</li> 
  <li>Azure Key Vault</li> 
  <li>Power BI</li> 
</ul>  

