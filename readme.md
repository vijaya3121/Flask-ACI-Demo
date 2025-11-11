## 🐳 Flask Web App on Azure Container Instances (ACI)
![Azure](https://img.shields.io/badge/Deployed%20on-Azure-blue)   ![Docker](https://img.shields.io/badge/Containerized%20with-Docker-blue)   ![Flask](https://img.shields.io/badge/Framework-Flask-green)   ![Status](https://img.shields.io/badge/Status-Deployed-success)

---

## 📘 Project Overview

This project demonstrates how to build a simple Flask web application, containerize it using Docker, push the image to Azure Container Registry (ACR), and deploy it to Azure Container Instances (ACI).
It’s part of my DevOps learning journey, focusing on cloud deployment, CI/CD, and container orchestration.

---

## 🧱 Architecture

Flask App → Docker Image → Azure Container Registry → Azure Container Instance (Public IP)

📊 **Architecture Diagram**
![](https://github.com/vijaya3121/Flask-ACI-Demo/blob/main/flask-aci-image.png)

---

## 🧰 Tech Stack

Flask (Python) – Web framework

Docker – Containerization

Azure CLI – Resource management

Azure Container Registry (ACR) – Private container image storage

Azure Container Instance (ACI) – Serverless container runtime

---

## ⚙️ Step-by-Step Setup
### 🐍 Step 1: Create Flask App
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
### 📦 Step 2: Create requirements.txt  
```
echo "flask" > requirements.txt
```
---
### 🐳 Step 3: Build Docker Image
```
FROM python:3.9-slim
WORKDIR /app
COPY . /app
RUN pip install -r requirements.txt
CMD ["python", "app.py"]
```
Successfully built and tagged the Flask Docker image.

![Docker Build Success](https://github.com/vijaya3121/Flask-ACI-Demo/blob/main/docker%20build%20success.png)

---
### 🧱 step 4️⃣ Build and Test Locally
```
docker build -t flask-demo:v1 .
docker run -p 5000:5000 flask-demo:v1
```
![docker run](https://github.com/vijaya3121/Flask-ACI-Demo/blob/main/docker%20run%20output.png)
---

### step5️⃣ Create Azure Resources
```
az group create --name myResourceGroupFlask --location centralindia
az acr create --resource-group myResourceGroupFlask --name myacrflask2040 --sku Basic
az acr login --name myacrflask2040
```
![acr list](https://github.com/vijaya3121/Flask-ACI-Demo/blob/main/acr%20list.png)
---
### ☁️ Step 6 Push to Azure Container Registry 
```
docker tag flask-demo:v1 myacrflask2040.azurecr.io/flask-demo:v1
docker push myacrflask2040.azurecr.io/flask-demo:v1
```
![push to acr](https://github.com/vijaya3121/Flask-ACI-Demo/blob/main/push%20to%20acr.png)
---
### step7️⃣ Deploy to Azure Container Instance
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

### 8️⃣ View App URL
```
az container show \
  --resource-group myResourceGroupFlask \
  --name flaskaci2040 \
  --query "{IP:ipAddress.ip, FQDN:ipAddress.fqdn}" \
  --out table
```
![container instance running](https://github.com/vijaya3121/Flask-ACI-Demo/blob/main/container%20instance%20running%20state.png)
---
## Then visit:
```
http://<ACI IP>:5000
```
---
## ✅ Output

You should see this message in your browser
![ACI on browser with IP](https://github.com/vijaya3121/Flask-ACI-Demo/blob/main/aci%20on%20browser.png)

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












