# Node.js CI/CD Automation using GitHub Actions, Jenkins and Docker

This repository demonstrates two CI/CD implementations for a Node.js web application.

* **Task 1:** Automate Code Deployment Using GitHub Actions and Docker
* **Task 2:** Create a Simple Jenkins Pipeline for CI/CD

Both tasks use the same Node.js application and GitHub repository, but demonstrate two different CI/CD automation approaches.

---

# Project Repository

```text
Bhuvan068/nodejs-demoapp
```

The project is based on the `benc-uk/nodejs-demoapp` sample Node.js application and has been configured with custom CI/CD pipelines for the assignment.

---

# Technologies Used

| Technology     | Purpose                           |
| -------------- | --------------------------------- |
| Node.js        | Web application runtime           |
| Express.js     | Web application framework         |
| npm            | Dependency management and testing |
| Git            | Source code version control       |
| GitHub         | Source code repository            |
| GitHub Actions | Task 1 CI/CD automation           |
| Jenkins        | Task 2 CI/CD automation           |
| Docker         | Application containerization      |
| DockerHub      | Docker image registry             |
| PowerShell     | Local command-line environment    |

---

# Project Structure

```text
nodejs-demoapp/
│
├── .github/
│   ├── scripts/
│   └── workflows/
│       ├── main.yml
│       ├── ci-build.yaml
│       ├── publish.yaml
│       └── cd-release-aks.yaml
│
├── build/
│   └── Dockerfile
│
├── deploy/
│
├── src/
│   ├── package.json
│   ├── package-lock.json
│   ├── server.mjs
│   ├── tests/
│   ├── views/
│   └── ...
│
├── Jenkinsfile
├── Dockerfile.jenkins
├── .dockerignore
├── .gitignore
├── makefile
├── LICENSE
└── README.md
```

---

# Task 1: Automate Code Deployment Using CI/CD Pipeline

## Overview

Task 1 implements a CI/CD pipeline using:

```text
GitHub Actions
      +
Docker
      +
DockerHub
```

Whenever code is pushed to the `main` branch, GitHub Actions automatically tests the Node.js application, builds a Docker image, and pushes the latest image to DockerHub.

---

## Task 1 Objective

The objective is to automate:

1. Detecting a push to the `main` branch.
2. Checking out the latest source code.
3. Configuring Node.js.
4. Installing application dependencies.
5. Starting the application.
6. Running automated tests.
7. Authenticating with DockerHub.
8. Building a Docker image.
9. Pushing the Docker image to DockerHub.

---

# Task 1 Architecture

```text
Developer
    │
    │ git push
    ▼
GitHub Repository
    │
    │ Push to main
    ▼
GitHub Actions
    │
    ▼
Checkout Source Code
    │
    ▼
Setup Node.js 20
    │
    ▼
Install Dependencies
    │
    ▼
Start Application
    │
    ▼
Run Integration Tests
    │
    ├──────── FAIL ────────> Pipeline Stops
    │
   PASS
    │
    ▼
DockerHub Authentication
    │
    ▼
Build Docker Image
    │
    ▼
Push Docker Image
    │
    ▼
DockerHub
    │
    ▼
bhuvanreddy0001/nodejs-demoapp:latest
```

---

## GitHub Actions Workflow

The Task 1 workflow is defined in:

```text
.github/workflows/main.yml
```

The workflow automatically starts whenever code is pushed to:

```text
main
```

The main pipeline contains two major jobs:

```text
Test Node.js App
        ↓
Build and Push Docker Image
```

The Docker build job runs only after the testing job succeeds.

---

## Task 1 Trigger

The GitHub Actions workflow uses a push trigger:

```yaml
on:
  push:
    branches:
      - main
```

Therefore:

```text
Code Change
    ↓
git commit
    ↓
git push origin main
    ↓
GitHub Actions Automatically Starts
```

No manual pipeline execution is required.

---

## Task 1 — Install Dependencies

The Node.js application is located inside:

```text
src/
```

Dependencies are installed using:

```bash
npm install
```

The required packages are defined in:

```text
src/package.json
```

---

## Task 1 — Start Application

Before integration testing, the application is started using:

```bash
npm start
```

The Node.js application runs internally on:

```text
Port 3000
```

The pipeline waits briefly for the server to become available before executing the tests.

---

## Task 1 — Automated Testing

The project contains HTTP-based integration tests.

Tests are executed using:

```bash
npm test
```

The pipeline verifies the application's required endpoints.

The successful testing result was:

```text
4 requests processed (4 succeeded)
```

If testing fails:

```text
Test Failed
    ↓
Pipeline Stops
    ↓
Docker Image Is Not Published
```

If testing succeeds:

```text
Test Passed
    ↓
Docker Build Starts
```

---

# Docker Containerization

The application Dockerfile is located at:

```text
build/Dockerfile
```

The application is packaged into a portable Docker container.

The application container exposes:

```text
Port 3000
```

---

## Task 1 — Docker Image Build

After all tests pass, GitHub Actions builds the Docker image.

The resulting DockerHub image is:

```text
bhuvanreddy0001/nodejs-demoapp:latest
```

---

## DockerHub Authentication

GitHub Actions authenticates with DockerHub before publishing the image.

Credentials are stored securely using GitHub Repository Secrets.

The configured secrets are:

```text
DOCKERHUB_USERNAME
DOCKERHUB_TOKEN
```

Credentials are not directly stored inside the workflow or source code.

---

## Task 1 — Push Docker Image

After successful authentication and Docker image creation, the pipeline pushes the image to DockerHub.

```bash
docker push bhuvanreddy0001/nodejs-demoapp:latest
```

The final image is:

```text
bhuvanreddy0001/nodejs-demoapp:latest
```

---

# Running the Application Locally

Move into the source directory:

```bash
cd src
```

Install dependencies:

```bash
npm install
```

Start the application:

```bash
npm start
```

The application becomes available at:

```text
http://localhost:3000
```

---

# Running Using Docker

Build the application image:

```bash
docker build -f build/Dockerfile -t nodejs-demoapp:latest .
```

Run the container:

```bash
docker run -d -p 3001:3000 --name nodejs-demo-container nodejs-demoapp:latest
```

Access the application at:

```text
http://localhost:3001
```

---

# Pulling the Task 1 Image from DockerHub

The image generated by GitHub Actions can be downloaded using:

```bash
docker pull bhuvanreddy0001/nodejs-demoapp:latest
```

Run the downloaded image:

```bash
docker run -d -p 3002:3000 --name dockerhub-demo bhuvanreddy0001/nodejs-demoapp:latest
```

Access it at:

```text
http://localhost:3002
```

This verifies that the image published by the CI/CD pipeline can be successfully downloaded and executed.

---

# Task 1 Automation Verification

A source-code modification was committed and pushed to the `main` branch.

The push automatically triggered GitHub Actions.

```text
Source Code Change
        ↓
git commit
        ↓
git push origin main
        ↓
GitHub Actions Triggered
        ↓
Tests Executed
        ↓
Tests Passed
        ↓
Docker Image Built
        ↓
DockerHub Authentication
        ↓
Updated Image Pushed
```

This verified that Task 1 operates automatically without manually starting the workflow.

---

# Task 1 Result

Task 1 successfully demonstrates:

* Git and GitHub source code management
* Automatic GitHub Actions triggering
* Node.js dependency installation
* Automated application testing
* Docker image creation
* Secure DockerHub authentication
* Automatic Docker image publishing
* Docker image retrieval
* Container execution
* Automatic rebuilding after source-code changes

The Task 1 pipeline can be summarized as:

```text
test → build → push
```

---

---

# Task 2: Create a Simple Jenkins Pipeline for CI/CD

## Overview

Task 2 implements another CI/CD pipeline for the same Node.js application, this time using:

```text
Jenkins
    +
Docker
```

Jenkins monitors the GitHub repository for new commits.

When a new commit is detected, Jenkins automatically retrieves the latest code and executes the complete CI/CD pipeline.

The pipeline performs:

```text
Build
  ↓
Test
  ↓
Docker Build
  ↓
Deploy
```

---

# Task 2 Objective

The objective of Task 2 is to:

1. Install and configure Jenkins.
2. Connect Jenkins with the GitHub repository.
3. Create a `Jenkinsfile`.
4. Automatically build the application.
5. Run application tests.
6. Build a Docker image.
7. Deploy the Docker container.
8. Detect new GitHub commits automatically.
9. Verify the deployed application.

---

# Jenkins Environment

Jenkins was installed locally using Docker.

The base Jenkins image used was:

```text
jenkins/jenkins:lts-jdk21
```

A custom Jenkins image was created to provide the tools required by the pipeline.

The configuration is stored in:

```text
Dockerfile.jenkins
```

The Jenkins environment contains:

```text
Jenkins
Node.js
npm
Docker CLI
```

---

# Dockerfile.jenkins

The custom Jenkins Dockerfile is:

```dockerfile
FROM jenkins/jenkins:lts-jdk21

USER root

RUN apt-get update && \
    apt-get install -y docker.io curl && \
    curl -fsSL https://deb.nodesource.com/setup_20.x | bash - && \
    apt-get install -y nodejs && \
    rm -rf /var/lib/apt/lists/*

USER jenkins
```

The custom image was built using:

```bash
docker build -f Dockerfile.jenkins -t jenkins-with-docker .
```

---

# Running Jenkins

Jenkins was started using Docker with access to the host Docker socket.

```powershell
docker run -d `
  --name jenkins `
  -u root `
  -p 8080:8080 `
  -p 50000:50000 `
  -v jenkins_home:/var/jenkins_home `
  -v /var/run/docker.sock:/var/run/docker.sock `
  jenkins-with-docker
```

Jenkins is accessible locally at:

```text
http://localhost:8080
```

The Docker socket allows Jenkins to build and deploy Docker containers.

> Note: Running the Jenkins container as root was used for this local educational environment to access the Docker socket. A production Jenkins environment should use more restrictive permissions.

---

# Jenkins Job Configuration

A Jenkins Pipeline job was created with the name:

```text
nodejs-demoapp-pipeline
```

The pipeline uses:

```text
Definition: Pipeline script from SCM
SCM: Git
Branch: */main
Script Path: Jenkinsfile
```

The GitHub repository is configured as the source repository.

Jenkins therefore retrieves the `Jenkinsfile` directly from the repository.

---

# Jenkinsfile

The Jenkins CI/CD pipeline is defined in:

```text
Jenkinsfile
```

The pipeline contains four main stages:

```text
Build
Test
Docker Build
Deploy
```

The implemented pipeline is:

```groovy
pipeline {
    agent any

    stages {

        stage('Build') {
            steps {
                echo 'Building Node.js application...'
                dir('src') {
                    sh 'npm install'
                }
            }
        }

        stage('Test') {
            steps {
                echo 'Running tests...'
                dir('src') {
                    sh 'npm start &'
                    sh 'sleep 5'
                    sh 'npm test'
                }
            }
        }

        stage('Docker Build') {
            steps {
                echo 'Building Docker image...'
                sh 'docker build -f build/Dockerfile -t nodejs-demoapp:jenkins .'
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploying container...'
                sh 'docker rm -f nodejs-jenkins-app || true'
                sh 'docker run -d -p 3005:3000 --name nodejs-jenkins-app nodejs-demoapp:jenkins'
            }
        }
    }
}
```

---

# Task 2 Jenkins Architecture

```text
Developer
    │
    │ git push
    ▼
GitHub Repository
    │
    │ Jenkins detects new commit
    ▼
Jenkins Pipeline
    │
    ▼
Checkout Source Code
    │
    ▼
Build
    │
    │ npm install
    ▼
Test
    │
    │ npm test
    ▼
Docker Build
    │
    │ nodejs-demoapp:jenkins
    ▼
Deploy
    │
    ▼
Docker Container
    │
    ▼
nodejs-jenkins-app
    │
    ▼
localhost:3005
```

---

# Jenkins Pipeline Stages

## Stage 1 — Build

The Build stage installs the Node.js dependencies.

```text
src/
    ↓
npm install
```

The Jenkins console displays:

```text
Building Node.js application...
```

After successful dependency installation, Jenkins continues to the Test stage.

---

## Stage 2 — Test

The application is started inside the Jenkins environment.

```bash
npm start &
```

Jenkins waits for the application to start and then executes:

```bash
npm test
```

The required application endpoint tests completed successfully.

The successful pipeline test output included:

```text
Root URL        PASS
Info Page       PASS
Tools Page      PASS
```

The test execution completed successfully, allowing Jenkins to continue to the Docker Build stage.

---

## Stage 3 — Docker Build

After successful testing, Jenkins creates a Docker image using:

```bash
docker build -f build/Dockerfile -t nodejs-demoapp:jenkins .
```

The resulting local image is:

```text
nodejs-demoapp:jenkins
```

If the previous stages fail, the deployment stages are not executed.

---

## Stage 4 — Deploy

Before deploying the latest version, Jenkins removes the previous application container if it exists.

```bash
docker rm -f nodejs-jenkins-app || true
```

Jenkins then starts the latest container using:

```bash
docker run -d -p 3005:3000 --name nodejs-jenkins-app nodejs-demoapp:jenkins
```

Container name:

```text
nodejs-jenkins-app
```

Host port:

```text
3005
```

Container application port:

```text
3000
```

The deployed application is accessible at:

```text
http://localhost:3005
```

The application was successfully opened and verified through this address.

---

# Automatic Jenkins Trigger

The assignment requires Jenkins to automatically execute the pipeline when code changes are committed to the repository.

Because Jenkins is running locally at:

```text
http://localhost:8080
```

GitHub cannot directly access this local Jenkins instance using a normal public webhook.

For the local implementation, Jenkins **Poll SCM** was configured.

The configured schedule is:

```text
H/2 * * * *
```

Jenkins periodically checks the GitHub repository for new commits.

The automatic workflow is therefore:

```text
Developer Changes Code
        ↓
git add
        ↓
git commit
        ↓
git push origin main
        ↓
GitHub Repository Updated
        ↓
Jenkins Poll SCM Detects Commit
        ↓
Pipeline Starts Automatically
        ↓
Build
        ↓
Test
        ↓
Docker Build
        ↓
Deploy
```

No manual **Build Now** action is required when Jenkins detects a new commit.

---

# Jenkins Automatic Trigger Verification

The automatic trigger was tested by modifying the repository and pushing a new commit.

A test update was added to the README and pushed using Git.

Jenkins detected the new commit automatically.

The user did **not** manually select:

```text
Build Now
```

Jenkins automatically started:

```text
Build #4
```

This verified that Poll SCM was correctly detecting repository changes.

---

# Final Jenkins Verification

The Jenkins environment configuration was later committed to the repository as:

```text
Dockerfile.jenkins
```

Commit:

```text
18e9729
```

After this commit was pushed to the `main` branch, Jenkins again detected the change automatically.

Jenkins automatically executed:

```text
Build #5
```

Final result:

```text
SUCCESS
```

This provides final verification that the automatic Jenkins CI/CD pipeline is working correctly.

---

# Task 2 Complete Workflow

```text
Source Code Change
        │
        ▼
git add
        │
        ▼
git commit
        │
        ▼
git push origin main
        │
        ▼
GitHub Repository
        │
        ▼
Jenkins Poll SCM
        │
        ▼
New Commit Detected
        │
        ▼
Jenkins Pipeline Starts
        │
        ▼
Checkout Repository
        │
        ▼
Build
npm install
        │
        ▼
Start Application
        │
        ▼
Test
npm test
        │
        ├──────── Failed
        │             │
        │             ▼
        │        Pipeline Stops
        │
        ▼
      Passed
        │
        ▼
Docker Build
        │
        ▼
nodejs-demoapp:jenkins
        │
        ▼
Remove Previous Container
        │
        ▼
Deploy New Container
        │
        ▼
nodejs-jenkins-app
        │
        ▼
localhost:3005
```

---

# CI and CD in the Jenkins Pipeline

## Continuous Integration — CI

The CI section performs:

```text
Git Push
   ↓
Jenkins Detects Commit
   ↓
Checkout
   ↓
Dependency Installation
   ↓
Application Startup
   ↓
Automated Testing
```

This verifies that new source-code changes work correctly before deployment.

## Continuous Deployment — CD

After successful testing:

```text
Tests Passed
     ↓
Docker Image Build
     ↓
Remove Previous Container
     ↓
Deploy New Container
     ↓
Application Available
```

This automatically deploys the successfully tested application.

---

# Task 1 vs Task 2

| Feature                    | Task 1                                  | Task 2                    |
| -------------------------- | --------------------------------------- | ------------------------- |
| CI/CD Tool                 | GitHub Actions                          | Jenkins                   |
| Source Repository          | GitHub                                  | GitHub                    |
| Trigger                    | Push to `main`                          | Poll SCM                  |
| Dependency Installation    | npm                                     | npm                       |
| Automated Testing          | Yes                                     | Yes                       |
| Docker Build               | Yes                                     | Yes                       |
| DockerHub Push             | Yes                                     | No                        |
| Docker Deployment          | Local verification                      | Jenkins deploys container |
| Image                      | `bhuvanreddy0001/nodejs-demoapp:latest` | `nodejs-demoapp:jenkins`  |
| Deployment Port            | Test ports                              | `3005`                    |
| Pipeline Configuration     | `.github/workflows/main.yml`            | `Jenkinsfile`             |
| Automatic Trigger Verified | Yes                                     | Yes                       |

---

# Combined CI/CD Architecture

The project now demonstrates two independent CI/CD pipelines.

```text
                         Developer
                             │
                         git push
                             │
                             ▼
                     GitHub Repository
                       /           \
                      /             \
                     ▼               ▼
             GitHub Actions       Jenkins
                Task 1             Task 2
                   │                  │
                   ▼                  ▼
                 Test               Build
                   │                  │
                   ▼                  ▼
              Docker Build           Test
                   │                  │
                   ▼                  ▼
             DockerHub Push      Docker Build
                   │                  │
                   ▼                  ▼
              DockerHub            Deploy
                   │                  │
                   ▼                  ▼
bhuvanreddy0001/nodejs-demoapp   Docker Container
                                      │
                                      ▼
                                localhost:3005
```

---

# Security Considerations

DockerHub credentials for Task 1 are stored using GitHub Actions Secrets:

```text
DOCKERHUB_USERNAME
DOCKERHUB_TOKEN
```

Sensitive tokens and passwords should never be committed to:

```text
README.md
Jenkinsfile
Dockerfile
Dockerfile.jenkins
package.json
source code
```

For the local Jenkins lab, Docker socket access is provided to the Jenkins container.

For production environments, Jenkins should be configured using more restrictive security and Docker permissions.

---

# Final Results

## Task 1 Result

GitHub Actions successfully performs:

```text
Push
  ↓
Test
  ↓
Docker Build
  ↓
DockerHub Push
```

Final DockerHub image:

```text
bhuvanreddy0001/nodejs-demoapp:latest
```

Task 1 was successfully implemented and verified.

---

## Task 2 Result

Jenkins successfully performs:

```text
Push
  ↓
Automatic Commit Detection
  ↓
Build
  ↓
Test
  ↓
Docker Build
  ↓
Deploy
```

The deployed application is available at:

```text
http://localhost:3005
```

Automatic pipeline execution was verified through Jenkins Build #4 and Build #5.

Final Jenkins result:

```text
Build #5 — SUCCESS
```

Task 2 was successfully implemented and verified.

---

# Conclusion

This project successfully demonstrates two approaches to implementing CI/CD for a Node.js application.

**Task 1** uses GitHub Actions to automatically test the application, build a Docker image, and publish the image to DockerHub whenever new code is pushed to the `main` branch.

```text
GitHub → GitHub Actions → Test → Docker Build → DockerHub
```

**Task 2** uses Jenkins to detect repository changes, build and test the application, create a Docker image, and automatically deploy the application as a running Docker container.

```text
GitHub → Jenkins → Build → Test → Docker Build → Deploy
```

The final project demonstrates:

* Source code management with Git and GitHub
* Automated CI/CD triggering
* GitHub Actions pipelines
* Jenkins pipelines
* Automated Node.js dependency installation
* Automated application testing
* Docker image creation
* DockerHub image publishing
* Jenkins-based Docker deployment
* Automatic detection of new commits
* Successful containerized application execution

Both **Task 1 and Task 2 have been successfully completed and verified**.
