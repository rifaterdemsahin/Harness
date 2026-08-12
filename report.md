# Harness CI/CD Proof of Concept (POC) Report

This report documents the implementation of the Harness CI/CD Proof of Concept and local/remote environment setups.

---

## 🔗 Project Reference Links
* **Harness Console Settings:** [Harness Account Settings](https://app.harness.io/ng/account/5EeV6llCSrqjP_iw2C--yw/module/cv/settings)
* **GitHub Repository:** [rifaterdemsahin/Harness](https://github.com/rifaterdemsahin/Harness)
* **Live GitHub Pages URL:** [Harness POC Dashboard](https://rifaterdemsahin.github.io/Harness/)

---

## 📁 Repository Structure & Code Files

We created a minimal codebase in `/Users/rifaterdemsahin/projects/harness`:

1. **`main.py`**:
   ```python
   print("Hello from Harness!")
   ```
2. **`Dockerfile`**:
   ```dockerfile
   FROM python:3.11-slim
   COPY . /app
   WORKDIR /app
   CMD ["python", "main.py"]
   ```
3. **`requirements.txt`**:
   ```text
   # No external dependencies needed for main.py
   ```
4. **`.harness/pipeline.yaml`**:
   ```yaml
   pipeline:
     name: Test Harness CI
     identifier: test_harness_ci
     projectIdentifier: default
     orgIdentifier: default
     tags: {}
     stages:
       - stage:
           name: Build
           identifier: build
           description: ""
           type: CI
           spec:
             cloneCodebase: true
             platform:
               os: Linux
               arch: Amd64
             runtime:
               type: Cloud
               spec: {}
             steps:
               - step:
                   type: Run
                   name: Test Step
                   identifier: test
                   spec:
                     shell: Bash
                     command: echo "Testing Harness with GitHub"
               - step:
                   type: Run
                   name: Build
                   identifier: build
                   spec:
                     shell: Bash
                     command: |
                       python --version
                       echo "App would be built here"
     properties:
       ci:
         codebase:
           connectorRef: github_connector_poc
           repoName: Harness
           branch: main
   ```
5. **`index.html`**:
   A beautiful, responsive HTML page with step-by-step documentation and an interactive **Harness Webhook and Pipeline Simulator** that runs the pipeline stages visually in real-time.

---

## 🌐 GitHub Pages Deployment
* **Enabled GitHub Pages** programmatically via GitHub REST API using `gh` CLI.
* Configuration: Deploying from the root (`/`) of the `main` branch.
* Live deployment verified at: **[https://rifaterdemsahin.github.io/Harness/](https://rifaterdemsahin.github.io/Harness/)**

---

## 🐳 Harness Delegate Setup (Docker)

To run the delegate worker on local infrastructure, we started Docker Desktop on macOS and initialized both the delegate container and the auto-upgrader container using the parameters provided:

### 1. Harness Docker Delegate
* **Command:**
  ```bash
  docker run -d --cpus=1 --memory=2g \
    -e DELEGATE_NAME=docker-delegate \
    -e NEXT_GEN="true" \
    -e DELEGATE_TYPE="DOCKER" \
    -e ACCOUNT_ID=5EeV6llCSrqjP_iw2C--yw \
    -e DELEGATE_TOKEN=YTNjMzQ4YTAwYzk3MjdhNWNlZGZmZTJkMjllOWE1NzU= \
    -e DELEGATE_TAGS="" \
    -e MANAGER_HOST_AND_PORT=https://app.harness.io us-docker.pkg.dev/gar-prod-setup/harness-public/harness/delegate:26.07.89706
  ```
* **Status:** Running (Container ID: `02d373418c05`, Image: `us-docker.pkg.dev/gar-prod-setup/harness-public/harness/delegate:26.07.89706`)

### 2. Auto-Upgrader Container
* **Command:**
  ```bash
  docker run -d --cpus=0.1 --memory=100m \
    -v /var/run/docker.sock:/var/run/docker.sock \
    -e ACCOUNT_ID=5EeV6llCSrqjP_iw2C--yw \
    -e MANAGER_HOST_AND_PORT=https://app.harness.io \
    -e UPGRADER_WORKLOAD_NAME=docker-delegate \
    -e UPGRADER_TOKEN=YTNjMzQ4YTAwYzk3MjdhNWNlZGZmZTJkMjllOWE1NzU= \
    -e CONTAINER_STOP_TIMEOUT=3600 \
    -e SCHEDULE="0 */1 * * *" harness/upgrader:latest
  ```
* **Status:** Running (Container ID: `573cf5e9da4e`, Image: `harness/upgrader:latest`)

---

## 📑 Action History & Git Updates
All configurations and source files have been successfully added, committed, and pushed to the remote repository `main` branch.
