# LeadClass - HomePurchase  
Codebase for training and evaluating LeadPoint's HomePurchase Lead Scoring models in Python using Pandas, Scikit-Learn, and MLFlow.

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

This repository contains the code for training and evaluating LeadPoint's **HomePurchase Lead Scoring Models** using:  
- **Python Libraries**: Pandas, Scikit-Learn, and MLFlow.  
- **Docker**: For containerized execution.  

---

## 2. [Installation](#table-of-contents)  

### 2.1 [Cloning the Codebase](#table-of-contents)  

Clone the repository using the following command:  

```bash
git clone https://shivashankar.rampur@stash.leadpointcorp.net/scm/lpml/ml_leadclass_purchase.git
```

---

### 2.2 [Creating Docker Environment](#table-of-contents)  

The Docker environment ensures consistent and reproducible execution across different platforms.

#### 2.2.1 [For macOS/Linux](#table-of-contents)  

**Build the Docker Image:**  

```bash
docker build --no-cache --build-arg req_file=./requirements.txt -t ml_train .
```

**Run the Docker Image:**  

```bash
docker run -it \
  -p 8888:8888 \
  -v ~/.credentials:/root/.credentials \
  -v ~/.aws:/root/.aws \
  -v $(pwd):/app \
  --env-file ./.env \
  --name newhome \
  --rm ml_train \
  python Train.py \
  --start_date "$(grep START_DATE .env | cut -d '=' -f2-)" \
  --end_date "$(grep END_DATE .env | cut -d '=' -f2-)" \
  --config_file "$(grep CONFIG_FILE .env | cut -d '=' -f2-)"
```

**Key Points:**  
- `$(pwd)` dynamically resolves the current directory path.  

---

#### 2.2.2 [For Windows](#table-of-contents)  

**Build the Docker Image:**  

```powershell
docker build --no-cache --build-arg req_file=./requirements.txt -t ml_train .
```

**Run the Docker Image:**  

```powershell
docker run -it `
  -p 8888:8888 `
  -v $HOME\.credentials:/root/.credentials `
  -v $HOME\.aws:/root/.aws `
  -v ${PWD}:/app `
  --env-file .\.env `
  --name newhome `
  --rm ml_train `
  python Train.py `
  --start_date "$(Get-Content .env | Select-String 'START_DATE' | ForEach-Object { $_ -replace 'START_DATE=', '' })" `
  --end_date "$(Get-Content .env | Select-String 'END_DATE' | ForEach-Object { $_ -replace 'END_DATE=', '' })" `
  --config_file "$(Get-Content .env | Select-String 'CONFIG_FILE' | ForEach-Object { $_ -replace 'CONFIG_FILE=', '' })"
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
START_DATE=DATE_SUB(NOW(), INTERVAL 165 DAY) 
END_DATE=DATE_SUB(NOW(), INTERVAL 80 DAY)
CONFIG_FILE=FeaturesList-6.3.yaml
MLFLOW_TRACKING_URI=http://app02b-mp.aws.stage.leadpointcorp.net:5020
AWS_SECRET_ACCESS_KEY=
AWS_ACCESS_KEY_ID=
```

---

## 4. [Outputs](#table-of-contents)  

The training process generates:  
- **Model**: The trained machine learning pipeline stored in MLflow.  
- **Metadata**: Tags such as `Start_Date`, `End_Date`, and `Trained_Date`.  
- **Artifacts**: Includes preprocessing steps, configuration files, and model parameters.  
