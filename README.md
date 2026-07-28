# MLOps-AnomalyDetectionLinuxSystem-MiniProject
Here is a clean, production-ready `README.md` file designed for your GitHub repository. It presents your project with strong engineering rigor, clear architectural diagrams, and structured setup instructions.

---
# Automated MLOps Pipeline for Real-Time Anomaly Detection

An end-to-end, lightweight MLOps platform that monitors Linux host telemetry in real time, detects system anomalies using unsupervised machine learning, and automates model tracking and containerized deployment.

![Linux](https://img.shields.io/badge/OS-Linux-FCC624?style=flat&logo=linux&logoColor=black)
![Python](https://img.shields.io/badge/Python-3.10%2B-3776AB?style=flat&logo=python&logoColor=white)
![FastAPI](https://img.shields.io/badge/FastAPI-0.100%2B-009688?style=flat&logo=fastapi&logoColor=white)
![Docker](https://img.shields.io/badge/Docker-Containerized-2496ED?style=flat&logo=docker&logoColor=white)
![MLflow](https://img.shields.io/badge/MLOps-MLflow-0194E2?style=flat&logo=mlflow&logoColor=white)
![License](https://img.shields.io/badge/License-MIT-green.svg)

---

## 📌 Overview

Traditional server monitoring tools rely heavily on static, hardcoded thresholds (e.g., alert when CPU usage > 90%). However, complex system intrusions, memory leaks, and performance degradation often manifest as subtle, multi-variable anomalies long before traditional thresholds are breached.

This project addresses that gap by establishing a **continuous MLOps lifecycle on a Linux host**:
1. **Linux Telemetry Collection:** A background system daemon streams CPU, RAM, Disk I/O, and Network metrics.
2. **Unsupervised Anomaly Detection:** An **Isolation Forest** model evaluates multi-dimensional host vitals to identify abnormal behavior.
3. **Containerized REST API:** A high-performance **FastAPI** microservice wrapped in **Docker** serves real-time predictions.
4. **MLOps Lifecycle Management:** **MLflow** tracks model experiments, parameters, and drift, providing an automated feedback loop for model retraining.

---

## 🏗️ System Architecture


```

```
                            +-----------------------------------+
                            |            LINUX HOST             |
                            |                                   |
                            |   +---------------------------+   |
                            |   |   psutil Telemetry Agent  |   |
                            |   |   (systemd background)    |   |
                            |   +-------------+-------------+   |
                            +-----------------|-----------------+
                                              |
                                              | JSON Telemetry Stream
                                              v
                  +-------------------------------------------------------+
                  |                   DOCKER NETWORK                      |
                  |                                                       |
                  |   +-------------------+       +-------------------+   |
                  |   |  FastAPI Engine   |  <--  |  Model Storage    |   |
                  |   |  (/predict)       |       |  (Joblib Artifacts|   |
                  |   +---------+---------+       +---------^---------+   |
                  |             |                           |             |
                  |             v                           |             |
                  |   +-------------------+       +---------+---------+   |
                  |   | Real-Time Output  |       | MLflow Server   |   |
                  |   | Anomaly Prediction|       | Experiment      |   |
                  |   | Score & Alerts    |       | Tracking/Drift  |   |
                  |   +-------------------+       +-------------------+   |
                  |                                                       |
                  +-------------------------------------------------------+

```

```

---

## 🧰 Tech Stack

| Domain | Tools & Technologies |
| :--- | :--- |
| **Telemetry & OS Interface** | Python 3.10+, `psutil`, Linux `systemd` |
| **Machine Learning** | `scikit-learn` (Isolation Forest), `pandas`, `numpy` |
| **API & Serving** | FastAPI, Uvicorn, Pydantic |
| **Containerization** | Docker, Docker Compose |
| **MLOps & Tracking** | MLflow, Joblib |

---

## 📂 Directory Structure

```text
.
├── config/
│   └── config.yaml             # System & model configuration options
├── daemon/
│   ├── telemetry_agent.py      # Background telemetry collection script
│   └── telemetry-agent.service # Linux systemd unit file
├── ml/
│   ├── train.py                # Model training script with MLflow logging
│   ├── preprocess.py           # Feature engineering & scaling logic
│   └── models/                 # Saved joblib model artifacts
├── service/
│   ├── app.py                  # FastAPI server application
│   └── Dockerfile              # Container definition for inference API
├── data/                       # Telemetry metrics storage (.csv / .parquet)
├── docker-compose.yml          # Orchestration for FastAPI & MLflow services
├── requirements.txt            # Python dependencies
└── README.md                   # Project documentation

```

---

## 🚀 Quickstart & Setup Instructions

### Prerequisites

* **Operating System:** Linux (Ubuntu, Fedora, Debian, or WSL2)
* **Python:** `3.10` or higher
* **Containerization:** Docker Engine & Docker Compose plugin installed
* **System Permissions:** `sudo` privileges (required for installing systemd services)

---

### Step 1: Clone Repository & Set Up Virtual Environment

```bash
git clone [https://github.com/Avdgq2577/automated-mlops-anomaly-detection.git](https://github.com/Avdgq2577/automated-mlops-anomaly-detection.git)
cd automated-mlops-anomaly-detection

# Create and activate virtual environment
python3 -m venv venv
source venv/bin/activate

# Install dependencies
pip install --upgrade pip
pip install -r requirements.txt

```

---

### Step 2: Deploy Containerized Services (FastAPI & MLflow)

Build and launch the inference API and MLflow tracking server:

```bash
docker-compose up -d --build

```

Verify that the containers are active:

* **FastAPI Interactive Docs:** `http://localhost:8000/docs`
* **MLflow UI:** `http://localhost:5000`

---

### Step 3: Train the Initial Anomaly Detection Model

Run the training pipeline to generate sample telemetry, fit the `IsolationForest` model, and register model metrics in MLflow:

```bash
python ml/train.py

```

---

### Step 4: Install & Enable the Linux Telemetry Daemon (`systemd`)

Deploy the telemetry agent as a persistent background daemon on your host system:

```bash
# Copy unit file to systemd directory
sudo cp daemon/telemetry-agent.service /etc/systemd/system/

# Reload systemd manager configuration
sudo systemctl daemon-reload

# Start and enable daemon to launch on boot
sudo systemctl start telemetry-agent.service
sudo systemctl enable telemetry-agent.service

# Check daemon status
sudo systemctl status telemetry-agent.service

```

---

## 📊 API Reference

### Health Check

`GET /health`

```json
{
  "status": "healthy",
  "model_loaded": true
}

```

### Predict Anomaly

`POST /predict`

**Request Payload:**

```json
{
  "cpu_percent": 88.5,
  "ram_percent": 91.2,
  "disk_io_read_bytes": 1048576,
  "disk_io_write_bytes": 5242880,
  "net_bytes_sent": 204800,
  "net_bytes_recv": 1024000
}

```

**Response:**

```json
{
  "is_anomaly": true,
  "anomaly_score": -0.1842,
  "timestamp": "2026-07-28T09:19:31Z",
  "alert_level": "CRITICAL"
}

```

---

## 🧪 Testing System Anomaly Detection

To test if the pipeline correctly flags real-time system stress as an anomaly, generate synthetic load on your Linux host:

```bash
# Install stress-ng load generator
sudo apt install stress-ng   # Ubuntu/Debian
# sudo dnf install stress-ng # Fedora/RHEL

# Run a 30-second CPU and memory stress test
stress-ng --cpu 4 --vm 2 --vm-bytes 1G --timeout 30s

```

Observe live prediction output logs from the FastAPI container:

```bash
docker logs -f fastapi_anomaly_service

```
