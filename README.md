# LeadClass - HomePurchase

This repository provides the codebase for training and evaluating **LeadPoint's HomePurchase Lead Scoring models** using Python libraries such as Pandas, Scikit-Learn, and MLFlow.

## Table of Contents
- [Installation](#installation)
  - [Cloning the Codebase](#cloning-the-codebase)
  - [Using Docker (Preferred)](#using-docker-preferred)
    - [For macOS](#for-macos)
    - [For Windows](#for-windows)
    - [For Linux](#for-linux)
  - [Using venv (Optional)](#using-venv-optional)
- [Usage](#usage)
- [License](#license)

---

## Installation

### Cloning the Codebase
First, clone the repository to your local machine:
```bash
git clone https://shivashankar.rampur@stash.leadpointcorp.net/scm/lpml/ml_leadclass_purchase.git
```

---

### Using Docker (Preferred)

Docker provides a reproducible environment, ensuring compatibility across different systems.

#### For macOS
1. **Build the Docker Image**  
   Run the following command to build the Docker image:
   ```bash
   docker build --no-cache --build-arg req_file=./requirements.txt -t ml_train .
   ```

2. **Run the Docker Image**  
   Use this command to run the Docker container:
   ```bash
   docker run -it \
      -p 8888:8888 \
      -v ~/.credentials:/root/.credentials \
      -v ~/.aws:/root/.aws \
      -v /Users/<YourUserName>/Desktop/Repository/ml_leadclass_purchase/Lead_Point_ML:/app \
      --env-file ./.env \
      --name newhome \
      --rm ml_train \
      python Train.py \
      --start_date "$(grep START_DATE .env | cut -d '=' -f2-)" \
      --end_date "$(grep END_DATE .env | cut -d '=' -f2-)" \
      --config_file "$(grep CONFIG_FILE .env | cut -d '=' -f2-)"
   ```

#### For Windows
1. **Build the Docker Image**
   Open a terminal with Docker installed and run:
   ```bash
   docker build --no-cache --build-arg req_file=./requirements.txt -t ml_train .
   ```

2. **Run the Docker Image**  
   Use the following command to run the container (adjust paths for Windows):
   ```powershell
   docker run -it `
      -p 8888:8888 `
      -v C:\Users\<YourUserName>\.credentials:/root/.credentials `
      -v C:\Users\<YourUserName>\.aws:/root/.aws `
      -v C:\Path\To\Repository\ml_leadclass_purchase\Lead_Point_ML:/app `
      --env-file .\.env `
      --name newhome `
      --rm ml_train `
      python Train.py `
      --start_date "$(findstr START_DATE .env | for /F "tokens=2 delims==" %a in ('findstr START_DATE .env') do @echo %a)" `
      --end_date "$(findstr END_DATE .env | for /F "tokens=2 delims==" %a in ('findstr END_DATE .env') do @echo %a)" `
      --config_file "$(findstr CONFIG_FILE .env | for /F "tokens=2 delims==" %a in ('findstr CONFIG_FILE .env') do @echo %a)"
   ```

#### For Linux
1. **Build the Docker Image**  
   Run the following command to build the Docker image:
   ```bash
   docker build --no-cache --build-arg req_file=./requirements.txt -t ml_train .
   ```

2. **Run the Docker Image**  
   Use the following command to run the container:
   ```bash
   docker run -it \
      -p 8888:8888 \
      -v ~/.credentials:/root/.credentials \
      -v ~/.aws:/root/.aws \
      -v ~/Repository/ml_leadclass_purchase/Lead_Point_ML:/app \
      --env-file ./.env \
      --name newhome \
      --rm ml_train \
      python Train.py \
      --start_date "$(grep START_DATE .env | cut -d '=' -f2-)" \
      --end_date "$(grep END_DATE .env | cut -d '=' -f2-)" \
      --config_file "$(grep CONFIG_FILE .env | cut -d '=' -f2-)"
   ```

---

### Using venv (Optional)
If you prefer not to use Docker, you can set up a local Python environment using `venv`.

#### Steps for macOS, Windows, and Linux
1. **Install Python**  
   Ensure Python 3.8 or higher is installed on your system. Download it from [python.org](https://www.python.org/).

2. **Create a Virtual Environment**  
   Run the following command in the project directory:
   ```bash
   python3 -m venv venv
   ```

3. **Activate the Virtual Environment**  
   - On macOS/Linux:
     ```bash
     source venv/bin/activate
     ```
   - On Windows:
     ```powershell
     .\venv\Scripts\activate
     ```

4. **Install Dependencies**  
   After activating the environment, install the required Python packages:
   ```bash
   pip install -r requirements.txt
   ```

5. **Run the Training Script**  
   Execute the training script using:
   ```bash
   python Train.py \
      --start_date "$(grep START_DATE .env | cut -d '=' -f2-)" \
      --end_date "$(grep END_DATE .env | cut -d '=' -f2-)" \
      --config_file "$(grep CONFIG_FILE .env | cut -d '=' -f2-)"
   ```

---

## Usage
Once the environment is set up, you can execute the training script to train the HomePurchase Lead Scoring model. Ensure the `.env` file is correctly configured with the necessary variables:

```env
START_DATE=YYYY-MM-DD
END_DATE=YYYY-MM-DD
CONFIG_FILE=path/to/config.json
```

Replace `YYYY-MM-DD` with the desired date range and provide the path to your configuration file.

To start the training process, use:
```bash
python Train.py --start_date <start_date> --end_date <end_date> --config_file <config_file>
```

---

## License
This project is proprietary and intended for internal use within LeadPoint Corporation. Unauthorized distribution or usage is strictly prohibited.
