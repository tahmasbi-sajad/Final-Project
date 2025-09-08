# Project1

A simple Python application deployed on Kubernetes with Docker and GitLab CI/CD.

---

## Table of Contents

- [Project Structure](#project-structure)
- [Requirements](#requirements)
- [Local Run](#local-run)
- [Docker](#docker)
- [Kubernetes Deployment](#kubernetes-deployment)
- [GitLab CI/CD Pipeline](#gitlab-cicd-pipeline)

---

## Project Structure

```
project1/
├─ app.py
├─ Dockerfile
├─ deployment.yaml
├─ .gitlab-ci.yml
├─ test-ci.yml
├─ README.md
```

- `app.py` → Main Python application.
- `Dockerfile` → Docker image configuration.
- `deployment.yaml` → Kubernetes Deployment manifest.
- `.gitlab-ci.yml` → GitLab CI/CD pipeline configuration.
- `test-ci.yml` → Child pipeline for running tests.

---

## Requirements

- Python 3.12+
- Docker
- Kubernetes cluster access (Kubeconfig)
- GitLab Runner for CI/CD

---

## Local Run

Run the Python application locally:

```bash
python app.py
```

Expected output:

```
Hello! This program was run in Kubernetes.
```

---

## Docker

Build and run the application using Docker:

```bash
docker build -t project1:latest .
docker run --rm project1:latest
```

This builds the Docker image and runs it locally.

---

## Kubernetes Deployment

Deploy the application using the `deployment.yaml` file:

```bash
kubectl apply -f deployment.yaml
kubectl get pods -n dev
```

- This deployment creates 2 replicas in the `dev` namespace.
- The container image is configured in the deployment (`tahmasbi/myrepo:latest` by default).

---

## GitLab CI/CD Pipeline

Pipeline stages:

1. **Build**
   - Builds Docker images with two tags: commit SHA (`$CI_COMMIT_SHORT_SHA`) and `latest`.
   - Pushes the images to Docker Hub.
   - Runs automatically on `dev` and `main`, manual on merge requests or tags.

2. **Test**
   - Runs the child pipeline defined in `test-ci.yml`.
   - Executes Python tests: `from app import main; main()`.
   - Automatic on `dev` and `main`, manual on merge requests or tags.

3. **Deploy**
   - Deploys the application to Kubernetes.
   - Automatic on `dev` branch.
   - Manual on `main`, merge requests, or tags.

### Pipeline Behavior Summary

| Branch / Event           | Build | Test | Deploy |
|--------------------------|-------|------|--------|
| Every commit on `dev`    | Auto  | Auto | Auto   |
| Merge request or Tag     | Auto  | Auto | Manual |
| Merge to `main`          | Auto  | Auto | Manual |

---


