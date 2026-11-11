# Healthcare Data Engineering Pipeline – Databricks

## Project Overview

This project demonstrates an end-to-end healthcare data engineering pipeline built using **Databricks, Apache Spark, PySpark, Python and SQL**.

The objective is to process synthetic healthcare data, combine patient referrals and appointment information, analyse waiting times, and recommend patient allocations based on clinical priority and available clinic capacity.

The solution follows the Medallion Architecture (Bronze, Silver and Gold) and includes data quality validation, unit testing, optimisation and CI/CD automation.

All data used in this project is synthetic. No real patient information is used.

## Business Problem

Healthcare providers need to manage patient referrals, appointment availability and waiting times efficiently.

This project addresses the following challenges:

- Combining patient, referral, appointment and clinic capacity data.
- Identifying patients who are waiting for appointments.
- Calculating patient waiting times.
- Prioritising referrals based on urgency and waiting duration.
- Recommending patient allocations based on available clinic capacity.
- Providing reporting datasets for healthcare operational teams.
- Protecting patient-identifiable information through data masking.

## Technology Stack

| Technology | Purpose |
|---|---|
| Databricks | Data engineering platform |
| Apache Spark / PySpark | Distributed data processing |
| Python | Data generation, transformations and testing |
| SQL | Data analysis and reporting |
| Delta Lake | Storage and management of processed data |
| GitHub | Source code management |
| GitHub Actions | CI/CD automation |
| Databricks Workflows | Pipeline orchestration |

## Architecture

The project follows the Medallion Architecture.

```text
          Synthetic Healthcare Data
                     |
                     v
          Synthetic Data Generation
                     |
                     v
              BRONZE LAYER
          Raw healthcare datasets
                     |
                     v
               SILVER LAYER
        Cleansing and transformations
        Patient and referral joins
        Appointment information
        Waiting time calculations
                     |
                     v
                GOLD LAYER
          Reporting and analytics
          Waiting time summaries
          Capacity summaries
          Appointment recommendations
          Unbooked referrals
                     |
                     v
           HEALTHCARE REPORTING
                     |
                     v
           PATIENT OPTIMISATION
        Priority-based allocation
        Clinic capacity matching
                     |
                     v
          DATA QUALITY VALIDATION
             UNIT TESTING
```

GitHub Actions triggers the Databricks workflow when changes are pushed to the `main` branch.

## Data Generation

Synthetic healthcare datasets are generated using Python and PySpark.

The project creates:

- 100 synthetic patient records.
- 100 patient referral records.
- 100 appointment records.
- 3 clinic capacity records.

A fixed random seed is used to make the generated datasets reproducible.

The synthetic datasets contain attributes such as:

- Patient ID
- Age band
- Area code
- Referral ID
- Specialty
- Referral date
- Clinical priority
- Referral status
- Appointment date
- Appointment status
- Clinic location
- Available capacity

No real patient-identifiable data is included.

## Data Pipeline

### 1. Bronze Layer – Raw Ingestion

The Bronze layer stores the ingested synthetic healthcare datasets.

Source datasets include:

- Patients
- Referrals
- Appointments
- Clinic capacity

The data is stored as Delta tables in Databricks.

### 2. Silver Layer – Data Transformation

The Silver layer cleans and combines the raw datasets.

Key activities include:

- Joining patient, referral and appointment datasets.
- Standardising healthcare attributes.
- Handling missing values.
- Calculating waiting days.
- Preparing structured data for reporting and optimisation.

The main Silver table is:

`healthcare_demo.silver.patient_referral_details`

### 3. Gold Layer – Reporting

The Gold layer provides business-ready datasets for reporting and analysis.

Key tables include:

| Table | Purpose |
|---|---|
| `waiting_time_summary` | Waiting time analysis by specialty and priority |
| `capacity_summary` | Clinic capacity reporting |
| `clinic_capacity_summary` | Capacity analysis by clinic |
| `appointment_recommendations` | Patient allocation recommendations |
| `unbooked_referrals` | Referrals awaiting appointments |
| `data_quality_results` | Data quality check results |
| `data_quality_audit` | Data quality audit information |

## Patient Allocation Optimisation

A priority-based allocation process recommends patients for available clinic capacity.

The allocation logic considers:

1. Urgent referrals before routine referrals.
2. Patients with longer waiting times first.
3. Earlier referral dates where priorities and waiting times are equal.
4. Available clinic capacity for each specialty.

Patients are assigned one of two recommendation statuses:

- `Recommended`
- `Waiting for capacity`

The current synthetic dataset demonstrates:

| Allocation status | Patients |
|---|---:|
| Recommended | 19 |
| Waiting for capacity | 17 |
| Total | 36 |

The allocation is a recommendation for operational planning. It does not create or confirm actual patient appointments.

## Data Quality Framework

A dedicated data quality framework validates the healthcare datasets.

The framework executes 17 validation checks, including:

- Duplicate patient and referral identifiers.
- Missing mandatory fields.
- Invalid referral statuses.
- Invalid priority values.
- Invalid specialty values.
- Negative or missing waiting days.
- Invalid referral and appointment date sequences.
- Missing clinic information for recommended patients.
- Capacity validation.

Validation results are stored in:

`healthcare_demo.gold.data_quality_results`

Audit information is stored in:

`healthcare_demo.gold.data_quality_audit`

**Result:** All 17 data quality checks passed against the generated test dataset.

## Unit Testing

A separate unit testing notebook validates the pipeline components.

The 17 tests cover:

- Bronze layer validation.
- Silver transformation validation.
- Gold reporting validation.
- Patient allocation optimisation.

The test results are displayed in Databricks, making it easier to identify failures during development.

**Result:** All 17 unit tests passed.

## Data Security and PII Masking

The project demonstrates basic patient data protection using a masked Gold reporting view.

The view:

`healthcare_demo.gold.patient_referral_details_masked`

Masks patient identifiers and area codes.

A Databricks group named `healthcare_reporting` is configured with reporting permissions to support controlled access to the masked reporting view.

The original Silver data remains separate from the masked reporting view.

Note: Effective access restrictions should be verified using a separate reporting account. A masked view alone does not prevent users with direct access to the underlying Silver table from reading its data.

## Workflow Orchestration

The Databricks job is named:

`healthcare_data_pipeline`

It contains seven tasks:

1. Synthetic data generation.
2. Bronze ingestion.
3. Silver transformation.
4. Gold reporting.
5. Patient optimisation.
6. Data quality validation.
7. Unit testing.

The tasks are orchestrated using Databricks Workflows with task dependencies.

The complete workflow has been executed successfully.

## CI/CD – GitHub Actions

GitHub Actions is configured to trigger the Databricks job automatically when code is pushed to the `main` branch.

Workflow file:

`.github/workflows/databricks.yml`

The CI/CD process follows this sequence:

1. Developer commits and pushes code to GitHub.
2. GitHub Actions starts automatically.
3. GitHub Actions authenticates with Databricks using repository secrets.
4. The Databricks Jobs API triggers the healthcare pipeline.
5. Databricks executes the seven pipeline tasks.

The workflow also supports manual execution through `workflow_dispatch`.

Credentials are stored as GitHub repository secrets rather than hardcoded in the source code.

## Project Structure

The repository contains the following key notebooks:

| Notebook | Description |
|---|---|
| `01_generate_synthetic_data` | Generates synthetic healthcare datasets |
| Bronze ingestion notebook | Loads raw datasets into Bronze |
| Silver transformation notebook | Cleans and joins datasets |
| Gold reporting notebook | Creates reporting tables |
| Optimisation notebook | Recommends patient allocations |
| `05_data_quality_framework` | Executes data quality checks |
| `06_unit_tests` | Runs unit tests |

The GitHub Actions workflow is maintained under:

`.github/workflows/databricks.yml`

## How to Run the Project

1. Clone the GitHub repository.
2. Open the project in Databricks using Git folders.
3. Configure the required Databricks compute and catalog permissions.
4. Run the synthetic data generation notebook.
5. Execute the Bronze ingestion notebook.
6. Execute the Silver transformation notebook.
7. Execute the Gold reporting notebook.
8. Run the patient optimisation notebook.
9. Execute the data quality framework.
10. Execute the unit testing notebook.
11. Run the `healthcare_data_pipeline` Databricks job to execute the complete workflow.

To enable GitHub Actions integration, configure the following repository secrets:

- `DATABRICKS_HOST`
- `DATABRICKS_TOKEN`
- `DATABRICKS_JOB_ID`

Never commit access tokens or credentials to the repository.

## Future Enhancements

Potential improvements include:

- Incremental data processing using Delta Lake.
- Historical tracking of referral and appointment changes.
- More advanced clinic allocation across multiple locations.
- Distance-based patient allocation.
- Appointment slot and scheduling optimisation.
- Automated alerts for data quality failures.
- Data lineage and pipeline monitoring.
- Integration with Power BI or other reporting platforms.
- Automated CI/CD validation and deployment.
- Predictive analytics for waiting time forecasting.

## Key Skills Demonstrated

- Databricks and Apache Spark.
- PySpark and Python.
- SQL and Delta Lake.
- Medallion Architecture.
- ETL/ELT pipeline development.
- Healthcare data modelling.
- Data quality engineering.
- Unit testing.
- Patient allocation optimisation.
- Data security and PII masking.
- Workflow orchestration.
- GitHub and GitHub Actions CI/CD.

## Disclaimer

This is an educational portfolio project built using synthetic healthcare data. It is not connected to real healthcare systems and must not be used for clinical decision-making or actual patient allocation.
