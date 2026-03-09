🐳 PHASE 2 — Containerization
Objective

Containerization is the process of packaging an application along with its dependencies into a lightweight, portable container.
Containers ensure that applications run consistently across environments such as local development, CI pipelines, and Kubernetes clusters.

In this project, the microservices are containerized using Docker.

Microservices in this application:

patient-service      → Node.js
application-service  → Node.js
order-service        → Spring Boot (Java)
1. Verify Docker Installation

Before containerizing the services, ensure Docker is installed and running.

Command
docker --version
Expected Output
Docker version 24.x.x

Verify Docker engine:

docker info
2. Troubleshooting Docker Installation
Issue
docker: command not found
Cause

Docker is not installed or not added to PATH.

Solution

Install Docker Desktop from:

https://www.docker.com/products/docker-desktop/

After installation:

Start Docker Desktop

Ensure Docker daemon is running

Verify again:

docker --version
3. Dockerfile Creation

Each microservice requires a Dockerfile that defines how the container image is built.

A Dockerfile specifies:

Base Image
Working Directory
Application Dependencies
Application Source Code
Startup Command
4. Dockerfile for Node.js Microservice (patient-service)

Navigate to:

patient-service/src

Create a file named:

Dockerfile
Dockerfile
FROM node:18

WORKDIR /app

COPY package*.json ./

RUN npm install

COPY . .

EXPOSE 3000

CMD ["node", "index.js"]
Explanation
Instruction	Purpose
FROM node:18	Base image with Node runtime
WORKDIR /app	Sets working directory inside container
COPY package*.json	Copies dependency configuration
RUN npm install	Installs dependencies
COPY . .	Copies application code
EXPOSE 3000	Opens application port
CMD	Starts Node application
5. Dockerfile for Node.js Microservice (application-service)

Navigate to:

application-service/src

Create Dockerfile:

FROM node:18

WORKDIR /app

COPY package*.json ./

RUN npm install

COPY . .

EXPOSE 3001

CMD ["node", "index.js"]
6. Dockerfile for Spring Boot Microservice (order-service)

Navigate to:

order-service

Create a Dockerfile.

Dockerfile
FROM openjdk:17-jdk-slim

WORKDIR /app

COPY target/order-service-0.0.1-SNAPSHOT.jar app.jar

EXPOSE 8080

ENTRYPOINT ["java","-jar","app.jar"]
Explanation
Instruction	Purpose
FROM openjdk:17-jdk-slim	Base Java runtime
WORKDIR /app	Container working directory
COPY target JAR	Copies built application
EXPOSE 8080	Opens application port
ENTRYPOINT	Starts Spring Boot service
7. Build Docker Images

Once Dockerfiles are created, build the container images.

Build Patient Service Image

Navigate to:

patient-service/src

Run:

docker build -t patient-service:v1 .
Build Application Service Image

Navigate to:

application-service/src

Run:

docker build -t application-service:v1 .
Build Order Service Image

Navigate to:

order-service

Run:

docker build -t order-service:v1 .
8. Verify Docker Images

List all Docker images.

docker images

Expected output:

REPOSITORY            TAG
patient-service       v1
application-service   v1
order-service         v1
9. Run Containers Locally

Run containers to verify that the services work correctly.

Run Patient Service Container
docker run -p 3000:3000 patient-service:v1

Test API:

http://localhost:3000/patients
Run Application Service Container
docker run -p 3001:3001 application-service:v1

Test API:

http://localhost:3001
Run Order Service Container
docker run -p 8080:8080 order-service:v1

Test API:

http://localhost:8080/orders
10. Troubleshooting Container Issues
Issue
port already in use
Cause

Another service is already using the port.

Solution

Stop container:

docker ps
docker stop <container-id>
Issue
Module not found
Cause

Dependencies were not installed.

Solution

Rebuild image:

docker build --no-cache -t patient-service:v1 .
Issue
Application fails to start
Cause

Incorrect startup command in Dockerfile.

Solution

Verify CMD or ENTRYPOINT in Dockerfile.

11. Container Architecture

After containerization the architecture becomes:

Client
   │
   ▼
Docker Containers
   │
   ├── patient-service
   ├── application-service
   └── order-service

Each service runs independently inside its container.

12. DevOps Pipeline Context

Containerization enables consistent deployments in Kubernetes.

Future CI/CD workflow:

Developer Push Code
        ↓
Azure DevOps CI Pipeline
        ↓
Build Application
        ↓
Docker Build
        ↓
Push Image → Azure Container Registry
        ↓
CD Pipeline
        ↓
Deploy Containers → AKS
13. Validation Checklist
Validation Step	Status
Docker Installed	✅
Dockerfiles Created	✅
Images Built Successfully	✅
Containers Running	✅
APIs Accessible	✅
14. Next Phase

Once container images are verified locally, the next phase is Cloud Deployment.

Push Images → Azure Container Registry
Provision Infrastructure → Terraform
Deploy → Azure Kubernetes Service
Manage Deployments → Helm Charts