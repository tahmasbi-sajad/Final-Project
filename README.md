# Flask App on Kubernetes with GitLab CI/CD

This project demonstrates deploying a simple **Flask application** on a
Kubernetes cluster using **Docker**, **Helm/Kubectl**, **Ingress**, and
**GitLab CI/CD**.

------------------------------------------------------------------------

## 📂 Project Structure

    .
    ├── app.py                # Simple Flask application
    ├── Dockerfile            # Docker image build instructions
    ├── requirements.txt      # Python dependencies
    ├── deployment.yaml       # Kubernetes Deployment
    ├── service.yaml          # Kubernetes Service
    ├── ingress.yaml          # Kubernetes Ingress
    ├── .gitlab-ci.yml        # GitLab CI/CD pipeline
    ├── test-ci.yml           # Test stage configuration

------------------------------------------------------------------------

## 🚀 Application

**Flask App (app.py):**

``` python
from flask import Flask

app = Flask(__name__)

@app.route("/")
def home():
    return "Hello! This program is running in Kubernetes."

if __name__ == "__main__":
    app.run(host="0.0.0.0", port=8000)
```

------------------------------------------------------------------------

## 🐳 Docker

**Dockerfile:**

``` dockerfile
FROM python:3.12-slim

WORKDIR /app

COPY app.py .

RUN pip install flask

CMD ["python", "app.py"]
```

Builds a lightweight Docker image running the Flask application.

------------------------------------------------------------------------

## ☸️ Kubernetes Manifests

-   **Deployment:** Runs 2 replicas of the app.
-   **Service:** Exposes the app internally on port 80 → container port
    8000.
-   **Ingress:** Routes external traffic through NGINX ingress
    controller.
-   **MetalLB:** Provides LoadBalancer IPs in bare-metal environments.

Example Service:

``` yaml
apiVersion: v1
kind: Service
metadata:
  name: web-svc
  namespace: dev
spec:
  ports:
    - port: 80
      targetPort: 8000
  selector:
    app: my-app
```

------------------------------------------------------------------------

## ⚙️ GitLab CI/CD Pipeline

**Stages:** 1. **Build** → Build and push Docker image to Docker Hub. 2.
**Test** → Install dependencies and verify app loads successfully. 3.
**Deploy** → Deploy to Kubernetes using `kubectl` and `helm`.

Pipeline defined in `.gitlab-ci.yml`.

------------------------------------------------------------------------

## 📦 Prerequisites Installed Beforehand

The following components were installed on the Kubernetes cluster before
deploying this project:

``` bash
kubectl delete -f https://raw.githubusercontent.com/metallb/metallb/v0.15.2/config/manifests/metallb-native.yaml

kubectl delete -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.13.1/deploy/static/provider/aws/deploy.yaml
```

And applied **MetalLB configuration** (`metallb.yaml`):

``` yaml
apiVersion: metallb.io/v1beta1
kind: IPAddressPool
metadata:
  name: first-pool
  namespace: metallb-system
spec:
  addresses:
  - 192.168.6.240-192.168.6.250

---

apiVersion: metallb.io/v1beta1
kind: L2Advertisement
metadata:
  name: first-pool-advertise
  namespace: metallb-system
spec:
  ipAddressPools:
    - first-pool
```

------------------------------------------------------------------------

## 🌍 Accessing the Application

-   The app is accessible via the **Ingress resource** at:

```{=html}
<!-- -->
```
    http://final-project.com

(Ensure your DNS or `/etc/hosts` file points to the MetalLB IP).

------------------------------------------------------------------------

## ✅ Testing

GitLab CI runs a simple test stage:

``` bash
pip install -r requirements.txt
python -c "import app; print('App loaded successfully')"
```

------------------------------------------------------------------------

## 📖 Summary

This project shows: - How to containerize a Flask app with Docker. - How
to deploy it to Kubernetes using Deployment, Service, Ingress. - How to
configure MetalLB for LoadBalancer IPs. - How to set up GitLab CI/CD for
automated build, test, and deploy.

