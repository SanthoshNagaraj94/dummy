# LeadClass - Refinance (RIFI)  
Codebase for training and evaluating LeadPoint's **Refinance (RIFI) Lead Scoring Models** in Python using Pandas, Scikit-Learn, and MLFlow.

---

## Table of Contents  

1. [Overview](#overview)  
2. [Installation](#installation)  
   - [Cloning the Codebase](#cloning-the-codebase)  
   - [Creating Docker Environment](#creating-docker-environment)  
     - [For macOS/Linux](#for-macoslinux)  
     - [For Windows](#for-windows)  
3. [Environment Variables in `.env`](#environment-variables-in-env)  
4. [Outputs](#outputs)  

---

## 1. [Overview](#table-of-contents)  

This repository contains the code for training and evaluating LeadPoint's **Refinance (RIFI) Lead Scoring Models** using:  
- **Python Libraries**: Pandas, Scikit-Learn, and MLFlow.  
- **Docker**: For containerized execution.  

---

## 2. [Installation](#table-of-contents)  

### 2.1 [Cloning the Codebase](#table-of-contents)  

Clone the repository using the following command:  

```bash
git clone https://shivashankar.rampur@stash.leadpointcorp.net/scm/lpml/ml_leadclass_refi.git
```

---

### 2.2 [Creating Docker Environment](#table-of-contents)  

The Docker environment ensures consistent and reproducible execution across different platforms.

#### 2.2.1 [For macOS/Linux](#table-of-contents)  

**Build the Docker Image:**  

```bash
docker build --no-cache --build-arg req_file=./requirements.txt -t ml_train_refi .
```

**Run the Docker Image:**  

```bash
docker run -it \
  -p 8888:8888 \
  -v ~/.credentials:/root/.credentials \
  -v ~/.aws:/root/.aws \
  -v $(pwd):/app \
  --env-file ./.env \
  --name refinance \
  --rm ml_train_refi \
  python Train.py \
  --Start_Date_P "$(grep Start_Date_P .env | cut -d '=' -f2-)" \
  --End_Date_P "$(grep End_Date_P .env | cut -d '=' -f2-)" \
  --Start_Date_N "$(grep Start_Date_N .env | cut -d '=' -f2-)" \
  --End_Date_N "$(grep End_Date_N .env | cut -d '=' -f2-)" \
  --config_file_name "$(grep config_file_name .env | cut -d '=' -f2-)"
```

**Key Points:**  
- `$(pwd)` dynamically resolves the current directory path.  

---

#### 2.2.2 [For Windows](#table-of-contents)  

**Build the Docker Image:**  

```powershell
docker build --no-cache --build-arg req_file=./requirements.txt -t ml_train_refi .
```

**Run the Docker Image:**  

```powershell
docker run -it `
  -p 8888:8888 `
  -v $HOME\.credentials:/root/.credentials `
  -v $HOME\.aws:/root/.aws `
  -v ${PWD}:/app `
  --env-file .\.env `
  --name refinance `
  --rm ml_train_refi `
  python Train.py `
  --Start_Date_P "$(Get-Content .env | Select-String 'Start_Date_P' | ForEach-Object { $_ -replace 'Start_Date_P=', '' })" `
  --End_Date_P "$(Get-Content .env | Select-String 'End_Date_P' | ForEach-Object { $_ -replace 'End_Date_P=', '' })" `
  --Start_Date_N "$(Get-Content .env | Select-String 'Start_Date_N' | ForEach-Object { $_ -replace 'Start_Date_N=', '' })" `
  --End_Date_N "$(Get-Content .env | Select-String 'End_Date_N' | ForEach-Object { $_ -replace 'End_Date_N=', '' })" `
  --config_file_name "$(Get-Content .env | Select-String 'config_file_name' | ForEach-Object { $_ -replace 'config_file_name=', '' })"
```

**Key Points:**  
- `${PWD}` dynamically resolves the current directory path in PowerShell.  

---

## 3. [Environment Variables in `.env`](#table-of-contents)  

Configure the `.env` file with the following variables:  

```env
PYTHONPATH=/app/imports:/app/train_models:/app/:$PYTHONPATH
py_file=Train.py
py_folder=/app
Start_Date_P=date_sub(now(), INTERVAL 130 DAY)
End_Date_P=date_sub(now(), INTERVAL 18 DAY)
Start_Date_N=date_sub(now(), INTERVAL 55 DAY)
End_Date_N=date_sub(now(), INTERVAL 6 DAY)
config_file_name=FeatureList-5.4.yaml
MLFLOW_TRACKING_URI=http://app02b-mp.aws.stage.leadpointcorp.net:5020
AWS_SECRET_ACCESS_KEY=
AWS_ACCESS_KEY_ID=
```

---

## 4. [Outputs](#table-of-contents)  

The training process generates:  
- **Model**: The trained machine learning pipeline stored in MLflow.  
- **Metadata**: Tags such as `Start_Date_P`, `End_Date_P`, `Start_Date_N`, `End_Date_N`, and `Trained_Date`.  
- **Artifacts**: Includes preprocessing steps, configuration files, and model parameters.  

