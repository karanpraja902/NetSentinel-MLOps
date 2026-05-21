# NetSentinel MLOps

NetSentinel MLOps is an end-to-end machine learning operations project for detecting malicious network and phishing activity. It provides a modular pipeline for data ingestion, validation, transformation, model training, experiment tracking, artifact storage, and containerized API deployment.

## Table of Contents

1. [Project Overview](#project-overview)
2. [Key Features](#key-features)
3. [Tech Stack](#tech-stack)
4. [Dataset and Features](#dataset-and-features)
5. [MLOps Pipeline Workflow](#mlops-pipeline-workflow)
6. [CI/CD and AWS Deployment](#cicd-and-aws-deployment)
7. [Getting Started](#getting-started)
8. [Project Structure](#project-structure)

## Project Overview

This project demonstrates a complete ML lifecycle, from raw data ingestion to a production-ready FastAPI service. It includes experiment tracking, data validation with drift detection, custom logging, exception handling, Docker packaging, and AWS deployment support.

The core goal of NetSentinel MLOps is to provide a scalable and maintainable system that can automatically train, evaluate, and serve a machine learning model for identifying malicious network activity.

**Overall Architecture**

![Project Architecture](images/architecture.jpg)

## Key Features

- **Modular architecture**: Reusable components for ingestion, validation, transformation, training, and deployment.
- **End-to-end MLOps pipeline**: Automated workflow from raw data extraction to production-ready model storage.
- **Data validation and drift detection**: Schema checks and Kolmogorov-Smirnov tests help detect data quality issues.
- **Experiment tracking**: MLflow and DagsHub record model parameters, metrics, and artifacts for each run.
- **CI/CD automation**: GitHub Actions builds Docker images, pushes them to AWS ECR, and deploys to AWS EC2.
- **Cloud artifact management**: AWS S3 stores datasets, validation reports, models, and pipeline outputs.
- **REST API interface**: FastAPI exposes `/train` and `/predict` endpoints for retraining and inference.

## Tech Stack

| Category | Tools/Technologies | Description |
| :--- | :--- | :--- |
| Backend and API | FastAPI, Uvicorn | Serves model training and prediction endpoints. |
| Modeling | Scikit-learn, Pandas, NumPy | Supports preprocessing, model training, and evaluation. |
| Database | MongoDB | Stores raw source data for the ingestion pipeline. |
| Experiment Tracking | MLflow, DagsHub | Tracks experiments, metrics, parameters, and artifacts. |
| CI/CD | GitHub Actions | Automates build, delivery, and deployment workflows. |
| Containerization | Docker, AWS ECR | Packages and stores application images. |
| Cloud Storage | AWS S3 | Stores ML artifacts, reports, and trained models. |
| Cloud Hosting | AWS EC2 | Runs the deployed API service and self-hosted runner. |

## Dataset and Features

The model is trained on URL and network-oriented features that capture suspicious structure, domain behavior, and page characteristics. These features help classify whether an observed URL or network signal is benign or malicious.

![URL Features](images/url_features.jpg)

### Key Features Used

| Feature Name | Description |
| :--- | :--- |
| `having_IP_Address` | Checks whether the URL contains an IP address instead of a domain name. |
| `URL_Length` | Measures URL length, since unusually long URLs can be suspicious. |
| `Shortining_Service` | Detects known shortening services such as `bit.ly`. |
| `having_At_Symbol` | Flags the `@` symbol, which can hide the true destination. |
| `SSLfinal_State` | Evaluates SSL certificate validity and trustworthiness. |
| `Domain_registeration_length` | Checks domain registration duration. |
| `web_traffic` | Measures traffic rank to help identify low-reputation sites. |

## MLOps Pipeline Workflow

NetSentinel MLOps is organized as a set of connected pipeline stages, each responsible for one part of the ML lifecycle.

**Pipeline Workflow Overview**

![Pipeline Workflow Overview](images/pipeline_workflow_diagram.png)

### 1. Data Ingestion

Raw data is extracted from MongoDB, split into training and testing sets, and saved as pipeline artifacts.

![Data Ingestion Flow](images/data_ingestion_flow.png)

### 2. Data Validation

The ingested data is validated against `data_schema/schema.yaml`. The validation stage checks column counts, data types, and distribution drift.

![Data Validation Flow](images/data_validation_flow.png)

### 3. Data Transformation

Missing values are handled with `KNNImputer`. The preprocessing pipeline transforms features and stores NumPy arrays for efficient model training.

![Data Transformation Flow](images/data_transformation_flow.png)

### 4. Model Training

Multiple classification models are trained with `GridSearchCV`. Each experiment is tracked with MLflow, and the best model is selected and saved.

![Model Training Flow](images/model_training_flow.png)

### 5. Artifact Management and Cloud Sync

Pipeline artifacts, validation reports, trained models, and preprocessors can be synchronized to AWS S3 for persistence and versioned deployment workflows.

## CI/CD and AWS Deployment

GitHub Actions automates the NetSentinel MLOps deployment workflow.

1. **Continuous Integration**: Runs checks on every push to `main`.
2. **Continuous Delivery**: Builds a Docker image and pushes it to AWS ECR.
3. **Continuous Deployment**: Uses an AWS EC2 self-hosted runner to pull and run the latest image on port `8080`.

## Getting Started

### Prerequisites

- Python 3.8+ and Git
- MongoDB cluster access
- AWS account with programmatic access configured

### Step 1: Clone the Repository

```bash
git clone https://github.com/karanpraja902/NetSentinel-MLOps.git
cd NetSentinel-MLOps
```

### Step 2: Set Up the Environment

Using `uv` is recommended:

```bash
uv sync
```

Alternatively, use `venv` and `pip`:

```bash
python -m venv venv
venv\Scripts\activate
pip install -r requirements.txt
```

### Step 3: Configure Environment Variables

Create a `.env` file in the project root:

```env
MONGO_DB_USERNAME="your-mongodb-username"
MONGO_DB_PASSWORD="your-mongodb-password"
```

### Step 4: Populate the Database

```bash
python push_data.py
```

### Step 5: Run the Project

Execute the training pipeline:

```bash
python test.py
```

Start the FastAPI server:

```bash
uvicorn app:app --host 0.0.0.0 --port 8080
```

Open the interactive API docs at `http://localhost:8080/docs`.

## Project Structure

```text
NetSentinel-MLOps/
|-- .github/workflows/main.yaml
|-- images/
|-- network_security/
|   |-- components/
|   |-- pipeline/
|   |-- entity/
|   |-- constant/
|   |-- cloud/
|   |-- exception/
|   |-- logging/
|   `-- utils/
|-- data_schema/schema.yaml
|-- app.py
|-- Dockerfile
|-- pyproject.toml
`-- requirements.txt
```
