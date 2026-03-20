## Careplus: End-to-End AWS Data Engineering Project

## 📌 Project Overview
This project demonstrates a fully automated, event-driven data engineering pipeline designed to process customer support data for a fictitious company, "Careplus". The pipeline ingests transactional support tickets and semi-structured system logs, transforms the data using serverless AWS services, and serves it through a robust data warehouse for business intelligence and ad-hoc analytics. 

Thank you to Codebasics.
* ![Project Plan](arch.PNG)

*(Note: Due to confidentiality and proprietary constraints, the underlying source code, ingestion scripts, and DDL statements are not published in this repository.)*

## 🏗️ Architecture & Tech Stack
* **Source Systems:** MySQL (OLTP), Log Servers
* **Data Ingestion:** Python
* **Data Lake (Storage):** Amazon S3 (Raw & Processed Zones)
* **Processing / ETL:** AWS Lambda, AWS Glue
* **Ad-hoc Analytics:** Amazon Athena
* **Data Warehouse:** Amazon Redshift
* **Data Visualization:** Power BI

## 🛠️ Skills Demonstrated
* **Data Lake Architecture:** Designing Amazon S3 storage zones (Raw and Processed) for efficient data lifecycle management.
* **Serverless ETL Processing:** Building scalable, event-driven data transformations using AWS Lambda and AWS Glue.
* **Log Parsing & Data Extraction:** Utilizing Python and regular expressions to efficiently extract and structure key-value metrics from semi-structured server logs.
* **Cloud Data Warehousing:** Automating bulk data ingestion into Amazon Redshift using optimized `COPY` commands.
* **Event-Driven Orchestration:** Configuring S3 Event Notifications to seamlessly trigger pipeline stages without the need for external scheduling tools.
* **Data Visualization & Analytics:** Connecting Power BI directly to the data warehouse to build interactive dashboards that monitor support metrics and system health.

## 🗄️ Data Sources
The pipeline integrates two primary data streams:
1. **Support Tickets (MySQL):** Transactional data exported as `.csv` containing ticket details such as `ticket_id`, `created_at`, `resolved_at`, `agent`, `priority`, `num_interactions`, `IssUeCat`, `channel`, and `status`.
   ![OLTP_DATA - support tickets](sql_.png)
3. **Support Logs (Text/Log Files):** Server logging data capturing system health and user sessions. Key attributes extracted include timestamps, log levels (INFO, DEBUG), `TicketID`, `SessionID`, `IP` address, `ResponseTime`, `CPU` utilization, and `UserAgent` strings.
   ![OLTP_DATA - support logs](log_.png)

## ⚙️ Pipeline Workflow

### 1. Data Ingestion
* A custom Python script connects to the MySQL database and log servers to extract the latest data.
* The extracted ticket data is saved in `.csv` format. 
* This data is directly ingested into the **RAW folder** of an Amazon S3 bucket.

### 2. Event-Driven Orchestration & ETL
The transformation layer is entirely automated. As soon as a file lands in S3, a Put event triggers the respective processing service based on prefix and suffix filters:
* **Ticket Data Processing:** An AWS Lambda function cleans and transforms the `.csv` ticket data, converting it into a highly optimized, BI-ready `.parquet` format.
* **Log Data Processing:** AWS Glue handles the transformations to parse key-value metrics from the text, structure the data, and output it as `.parquet` files.
* Both processed datasets are subsequently saved into the **processed folder** of the S3 bucket.
  ![event_driven](event_driven.PNG)

### 3. Storage & Analytics
* **Ad-Hoc Querying:** Amazon Athena is used for adhoc analysis against the `.parquet` files.
* **Automated Data Warehousing:** Incremental loading into the Data Warehouse is automated. Dedicated Lambda functions execute the standard Redshift COPY command to bulk-load the processed data from S3 into **Amazon Redshift**.

### 4. Data Visualization
Amazon Redshift is connected to a **Power BI** dashboard. The dashboards provide critical business metrics:
* **Ticket Insights:** Tracks total tickets, resolution rates, average interactions, and average resolution minutes broken down by priority, agent, and issue category.
  ![event_driven](support_tickets_viz.PNG)
* **Log Analytics:** Monitors system health, displaying average CPU usage, total logs by user agent, and log levels to correlate system performance.
  ![event_driven](support_logs_viz.PNG)

## 📝 Project Summary
The Careplus data pipeline successfully demonstrates the ability to architect and deploy a fully serverless, event-driven data engineering solution on AWS. By automating the ingestion, transformation, and warehousing of both structured transactional support tickets and semi-structured system logs, the project transforms raw operational data into actionable business intelligence. This architecture minimizes operational overhead while ensuring data is consistently clean, structured, and readily available for analysis.
