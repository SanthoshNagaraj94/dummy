# Home Purchase Lead Scoring project Documentation

## Table of Contents
1. [Overview](#overview)
2. [Setup and Working](#setup-and-working)
   - [Using Docker](#using-docker)
     - [Build the Docker Image](#build-the-docker-image)
     - [Run the Docker Container](#run-the-docker-container)
     - [Environment Variables in .env](#environment-variables-in-env)
   - [Using Python Virtual Environment (venv)](#using-python-virtual-environment-venv)
     - [Create a Virtual Environment](#create-a-virtual-environment)
     - [Install Dependencies](#install-dependencies)
     - [Run the Training Script](#run-the-training-script)
3. [Outputs](#outputs)
4. [Model Serving](#model-serving)
5. [Logging and Debugging](#logging-and-debugging)

---

## Overview

This document provides comprehensive instructions for setting up and running the Home Purchase Lead Scoring project. It includes two methods for setup:

1. **Using Docker**: A containerized environment for consistent execution.
2. **Using Python Virtual Environment (venv)**: A local setup for flexibility and lightweight development.

Learn how to install dependencies, configure the environment, run training scripts, and serve trained models.

---

## 1. Setup and Working

### 1.1 Using Docker

#### 1.1.1 Build the Docker Image

To create a Docker image, use the following command:

```bash
docker build --no-cache --build-arg req_file=./requirements.txt -t ml_train .
```

**Explanation:**
- `--no-cache`: Ensures a clean build by not using cached layers.
- `--build-arg req_file=./requirements.txt`: Specifies the path to the dependencies file.
- `-t ml_train`: Tags the Docker image as `ml_train`.

#### 1.1.2 Run the Docker Container

Run the Docker container to execute the training script:

- **macOS/Linux:**

```bash
docker run -it \
  -p 8888:8888 \
  -v ~/.credentials:/root/.credentials \
  -v ~/.aws:/root/.aws \
  -v $(pwd):/app \
  --env-file ./.env \
  --name homepurchase_train \
  --rm ml_train \
  python Train.py \
  --start_date "$(grep START_DATE .env | cut -d '=' -f2-)" \
  --end_date "$(grep END_DATE .env | cut -d '=' -f2-)" \
  --config_file "$(grep CONFIG_FILE .env | cut -d '=' -f2-)"
```

- **Windows (PowerShell):**

```powershell
docker run -it `
  -p 8888:8888 `
  -v $HOME\.credentials:/root/.credentials `
  -v $HOME\.aws:/root/.aws `
  -v ${PWD}:/app `
  --env-file .\.env `
  --name homepurchase_train `
  --rm ml_train `
  python Train.py `
  --start_date "$(Get-Content .env | Select-String 'START_DATE' | ForEach-Object { $_ -replace 'START_DATE=', '' })" `
  --end_date "$(Get-Content .env | Select-String 'END_DATE' | ForEach-Object { $_ -replace 'END_DATE=', '' })" `
  --config_file "$(Get-Content .env | Select-String 'CONFIG_FILE' | ForEach-Object { $_ -replace 'CONFIG_FILE=', '' })"
```

**Key Details:**
- **Port Mapping (`-p 8888:8888`)**: Links container and host ports for Jupyter or other services.
- **Volume Mounts (`-v`)**:
  - Mounts AWS credentials for resource access.
  - Mounts project files to the container.
- **Environment Variables (`--env-file ./.env`)**: Passes necessary configurations like dates and file paths.
- **Script Execution (`python Train.py`)**: Executes the training script with arguments.

#### 1.1.3 Environment Variables in `.env`

The `.env` file must include:

```env
START_DATE=2024-04-26
END_DATE=2024-07-19
CONFIG_FILE=config/FeatureList.yaml
```

---

### 1.2 Using Python Virtual Environment (venv)

#### 1.2.1 Create a Virtual Environment

To create and activate a virtual environment:

- **macOS/Linux:**

```bash
python3 -m venv venv
source venv/bin/activate
```

- **Windows (Command Prompt):**

```cmd
python -m venv venv
venv\Scripts\activate
```

#### 1.2.2 Install Dependencies

Install the required Python libraries:

```bash
pip install -r requirements.txt
```

#### 1.2.3 Run the Training Script

Run the script directly:

```bash
python Train.py --start_date 2024-04-26 --end_date 2024-07-19 --config_file config/FeatureList.yaml
```

**Arguments:**
- `--start_date`: Start date for the data.
- `--end_date`: End date for the data.
- `--config_file`: Path to the YAML configuration file.

---

## 2. Outputs

The training process logs the following to MLflow:

1. **Model**: The trained machine learning pipeline.
2. **Metadata**: Tags like `Start_Date`, `End_Date`, and `Trained_Date`.
3. **Artifacts**: Includes preprocessing steps, configuration files, and model parameters.

---

## 3. Model Serving

Serve the trained model using MLflow:

```bash
mlflow models serve -m models:/custom_model/Production -p 5001
```

Replace `Production` with the desired stage or version of the model.

---

## 4. Logging and Debugging

Logs are output to the console during training, with details about:

- **Pipeline Creation**: Logs for each stage of the pipeline.
- **Data Processing**: Tracks steps from preprocessing to model training.
- **Errors and Warnings**: Helps in identifying issues and debugging configurations.
