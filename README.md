# Node.js - Sample Web Application

This is a sample Node.js web app using the Express framework and EJS templates.

The app has been designed with cloud native demos & containers in mind, in order to provide a real working application for deployment, something more than "hello-world" but with the minimum of pre-reqs. It is not intended as a complete example of a fully functioning architecture or complex software design.

Typical uses would be deployment to Kubernetes, demos of Docker, CI/CD (build pipelines are provided), deployment to cloud (Azure) monitoring, auto-scaling

The app has several basic pages accessed from the top navigation menu, some of which are only lit up when certain configuration variables are set (see 'Optional Features' below):

- **'Info'** - Will show system & runtime information, and will also display if the app is running from within a Docker container and Kubernetes.
- **'Tools'** - Some tools useful in demos, such a forcing CPU load (for autoscale demos), and error/exception pages for use with App Insights or other monitoring tool.
- **'Monitor'** - Display realtime monitoring data, showing memory usage/total and process CPU load.
- **'Weather'** - (Optional) Gets the location of the client page (with HTML5 Geolocation). The resulting location is used to fetch weather data from the [OpenWeather](https://openweathermap.org/) API
- **'Todo'** - (Optional) This is a small todo/task-list app which uses MongoDB as a database.
- **'User Account'** - (Optional) When configured with Azure AD (application client id) user login button will be enabled, and an user-account details page enabled, which calls the Microsoft Graph API

![](https://img.shields.io/github/last-commit/benc-uk/nodejs-demoapp) ![](https://img.shields.io/github/release-date/benc-uk/nodejs-demoapp) ![](https://img.shields.io/github/v/release/benc-uk/nodejs-demoapp) ![](https://img.shields.io/github/commit-activity/y/benc-uk/nodejs-demoapp)

## Screens

![screen](https://user-images.githubusercontent.com/14982936/55620043-dfe96480-5791-11e9-9746-3b42a3a41e5f.png)
![screen](https://user-images.githubusercontent.com/14982936/55620045-dfe96480-5791-11e9-94f3-6d788ed447c1.png)


# 🧑‍💻 Running and Testing Locally

### Pre-reqs

- Be using Linux, WSL or MacOS, with bash, make etc
- [Node.js](https://nodejs.org/en/) - for running locally, linting, running tests etc
- [Docker](https://docs.docker.com/get-docker/) - for running as a container, or building images
- [Azure CLI](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli-linux) - for deployment to Azure



### Makefile

A standard GNU Make file is provided to help with running and building locally.

```txt
$ make

help                 💬 This help message
lint                 🔎 Lint & format, will not fix but sets exit code on error
lint-fix             📜 Lint & format, will try to fix errors and modify code
image                🔨 Build container image from Dockerfile
push                 📤 Push container image to registry
run                  🏃 Run locally using Node.js
deploy               🚀 Deploy to Azure Container App
undeploy             💀 Remove from Azure
test                 🚦 Run integration tests, server must be running
test-report          🤡 Tests but with JUnit output, server must be running
clean                🧹 Clean up project
```

Make file variables and default values, pass these in when calling `make`, e.g. `make image IMAGE_REPO=blah/foo`

| Makefile Variable | Default                            |
| ----------------- | ---------------------------------- |
| IMAGE_REG         | ghcr<span>.</span>io               |
| IMAGE_REPO        | benc-uk/nodejs-demoapp             |
| IMAGE_TAG         | latest                             |
| AZURE_RES_GROUP   | demoapps                           |
| AZURE_REGION      | northeurope                        |
| TEST_BASE_URL     | http://<span>localhost</span>:3000 |
| TEST_FILES        | base-tests.http                    |

The web app will be listening on the standard Express port of 3000, but this can be changed by setting the `PORT` environmental variable.

# 📦 Containers

Public container image is [available on GitHub Container Registry](https://github.com/users/benc-uk/packages/container/package/nodejs-demoapp).

Run in a container with:

```bash
docker run --rm -it -p 3000:3000 ghcr.io/benc-uk/nodejs-demoapp:latest
```

Should you want to build your own container, use `make image` and the above variables to customise the name & tag.

## Kubernetes

The app can easily be deployed to Kubernetes using Helm, see [deploy/kubernetes/readme.md](deploy/kubernetes/readme.md) for details

# 🏗️ GitHub Actions CI/CD

A set of GitHub Actions workflows are included for CI / CD. Automated builds for PRs are run in GitHub hosted runners validating the code (linting and tests) and building dev images. When code is merged into main, then automated deployment to AKS is done using Helm.

[![CD Release - AKS](https://github.com/benc-uk/nodejs-demoapp/actions/workflows/cd-release-aks.yaml/badge.svg)](https://github.com/benc-uk/nodejs-demoapp/actions/workflows/cd-release-aks.yaml)

# 🧪 Testing

This project uses a HTTP files located in `src/tests/` that can be used a few different ways, you can install the [VSCode REST Client](https://marketplace.visualstudio.com/items?itemName=humao.rest-client) or [httpYac](https://marketplace.visualstudio.com/items?itemName=anweber.vscode-httpyac) allowing you to interactively run requests from VSCode

You can interactively run & send the requests in the `src/tests` file using these extensions, but the main reason to use _httpYac_, is it has a much richer language & the support of assertions which can turn the request files into integration tests too 👌

_httpYac_ has a command line tool for running tests and .http files which forms the basis of the `make test` and `make test-report` makefile targets.

## Running Tests

To run the tests, in one shell session run `make run` and open a second separate shell run `make test`. You can set `TEST_BASE_URL` to point the tests at a different URL, host or port, and set `TEST_FILES` to a glob that matches the files you want to run from the src/tests/ directory.

