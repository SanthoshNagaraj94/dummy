# Setup and Configuration Documentation for the Project

## Overview
This document provides step-by-step instructions for setting up and running the Home Purchase Lead Scoring project using Docker or a Python virtual environment (venv) on macOS, Linux, and Windows. It includes details on installing dependencies, configuring the environment, running the training script, and serving the trained model.

---

## 1. Setup and Working

### 1.1 Using Docker

#### 1.1.1 Build the Docker Image
To create a Docker image, use the following command:
```bash
docker build --no-cache --build-arg req_file=./requirements.txt -t ml_train .
```
Here:
- `--no-cache`: Ensures a fresh build.
- `--build-arg req_file=./requirements.txt`: Specifies the dependencies file.
- `-t ml_train`: Tags the image as `ml_train`.

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

Explanation:
- **Port Mapping (`-p 8888:8888`)**: Maps Jupyter or other services running in the container to the host.
- **Volume Mounts (`-v`)**:
  - Mounts AWS credentials for access to AWS resources.
  - Mounts the project directory for accessing scripts and configuration.
- **Environment Variables (`--env-file ./.env`)**: Reads configurations like dates and file paths.
- **Script Execution (`python Train.py`)**: Executes the training script with specified arguments.

#### 1.1.3 Environment Variables in `.env`
Your `.env` file should contain:
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
Install the required Python packages:
```bash
pip install -r requirements.txt
```

#### 1.2.3 Run the Training Script
Run the script directly:
- **macOS/Linux/Windows:**
  ```bash
  python Train.py --start_date 2024-04-26 --end_date 2024-07-19 --config_file config/FeatureList.yaml
  ```

Arguments:
- `--start_date`: Start date for the data.
- `--end_date`: End date for the data.
- `--config_file`: Path to the YAML configuration file.

---

## 2. Outputs
The training process logs the following to MLflow:
1. **Model**: The trained machine learning pipeline.
2. **Metadata**: Tags like `Start_Date`, `End_Date`, and `Trained_Date`.
3. **Artifacts**: Preprocessing steps, configuration files, and model parameters.

---

## 3. Model Serving
After training, you can serve the trained model using MLflow:
```bash
mlflow models serve -m models:/custom_model/Production -p 5001
```
Replace `Production` with the correct stage or model version.

---

### Notes on Logging and Debugging
- Logs are output to the console during training and contain information about:
  - Pipeline creation
  - Data processing
  - Errors or warnings for debugging
