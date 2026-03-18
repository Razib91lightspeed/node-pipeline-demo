# Node Pipeline Demo

A simple Node.js / Express application that demonstrates how to containerize a web service with Docker and deploy it automatically to [Fly.io](https://fly.io) using a GitHub Actions CI/CD pipeline.

[![Docker Hub](https://img.shields.io/docker/pulls/razibhasan2/node-pipeline-demo)](https://hub.docker.com/r/razibhasan2/node-pipeline-demo)
[![Deploy to Fly.io](https://img.shields.io/badge/deployed%20on-Fly.io-purple)](https://node-pipeline-demo.fly.dev/)

---

## Live Demo

**[https://node-pipeline-demo.fly.dev/](https://node-pipeline-demo.fly.dev/)**

---

## Table of Contents

- [About the Project](#about-the-project)
- [Tech Stack](#tech-stack)
- [Prerequisites](#prerequisites)
- [Getting Started](#getting-started)
  - [Install Dependencies](#install-dependencies)
  - [Run Locally](#run-locally)
  - [Run with Docker](#run-with-docker)
- [API Endpoints](#api-endpoints)
- [CI/CD Pipeline](#cicd-pipeline)
- [Environment Variables](#environment-variables)
- [Deployment](#deployment)

---

## About the Project

This project serves as a minimal, production-ready reference for:

- Building a **Node.js** web service with **Express.js**
- Containerizing it with **Docker** (multi-platform: `linux/amd64` and `linux/arm64`)
- Publishing the image to **Docker Hub**
- Deploying automatically to **Fly.io** on every push to `main`

The application itself is intentionally simple — a single HTTP endpoint that returns a greeting — so the focus stays on the pipeline and deployment workflow.

---

## Tech Stack

| Layer | Technology |
|-------|-----------|
| Runtime | Node.js 20 (Alpine) |
| Web framework | Express.js 4.x |
| Containerization | Docker / Docker Buildx |
| Container registry | Docker Hub |
| Hosting | Fly.io |
| CI/CD | GitHub Actions |

---

## Prerequisites

- [Node.js](https://nodejs.org/) 18 or later
- [npm](https://www.npmjs.com/) (bundled with Node.js)
- [Docker](https://www.docker.com/) (optional, for container-based workflows)
- [flyctl](https://fly.io/docs/hands-on/install-flyctl/) (optional, for manual Fly.io deployments)

---

## Getting Started

### Install Dependencies

```bash
npm install
```

### Run Locally

```bash
npm start
```

The server starts on **http://localhost:3000**.

### Run with Docker

**Pull the pre-built image from Docker Hub:**

```bash
docker pull razibhasan2/node-pipeline-demo:latest
docker run -p 3000:3000 razibhasan2/node-pipeline-demo:latest
```

**Or build the image locally:**

```bash
docker build -t node-pipeline-demo .
docker run -p 3000:3000 node-pipeline-demo
```

The server is then available at **http://localhost:3000**.

---

## API Endpoints

| Method | Path | Description | Response |
|--------|------|-------------|----------|
| `GET` | `/` | Health check / greeting | `Hello from Docker Pipeline!` |

---

## CI/CD Pipeline

Two GitHub Actions workflows run automatically on every push to `main`:

### 1. `docker-publish.yml` — Build, Push & Deploy

1. Checks out the repository
2. Sets up Docker Buildx for multi-platform builds
3. Authenticates with Docker Hub using repository secrets
4. Builds and pushes a multi-platform image (`linux/amd64`, `linux/arm64`) to Docker Hub as `razibhasan2/node-pipeline-demo:latest`
5. Installs `flyctl` and deploys the updated image to Fly.io

### 2. `fly-deploy.yml` — Fly.io Deploy

A dedicated, concurrency-safe workflow that:

1. Checks out the repository
2. Installs `flyctl`
3. Runs `flyctl deploy --remote-only` to trigger a remote build and deploy on Fly.io

Concurrency is configured so that only one deployment runs at a time; any in-progress deployment is cancelled when a newer commit is pushed.

---

## Environment Variables

No environment variables are required to run the application itself.

The CI/CD workflows require the following **GitHub repository secrets**:

| Secret | Description |
|--------|-------------|
| `DOCKER_USERNAME` | Docker Hub username |
| `DOCKER_PASSWORD` | Docker Hub access token or password |
| `FLY_API_TOKEN` | Fly.io API token (generate with `flyctl auth token`) |

---

## Deployment

The application is deployed to Fly.io. The configuration lives in `fly.toml`:

| Setting | Value |
|---------|-------|
| App name | `node-pipeline-demo` |
| Primary region | `arn` (Stockholm) |
| Internal port | `3000` |
| HTTPS enforced | Yes |
| VM size | Shared CPU, 1 GB RAM |

To deploy manually:

```bash
flyctl deploy
```
