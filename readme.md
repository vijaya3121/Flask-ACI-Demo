## 🐳 Flask Web App on Azure Container Instances (ACI)

## 📘 Project Overview

This project demonstrates how to build a simple Flask web application, containerize it using Docker, push the image to Azure Container Registry (ACR), and deploy it to Azure Container Instances (ACI).
It’s part of my DevOps learning journey, focusing on cloud deployment, CI/CD, and container orchestration.

---

## 🧱 Architecture

Flask App → Docker Image → Azure Container Registry → Azure Container Instance (Public IP)

---

## 🧰 Tech Stack

Flask (Python) – Web framework

Docker – Containerization

Azure CLI – Resource management

Azure Container Registry (ACR) – Private container image storage

Azure Container Instance (ACI) – Serverless container runtime

---

## ⚙️ Step-by-Step Setup
### 1️⃣ Create a Flask App
```
mkdir flask-web && cd flask-web
nano app.py

from flask import Flask
app = Flask(__name__)

@app.route('/')
def home():
    return "Hello from Flask running in Azure Container Instance!"

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000)
```
---

### 2️⃣ Create requirements file
```
echo "flask" > requirements.txt
```
---
### 3️⃣ Create Dockerfile
```
FROM python:3.9-slim
WORKDIR /app
COPY . /app
RUN pip install -r requirements.txt
CMD ["python", "app.py"]
```
---
### 4️⃣ Build and test locally
```
docker build -t flask-demo:v1 .
docker run -p 5000:5000 flask-demo:v1
```
---

### 5️⃣ Create Azure Resources
```
az group create --name myResourceGroupFlask --location centralindia
az acr create --resource-group myResourceGroupFlask --name myacrflask2040 --sku Basic
az acr login --name myacrflask2040
```
---
### 6️⃣ Tag and push image to ACR
```
docker tag flask-demo:v1 myacrflask2040.azurecr.io/flask-demo:v1
docker push myacrflask2040.azurecr.io/flask-demo:v1
```
---
### 7️⃣ Deploy to Azure Container Instance
```
az container create \
  --resource-group myResourceGroupFlask \
  --name flaskaci2040 \
  --image myacrflask2040.azurecr.io/flask-demo:v1 \
  --cpu 1 \
  --memory 1 \
  --os-type Linux \
  --ip-address Public \
  --dns-name-label flaskaci2040demo \
  --ports 5000 \
  --registry-login-server myacrflask2040.azurecr.io \
  --registry-username myacrflask2040 \
  --registry-password <your-password>
```
---

### 8️⃣ View app URL
```
az container show \
  --resource-group myResourceGroupFlask \
  --name flaskaci2040 \
  --query "{IP:ipAddress.ip, FQDN:ipAddress.fqdn}" \
  --out table
```
---
## Then visit:
```
http://<ACI IP>:5000
```
---
## ✅ Output

You should see this message in your browser

---
## 🔍 Verify Logs
```
az container logs --resource-group myResourceGroupFlask --name flaskaci2040
```
---
## 🚀 Next Steps

Add GitHub Actions CI/CD pipeline to automate build and push to ACR.

Deploy the same app on Azure App Service (PaaS).

Later, deploy in Azure Kubernetes Service (AKS).

---
## 👩‍💻 Author

Vijaya Reddy
Azure DevOps Learner | Aspiring Cloud & DevOps Engineer












