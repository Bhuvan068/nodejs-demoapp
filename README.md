# Node.js CI/CD Pipeline using GitHub Actions and Docker

## Task 1: Automate Code Deployment Using CI/CD Pipeline

This project demonstrates the implementation of a **CI/CD pipeline for a Node.js web application using GitHub Actions and Docker**.

Whenever code is pushed to the `main` branch, GitHub Actions automatically tests the application, builds a Docker image, and pushes the latest image to DockerHub.

---
<img width="1534" height="838" alt="image" src="https://github.com/user-attachments/assets/64fb6af9-b3ad-46c1-bd8f-a0efaf4e0b5f" />

## Objective

The objective of this project is to automate the process of:

1. Testing a Node.js web application.
2. Building the application as a Docker image.
3. Authenticating with DockerHub securely.
4. Pushing the Docker image to DockerHub.
5. Automatically triggering the entire pipeline whenever new code is pushed to the `main` branch.

---

## Technologies Used

| Technology     | Purpose                        |
| -------------- | ------------------------------ |
| Node.js        | Web application runtime        |
| Express.js     | Web application framework      |
| Git            | Source code version control    |
| GitHub         | Source code repository         |
| GitHub Actions | CI/CD automation               |
| Docker         | Application containerization   |
| DockerHub      | Docker image registry          |
| PowerShell     | Local command-line environment |

---

## Project Repository

GitHub Repository:

`Bhuvan068/nodejs-demoapp`

The project is based on the `benc-uk/nodejs-demoapp` sample Node.js application and has been configured with a custom GitHub Actions CI/CD workflow for this task.

---

## Project Structure

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
├── .dockerignore
├── .gitignore
├── makefile
├── LICENSE
└── README.md
```

The workflow created specifically for this task is:

```text
.github/workflows/main.yml
```

---

# CI/CD Architecture

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
Start Node.js Application
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

# CI/CD Workflow

The GitHub Actions workflow is defined in:

```text
.github/workflows/main.yml
```

The pipeline automatically starts whenever code is pushed to the `main` branch.

```yaml
name: Node.js CI/CD Pipeline

on:
  push:
    branches:
      - main

jobs:
  test:
    name: Test Node.js App
    runs-on: ubuntu-latest

    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: 20

      - name: Install dependencies
        working-directory: ./src
        run: npm install

      - name: Start application
        working-directory: ./src
        run: npm start &

      - name: Wait for application
        run: sleep 5

      - name: Run tests
        working-directory: ./src
        run: npm test

  build-and-push:
    name: Build and Push Docker Image
    needs: test
    runs-on: ubuntu-latest

    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Login to DockerHub
        uses: docker/login-action@v3
        with:
          username: ${{ secrets.DOCKERHUB_USERNAME }}
          password: ${{ secrets.DOCKERHUB_TOKEN }}

      - name: Build Docker image
        run: docker build -f build/Dockerfile -t ${{ secrets.DOCKERHUB_USERNAME }}/nodejs-demoapp:latest .

      - name: Push Docker image
        run: docker push ${{ secrets.DOCKERHUB_USERNAME }}/nodejs-demoapp:latest
```

---

# Pipeline Stages

## 1. Trigger

The workflow automatically starts whenever code is pushed to:

```text
main
```

The trigger configuration is:

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
Git Commit
    ↓
Git Push
    ↓
GitHub Actions Automatically Starts
```

No manual execution of the pipeline is required.

---

## 2. Checkout Source Code

GitHub Actions first retrieves the latest version of the project.

```yaml
- name: Checkout code
  uses: actions/checkout@v4
```

This makes the repository files available to the GitHub Actions runner.

---

## 3. Setup Node.js

The application requires Node.js 20 or newer.

The workflow configures Node.js 20 using:

```yaml
- name: Setup Node.js
  uses: actions/setup-node@v4
  with:
    node-version: 20
```

---

## 4. Install Dependencies

The Node.js application is located inside the `src` directory.

Dependencies are installed using:

```yaml
- name: Install dependencies
  working-directory: ./src
  run: npm install
```

The required packages are defined in:

```text
src/package.json
```

---

## 5. Start the Application

The application must be running before its integration tests can execute.

GitHub Actions starts the Node.js server using:

```yaml
- name: Start application
  working-directory: ./src
  run: npm start &
```

The application runs on:

```text
Port 3000
```

The pipeline then waits briefly for the server to become available.

```yaml
- name: Wait for application
  run: sleep 5
```

---

## 6. Automated Testing

The project contains HTTP-based integration tests.

The tests are executed using:

```bash
npm test
```

The tests verify endpoints including:

```text
/
 /info
 /tools
 /api/monitoringdata
```

During local verification, all four requests completed successfully:

```text
4 requests processed (4 succeeded)
```

The Docker build and push job depends on the test job:

```yaml
needs: test
```

Therefore, if testing fails:

```text
Test Failed
    ↓
Pipeline Stops
    ↓
Docker image is NOT pushed
```

If testing succeeds:

```text
Test Passed
    ↓
Docker Build Starts
```

---

# Docker Containerization

The project already contains a Dockerfile at:

```text
build/Dockerfile
```

The Dockerfile uses:

```dockerfile
ARG ARCH=
ARG IMAGE_BASE=20-alpine

FROM ${ARCH}node:$IMAGE_BASE

ENV NODE_ENV production

WORKDIR /app

COPY src/package*.json ./

RUN npm install --production --silent

COPY src/. .

EXPOSE 3000

ENTRYPOINT ["npm", "start"]
```

The application is therefore packaged into a portable Docker container.

---

# Docker Image Build

After all tests pass, GitHub Actions builds the Docker image.

```yaml
- name: Build Docker image
  run: docker build -f build/Dockerfile -t ${{ secrets.DOCKERHUB_USERNAME }}/nodejs-demoapp:latest .
```

The resulting image is tagged as:

```text
bhuvanreddy0001/nodejs-demoapp:latest
```

---

# DockerHub Authentication

GitHub Actions must authenticate with DockerHub before it can push the image.

Credentials are not stored directly inside the workflow.

Instead, GitHub Repository Secrets are used.

The following secrets are configured:

```text
DOCKERHUB_USERNAME
DOCKERHUB_TOKEN
```

`DOCKERHUB_USERNAME` contains the DockerHub username.

`DOCKERHUB_TOKEN` contains a DockerHub Personal Access Token with the required image push permissions.

Authentication is performed using:

```yaml
- name: Login to DockerHub
  uses: docker/login-action@v3
  with:
    username: ${{ secrets.DOCKERHUB_USERNAME }}
    password: ${{ secrets.DOCKERHUB_TOKEN }}
```

This prevents DockerHub credentials from being exposed in the repository.

---

# Push Docker Image to DockerHub

After authentication and successful image creation, the pipeline pushes the image using:

```yaml
- name: Push Docker image
  run: docker push ${{ secrets.DOCKERHUB_USERNAME }}/nodejs-demoapp:latest
```

The resulting Docker image is available as:

```text
bhuvanreddy0001/nodejs-demoapp:latest
```

---

# Running the Application Locally

## Run Using Node.js

Move into the application directory:

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

# Building the Docker Image Locally

From the repository root:

```bash
docker build -f build/Dockerfile -t nodejs-demoapp:latest .
```

Verify the image:

```bash
docker images
```

Run the container:

```bash
docker run -d -p 3001:3000 --name nodejs-demo-container nodejs-demoapp:latest
```

The containerized application can then be accessed at:

```text
http://localhost:3001
```

---

# Pulling the CI/CD Generated Image

The image generated by GitHub Actions can be downloaded directly from DockerHub:

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

This verifies that the Docker image stored in DockerHub can be successfully downloaded and executed.

---

# CI/CD Automation Verification

To verify that the pipeline responds automatically to source-code changes, the application's home page was modified.

The heading:

```text
Node.js Demo App
```

was changed to:

```text
Node.js Demo App - CI/CD Updated
```

The change was committed:

```bash
git add src/views/index.ejs

git commit -m "Update homepage for CI/CD test"
```

It was then pushed to `main`:

```bash
git push origin main
```

This automatically triggered the GitHub Actions workflow.

The pipeline successfully:

```text
Detected push to main
        ↓
Executed Node.js tests
        ↓
Tests passed
        ↓
Built new Docker image
        ↓
Authenticated with DockerHub
        ↓
Pushed updated latest image
```

No manual pipeline execution was required.

---

# Verifying the Updated Image

After the second CI/CD run completed successfully, the updated image was downloaded:

```bash
docker pull bhuvanreddy0001/nodejs-demoapp:latest
```

Docker confirmed:

```text
Status: Downloaded newer image for bhuvanreddy0001/nodejs-demoapp:latest
```

The updated image was then started using:

```bash
docker run -d -p 3003:3000 --name cicd-updated-demo bhuvanreddy0001/nodejs-demoapp:latest
```

The updated application can be accessed at:

```text
http://localhost:3003
```

The changed heading confirms that the newly pushed source code passed through the CI/CD pipeline and was included in the newly generated Docker image.

---

# Complete Workflow

```text
Source Code Modification
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
GitHub Actions Triggered
        │
        ▼
Checkout Repository
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
        ├────────── Failed
        │              │
        │              ▼
        │         Pipeline Stops
        │
        ▼
      Passed
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
DockerHub Repository
        │
        ▼
bhuvanreddy0001/nodejs-demoapp:latest
        │
        ▼
docker pull
        │
        ▼
docker run
        │
        ▼
Running Containerized Web Application
```

---

# CI vs CD in This Project

## Continuous Integration (CI)

The CI section handles:

```text
Code Push
   ↓
Checkout
   ↓
Node.js Setup
   ↓
Dependency Installation
   ↓
Application Startup
   ↓
Automated Testing
```

This ensures that new code is tested before a new Docker image is published.

## Continuous Delivery (CD)

The CD section handles:

```text
Successful Tests
      ↓
DockerHub Authentication
      ↓
Docker Image Build
      ↓
DockerHub Push
```

This ensures that a new container image is automatically published after successful testing.

DockerHub acts as the **container image registry**. It stores and distributes the generated image. It is not itself the production application hosting environment.

---

# Security

DockerHub credentials are stored using **GitHub Actions Secrets** rather than being written directly in the workflow.

The pipeline uses:

```text
DOCKERHUB_USERNAME
DOCKERHUB_TOKEN
```

Sensitive tokens should never be committed to:

```text
main.yml
README.md
Dockerfile
package.json
source code
```

---

# Testing Result

The Node.js application's integration tests completed successfully:

```text
Root URL              PASS
Info Page             PASS
Tools Page            PASS
Monitoring API        PASS
```

Final test result:

```text
4 requests processed (4 succeeded)
```

The GitHub Actions workflow also completed successfully with:

```text
Test Node.js App                 ✓
Build and Push Docker Image      ✓
```

---

# Final Result

The CI/CD pipeline was successfully implemented using **GitHub Actions, Node.js, Docker, and DockerHub**.

The completed system automatically performs:

```text
test → build → push
```

whenever a new change is pushed to the `main` branch.

The final implementation successfully demonstrates:

* Source code management using Git and GitHub
* Automatic CI/CD triggering
* Node.js dependency installation
* Automated integration testing
* Docker image creation
* Secure DockerHub authentication
* Automated Docker image publishing
* Docker image retrieval
* Container execution
* Automatic rebuilding after source-code changes

Therefore, the required CI/CD workflow for **Task 1: Automate Code Deployment Using CI/CD Pipeline** has been successfully implemented and verified.
