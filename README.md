# Node.js DevOps Automation — GitHub Actions, Jenkins, Docker and Terraform

This repository demonstrates three DevOps automation tasks implemented for a Node.js web application.

* **Task 1:** Automate Code Deployment using GitHub Actions and Docker
* **Task 2:** Create a Simple Jenkins Pipeline for CI/CD
* **Task 3:** Infrastructure as Code (IaC) with Terraform and Docker

The project demonstrates CI/CD automation, containerization, automated testing, Docker image publishing, Jenkins-based deployment, and Infrastructure as Code.

---

# Project Repository

```text
Bhuvan068/nodejs-demoapp
```

The project is based on the `benc-uk/nodejs-demoapp` sample Node.js application and has been configured with custom GitHub Actions, Jenkins, Docker, and Terraform automation.

---

# Technologies Used

| Technology                | Purpose                                   |
| ------------------------- | ----------------------------------------- |
| Node.js                   | Web application runtime                   |
| Express.js                | Web application framework                 |
| npm                       | Dependency management and testing         |
| Git                       | Source code version control               |
| GitHub                    | Source code repository                    |
| GitHub Actions            | Task 1 CI/CD automation                   |
| Jenkins                   | Task 2 CI/CD automation                   |
| Docker                    | Application containerization              |
| DockerHub                 | Docker image registry                     |
| Terraform                 | Task 3 Infrastructure as Code             |
| Docker Terraform Provider | Manage Docker resources through Terraform |
| Nginx                     | Test infrastructure for Terraform         |
| PowerShell                | Local command-line environment            |

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
├── terraform/
│   ├── main.tf
│   ├── .terraform.lock.hcl
│   └── .gitignore
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

# Overall DevOps Architecture

```text
                         Developer
                             │
                             │ git push
                             ▼
                      GitHub Repository
                      /       |        \
                     /        |         \
                    ▼         ▼          ▼
            GitHub Actions  Jenkins   Terraform
               Task 1       Task 2      Task 3
                  │            │           │
                  ▼            ▼           ▼
                Test         Build        Plan
                  │            │           │
                  ▼            ▼           ▼
            Docker Build      Test        Apply
                  │            │           │
                  ▼            ▼           ▼
            DockerHub      Docker Build  Docker
                  │            │        Infrastructure
                  ▼            ▼           │
          Published Image    Deploy        ▼
                               │          State
                               ▼           │
                         Docker Container  ▼
                                          Destroy
```

---

# TASK 1 — Automate Code Deployment Using GitHub Actions and Docker

## Task 1 Overview

Task 1 implements a CI/CD pipeline using:

```text
GitHub Actions
      +
    Docker
      +
  DockerHub
```

Whenever code is pushed to the `main` branch, GitHub Actions automatically retrieves the latest source code, installs dependencies, tests the application, builds a Docker image, and pushes the image to DockerHub.

---

## Task 1 Objective

The objective is to automate:

1. Detect a push to the `main` branch.
2. Checkout the latest source code.
3. Configure Node.js.
4. Install application dependencies.
5. Start the application.
6. Run automated tests.
7. Authenticate with DockerHub.
8. Build a Docker image.
9. Push the Docker image to DockerHub.

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
Run Tests
    │
    ├──── FAILED ────> Pipeline Stops
    │
  PASSED
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
```

---

# GitHub Actions Workflow

The Task 1 workflow is defined in:

```text
.github/workflows/main.yml
```

The workflow starts automatically when code is pushed to:

```text
main
```

The main pipeline contains two major operations:

```text
Test Node.js Application
          ↓
Build and Push Docker Image
```

The Docker image is built and published only after the application tests succeed.

---

# Task 1 Trigger

The GitHub Actions workflow uses a push trigger.

```yaml
on:
  push:
    branches:
      - main
```

The automatic process is:

```text
Code Change
    ↓
git commit
    ↓
git push origin main
    ↓
GitHub Actions Starts Automatically
```

No manual workflow execution is required.

---

# Install Dependencies

The Node.js application is located inside:

```text
src/
```

Dependencies are installed using:

```bash
npm install
```

The application dependencies are defined in:

```text
src/package.json
```

---

# Start Application

Before integration testing, the application is started using:

```bash
npm start
```

The Node.js application runs internally on:

```text
Port 3000
```

The pipeline waits for the server to become available before running the automated tests.

---

# Automated Testing

Tests are executed using:

```bash
npm test
```

The successful Task 1 test execution reported:

```text
4 requests processed (4 succeeded)
```

The pipeline logic is:

```text
Tests
  │
  ├──── FAIL ────> Stop Pipeline
  │
 PASS
  │
  ▼
Docker Build
```

This prevents a failed application version from being published as a Docker image.

---

# Docker Containerization

The application Dockerfile is:

```text
build/Dockerfile
```

The Docker container exposes:

```text
Port 3000
```

The application is therefore packaged as a portable Docker image that can run consistently in different environments.

---

# DockerHub Authentication

GitHub Actions authenticates with DockerHub before publishing the image.

Credentials are stored securely using GitHub Repository Secrets:

```text
DOCKERHUB_USERNAME
DOCKERHUB_TOKEN
```

Sensitive DockerHub credentials are therefore not stored directly inside the repository.

---

# Docker Image Build and Push

After successful testing, GitHub Actions builds the Docker image.

Final DockerHub image:

```text
bhuvanreddy0001/nodejs-demoapp:latest
```

The image is pushed using:

```bash
docker push bhuvanreddy0001/nodejs-demoapp:latest
```

---

# Pull and Run DockerHub Image

The published image can be downloaded using:

```bash
docker pull bhuvanreddy0001/nodejs-demoapp:latest
```

It can then be executed locally:

```bash
docker run -d -p 3002:3000 --name dockerhub-demo bhuvanreddy0001/nodejs-demoapp:latest
```

The application becomes available at:

```text
http://localhost:3002
```

This verifies that the image produced by the CI/CD pipeline can be downloaded and executed successfully.

---

# Task 1 Automation Verification

The automatic workflow was verified by making a source-code modification and pushing the change.

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

This confirmed that Task 1 operates automatically.

---

# Task 1 Result

Task 1 successfully demonstrates:

* GitHub Actions CI/CD
* Automatic pipeline triggering
* Node.js dependency installation
* Automated application testing
* Docker image creation
* DockerHub authentication
* Docker image publishing
* Docker image retrieval
* Container execution

Task 1 pipeline:

```text
Push → Test → Docker Build → DockerHub Push
```

**Task 1 Status: COMPLETED**

---

---

# TASK 2 — Create a Simple Jenkins Pipeline for CI/CD

## Task 2 Overview

Task 2 implements another CI/CD pipeline for the same Node.js application using:

```text
Jenkins
   +
Docker
```

Jenkins monitors the GitHub repository for changes.

When a new commit is detected, Jenkins retrieves the latest source code and executes the CI/CD pipeline.

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
2. Connect Jenkins with GitHub.
3. Create a Jenkinsfile.
4. Build the application.
5. Run automated tests.
6. Build a Docker image.
7. Deploy the Docker container.
8. Automatically detect GitHub commits.
9. Verify the deployed application.

---

# Jenkins Environment

Jenkins was installed locally using Docker.

The base Jenkins image used was:

```text
jenkins/jenkins:lts-jdk21
```

A custom Jenkins image was created using:

```text
Dockerfile.jenkins
```

The custom Jenkins environment contains:

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

Jenkins was started using Docker:

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

Jenkins was accessible at:

```text
http://localhost:8080
```

The Docker socket allows Jenkins to build and deploy Docker containers.

> Note: Running Jenkins as root was used only for this local educational environment. Production environments should use more restrictive permissions.

---

# Jenkins Job Configuration

The Jenkins Pipeline job was created as:

```text
nodejs-demoapp-pipeline
```

Configuration:

```text
Definition: Pipeline script from SCM
SCM: Git
Branch: */main
Script Path: Jenkinsfile
```

Jenkins therefore retrieves the pipeline configuration directly from GitHub.

---

# Jenkinsfile

The CI/CD pipeline is defined in:

```text
Jenkinsfile
```

The implemented Jenkinsfile is:

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

# Jenkins Pipeline Architecture

```text
Developer
    │
    │ git push
    ▼
GitHub
    │
    ▼
Jenkins Poll SCM
    │
    ▼
New Commit Detected
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
    ▼
nodejs-demoapp:jenkins
    │
    ▼
Deploy
    │
    ▼
nodejs-jenkins-app
    │
    ▼
localhost:3005
```

---

# Jenkins Stage 1 — Build

The Build stage installs Node.js dependencies:

```text
src/
 ↓
npm install
```

Jenkins displays:

```text
Building Node.js application...
```

After successful dependency installation, the pipeline continues to testing.

---

# Jenkins Stage 2 — Test

The application is started using:

```bash
npm start &
```

Jenkins waits briefly:

```bash
sleep 5
```

The tests are then executed:

```bash
npm test
```

Successful tests allow Jenkins to continue to Docker Build.

If testing fails:

```text
Test Failed
    ↓
Pipeline Stops
```

---

# Jenkins Stage 3 — Docker Build

Jenkins builds the application Docker image using:

```bash
docker build -f build/Dockerfile -t nodejs-demoapp:jenkins .
```

The resulting image is:

```text
nodejs-demoapp:jenkins
```

---

# Jenkins Stage 4 — Deploy

Before deployment, Jenkins removes the previous container if it exists:

```bash
docker rm -f nodejs-jenkins-app || true
```

The latest application is then deployed:

```bash
docker run -d -p 3005:3000 --name nodejs-jenkins-app nodejs-demoapp:jenkins
```

Container:

```text
nodejs-jenkins-app
```

Port mapping:

```text
Host 3005 → Container 3000
```

The application was successfully verified at:

```text
http://localhost:3005
```

---

# Automatic Jenkins Trigger

Because Jenkins was running locally, GitHub could not directly reach it through a normal public webhook.

Therefore, Jenkins **Poll SCM** was used.

Schedule:

```text
H/2 * * * *
```

Jenkins periodically checks the GitHub repository for new commits.

```text
Code Change
    ↓
git commit
    ↓
git push origin main
    ↓
GitHub Updated
    ↓
Jenkins Poll SCM
    ↓
New Commit Detected
    ↓
Pipeline Starts Automatically
```

---

# Jenkins Automatic Trigger Verification

The automatic trigger was tested by modifying the repository and pushing a new commit.

Jenkins detected the change without manually clicking:

```text
Build Now
```

Jenkins automatically started:

```text
Build #4
```

A later repository change was also detected automatically.

Jenkins executed:

```text
Build #5
```

Final result:

```text
Build #5 — SUCCESS
```

This verified that Poll SCM was working correctly.

---

# Task 2 Complete Workflow

```text
Git Push
   ↓
GitHub
   ↓
Jenkins Poll SCM
   ↓
Commit Detected
   ↓
Checkout
   ↓
Build
   ↓
Test
   │
   ├──── FAIL ────> Pipeline Stops
   │
  PASS
   ↓
Docker Build
   ↓
Deploy
   ↓
nodejs-jenkins-app
   ↓
localhost:3005
```

---

# Task 2 Result

Task 2 successfully demonstrates:

* Jenkins installation using Docker
* Custom Jenkins environment
* Pipeline as Code using Jenkinsfile
* GitHub repository integration
* Automatic commit detection
* Node.js build
* Automated testing
* Docker image creation
* Docker container deployment
* Poll SCM automation
* Successful automatic Build #4
* Successful automatic Build #5

Task 2 pipeline:

```text
Push → Jenkins → Build → Test → Docker Build → Deploy
```

**Task 2 Status: COMPLETED**

---

---

# TASK 3 — Infrastructure as Code with Terraform

## Task 3 Overview

Task 3 implements **Infrastructure as Code (IaC)** using:

```text
Terraform
    +
 Docker
```

Terraform is used to define, provision, track, and destroy Docker infrastructure using configuration code.

Instead of manually creating the Docker infrastructure, it is defined inside:

```text
terraform/main.tf
```

---

# Task 3 Objective

The objective is to:

1. Install Terraform.
2. Configure the Terraform Docker provider.
3. Write Terraform configuration in `main.tf`.
4. Initialize Terraform.
5. Preview changes using `terraform plan`.
6. Provision infrastructure using `terraform apply`.
7. Verify the Docker container.
8. Check Terraform state.
9. Destroy the infrastructure using `terraform destroy`.
10. Verify that the infrastructure was removed.

---

# Task 3 Project Structure

```text
terraform/
│
├── main.tf
├── .terraform.lock.hcl
└── .gitignore
```

The `.terraform/` directory and Terraform state files are intentionally excluded from Git.

---

# Terraform Configuration

The `main.tf` configuration is:

```hcl
terraform {
  required_providers {
    docker = {
      source  = "kreuzwerker/docker"
      version = "~> 3.0"
    }
  }
}

provider "docker" {}

resource "docker_image" "nginx" {
  name         = "nginx:latest"
  keep_locally = false
}

resource "docker_container" "nginx" {
  name  = "terraform-nginx"
  image = docker_image.nginx.image_id

  ports {
    internal = 80
    external = 8085
  }
}
```

The configuration defines two resources:

```text
docker_image.nginx
docker_container.nginx
```

---

# Task 3 Architecture

```text
main.tf
   │
   ▼
Terraform
   │
   ▼
Docker Provider
   │
   ├────────────────┐
   ▼                ▼
nginx:latest    terraform-nginx
                     │
                     ▼
             Container Port 80
                     │
                     ▼
               Host Port 8085
                     │
                     ▼
          http://localhost:8085
```

---

# Step 1 — Terraform Init

The Terraform working directory was initialized using:

```powershell
terraform init
```

Terraform downloaded and installed the Docker provider:

```text
kreuzwerker/docker v3.9.0
```

Successful result:

```text
Terraform has been successfully initialized!
```

Terraform also generated:

```text
.terraform.lock.hcl
```

---

# Step 2 — Terraform Plan

Before creating infrastructure, the execution plan was checked:

```powershell
terraform plan
```

Terraform reported:

```text
Plan: 2 to add, 0 to change, 0 to destroy.
```

The two resources were:

```text
docker_image.nginx
docker_container.nginx
```

This satisfies the requirement to inspect the planned infrastructure before applying it.

---

# Step 3 — Terraform Apply

The infrastructure was provisioned using:

```powershell
terraform apply
```

Terraform requested confirmation.

```text
Enter a value: yes
```

Terraform created:

```text
docker_image.nginx
        ↓
docker_container.nginx
```

Final result:

```text
Apply complete! Resources: 2 added, 0 changed, 0 destroyed.
```

---

# Docker Infrastructure Created

Terraform created the Docker image:

```text
nginx:latest
```

and container:

```text
terraform-nginx
```

Port mapping:

```text
Host Port 8085
      ↓
Container Port 80
```

Docker verification:

```powershell
docker ps -a
```

showed:

```text
terraform-nginx
0.0.0.0:8085->80/tcp
```

The container status was running.

---

# Nginx Verification

The Terraform-created infrastructure was opened at:

```text
http://localhost:8085
```

The browser displayed:

```text
Welcome to nginx!
```

This confirmed that Terraform successfully created working Docker infrastructure.

---

# Step 4 — Terraform State

Terraform-managed resources were inspected using:

```powershell
terraform state list
```

Output:

```text
docker_container.nginx
docker_image.nginx
```

This confirms that Terraform was tracking both resources.

---

# Terraform State Protection

Terraform generates local state files such as:

```text
terraform.tfstate
terraform.tfstate.backup
```

These files are excluded using:

```text
terraform/.gitignore
```

The `.gitignore` contains:

```gitignore
.terraform/
*.tfstate
*.tfstate.*
crash.log
```

Therefore, local Terraform state and downloaded provider files are not pushed to GitHub.

The provider lock file:

```text
.terraform.lock.hcl
```

is committed to version control.

---

# Step 5 — Terraform Destroy

After verification, the infrastructure was removed using:

```powershell
terraform destroy
```

The operation was confirmed with:

```text
yes
```

Final result:

```text
Destroy complete! Resources: 2 destroyed.
```

---

# Destroy Verification

After destroying the infrastructure, Docker containers were checked again:

```powershell
docker ps -a
```

The container:

```text
terraform-nginx
```

was no longer present.

This confirmed that Terraform successfully removed all infrastructure it created.

---

# Complete Task 3 Workflow

```text
Write main.tf
     ↓
terraform init
     ↓
Docker Provider Installed
     ↓
terraform plan
     ↓
2 Resources Planned
     ↓
terraform apply
     ↓
nginx:latest
     ↓
terraform-nginx
     ↓
localhost:8085
     ↓
Welcome to nginx!
     ↓
terraform state list
     ↓
2 Resources Tracked
     ↓
terraform destroy
     ↓
2 Resources Destroyed
```

---

# Infrastructure as Code Concept

Without Terraform, Docker infrastructure can be created manually using Docker commands.

With Infrastructure as Code:

```text
Infrastructure Requirements
          ↓
        main.tf
          ↓
       Terraform
          ↓
      Docker Provider
          ↓
  Docker Infrastructure
```

This makes infrastructure:

* Repeatable
* Automated
* Version controlled
* Reproducible
* Easier to manage
* Easier to destroy and recreate

---

# Terraform Commands Used

| Command                | Purpose                        |
| ---------------------- | ------------------------------ |
| `terraform -version`   | Verify Terraform installation  |
| `terraform init`       | Initialize Terraform           |
| `terraform plan`       | Preview infrastructure changes |
| `terraform apply`      | Create infrastructure          |
| `terraform state list` | Check managed resources        |
| `terraform destroy`    | Destroy infrastructure         |
| `docker ps -a`         | Verify Docker container status |

---

# Task 3 Execution Results

### Terraform Init

```text
Terraform has been successfully initialized!
```

### Terraform Plan

```text
Plan: 2 to add, 0 to change, 0 to destroy.
```

### Terraform Apply

```text
Apply complete! Resources: 2 added, 0 changed, 0 destroyed.
```

### Docker Container

```text
terraform-nginx
0.0.0.0:8085->80/tcp
```

### Browser Verification

```text
http://localhost:8085

Welcome to nginx!
```

### Terraform State

```text
docker_container.nginx
docker_image.nginx
```

### Terraform Destroy

```text
Destroy complete! Resources: 2 destroyed.
```

---

# Task 3 GitHub Update

The Terraform files added to GitHub are:

```text
terraform/main.tf
terraform/.terraform.lock.hcl
terraform/.gitignore
```

The Task 3 Terraform commit was successfully pushed to the `main` branch.

Remote branch after push:

```text
8237b20
```

State files and `.terraform/` were intentionally excluded.

---

# Task 3 Result

Task 3 successfully demonstrates:

* Infrastructure as Code
* Terraform configuration
* Terraform Docker provider
* Docker image provisioning
* Docker container provisioning
* Infrastructure planning
* Infrastructure deployment
* Terraform state management
* Docker verification
* Nginx verification
* Infrastructure destruction
* Git version control for Terraform configuration

Task 3 workflow:

```text
Code → Init → Plan → Apply → Verify → State → Destroy
```

**Task 3 Status: COMPLETED**

---

# Task 1 vs Task 2 vs Task 3

| Feature                | Task 1            | Task 2           | Task 3                    |
| ---------------------- | ----------------- | ---------------- | ------------------------- |
| Main Tool              | GitHub Actions    | Jenkins          | Terraform                 |
| Purpose                | CI/CD             | CI/CD            | Infrastructure as Code    |
| Source Repository      | GitHub            | GitHub           | GitHub                    |
| Trigger                | Push to `main`    | Poll SCM         | Manual Terraform commands |
| Node.js Build          | Yes               | Yes              | No                        |
| Automated Testing      | Yes               | Yes              | No                        |
| Docker Image Build     | Yes               | Yes              | Terraform manages image   |
| DockerHub Push         | Yes               | No               | No                        |
| Container Deployment   | Verification      | Jenkins          | Terraform                 |
| Infrastructure State   | No                | No               | Yes                       |
| Destroy Infrastructure | Manual            | Manual           | Terraform                 |
| Main Configuration     | `main.yml`        | `Jenkinsfile`    | `main.tf`                 |
| Verification Port      | Docker test ports | `3005`           | `8085`                    |
| Final Verification     | DockerHub image   | Jenkins Build #5 | Nginx + Terraform destroy |

---

# Combined Task 1, Task 2 and Task 3 Architecture

```text
                              Developer
                                  │
                                  ▼
                           GitHub Repository
                         /        │        \
                        /         │         \
                       ▼          ▼          ▼
               GitHub Actions   Jenkins   Terraform
                   TASK 1       TASK 2      TASK 3
                     │             │           │
                     ▼             ▼           ▼
                    Test          Build        Init
                     │             │           │
                     ▼             ▼           ▼
                Docker Build      Test         Plan
                     │             │           │
                     ▼             ▼           ▼
               DockerHub Push Docker Build    Apply
                     │             │           │
                     ▼             ▼           ▼
                 DockerHub       Deploy      Docker
                                   │       Infrastructure
                                   ▼           │
                             localhost:3005    ▼
                                             State
                                               │
                                               ▼
                                            Destroy
```

---

# CI/CD and IaC Relationship

The three tasks demonstrate different parts of DevOps automation.

## Continuous Integration

```text
Code Change
    ↓
Build
    ↓
Test
```

Both GitHub Actions and Jenkins perform Continuous Integration.

## Continuous Delivery / Deployment

```text
Tests Passed
     ↓
Docker Image
     ↓
Publish / Deploy
```

Task 1 publishes the Docker image to DockerHub.

Task 2 deploys the Docker container locally through Jenkins.

## Infrastructure as Code

```text
Infrastructure Code
       ↓
Terraform Plan
       ↓
Terraform Apply
       ↓
Infrastructure
       ↓
Terraform State
       ↓
Terraform Destroy
```

Task 3 demonstrates infrastructure lifecycle management.

---

# Security and Repository Considerations

DockerHub credentials used by Task 1 are stored using GitHub Secrets:

```text
DOCKERHUB_USERNAME
DOCKERHUB_TOKEN
```

Sensitive credentials should never be directly stored in:

```text
README.md
Jenkinsfile
Dockerfile
Dockerfile.jenkins
main.tf
package.json
Source Code
```

For Task 3, Terraform state files are excluded from Git using:

```gitignore
.terraform/
*.tfstate
*.tfstate.*
crash.log
```

For Task 2, Jenkins Docker socket access was used for the local educational environment. A production environment should use more restrictive permissions.

---

# Final Results

## Task 1

```text
Git Push
   ↓
GitHub Actions
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

**Result: SUCCESS**

---

## Task 2

```text
Git Push
   ↓
Jenkins Detects Commit
   ↓
Build
   ↓
Test
   ↓
Docker Build
   ↓
Deploy
```

Deployed application:

```text
http://localhost:3005
```

Final Jenkins verification:

```text
Build #5 — SUCCESS
```

**Result: SUCCESS**

---

## Task 3

```text
main.tf
   ↓
terraform init
   ↓
terraform plan
   ↓
terraform apply
   ↓
terraform-nginx
   ↓
localhost:8085
   ↓
terraform state list
   ↓
terraform destroy
```

Results:

```text
Plan: 2 to add, 0 to change, 0 to destroy.

Apply complete! Resources: 2 added, 0 changed, 0 destroyed.

docker_container.nginx
docker_image.nginx

Destroy complete! Resources: 2 destroyed.
```

**Result: SUCCESS**

---

# Final DevOps Workflow

The complete project can be summarized as:

```text
                         SOURCE CODE
                             │
                             ▼
                           GitHub
                 ┌───────────┼───────────┐
                 │           │           │
                 ▼           ▼           ▼
          GitHub Actions   Jenkins    Terraform
              Task 1        Task 2      Task 3
                 │           │           │
                 ▼           ▼           ▼
               Test         Build       Plan
                 │           │           │
                 ▼           ▼           ▼
          Docker Build      Test        Apply
                 │           │           │
                 ▼           ▼           ▼
          DockerHub Push Docker Build   Docker
                             │       Infrastructure
                             ▼           │
                           Deploy        ▼
                             │          State
                             ▼           │
                       Application       ▼
                                      Destroy
```

---

# Conclusion

This project successfully demonstrates three important DevOps concepts for application and infrastructure automation.

**Task 1** uses GitHub Actions to automatically test the Node.js application, build a Docker image, and publish it to DockerHub whenever new code is pushed to the `main` branch.

```text
GitHub → GitHub Actions → Test → Docker Build → DockerHub
```

**Task 2** uses Jenkins to detect repository changes, build and test the application, create a Docker image, and automatically deploy the application as a running Docker container.

```text
GitHub → Jenkins → Build → Test → Docker Build → Deploy
```

**Task 3** uses Terraform to define Docker infrastructure as code, preview the infrastructure changes, provision an Nginx Docker container, track the resources using Terraform state, and destroy the infrastructure when it is no longer required.

```text
main.tf → Terraform → Plan → Apply → Docker → State → Destroy
```

The complete project demonstrates:

* Git and GitHub source code management
* GitHub Actions CI/CD
* Jenkins CI/CD
* Automated pipeline triggering
* Node.js dependency installation
* Automated application testing
* Docker image creation
* DockerHub image publishing
* Docker container deployment
* Infrastructure as Code
* Terraform Docker provider
* Terraform planning
* Terraform infrastructure provisioning
* Terraform state management
* Infrastructure destruction
* Git-based infrastructure configuration
* Successful application and infrastructure verification

Therefore, **Task 1, Task 2, and Task 3 have been successfully implemented, tested, and verified**.
