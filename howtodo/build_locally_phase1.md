Below is the exact order used in real enterprise DevOps projects (and this will also impress interviewers).

🧭 COMPLETE ORDER OF IMPLEMENTATION (FROM ZERO → PRODUCTION)

We will divide it into 6 phases.

Phase 1 — Code Preparation
Phase 2 — Containerization
Phase 3 — Infrastructure Setup (Terraform)
Phase 4 — Kubernetes Deployment Setup (Helm)
Phase 5 — CI Pipeline
Phase 6 — CD Pipeline
Phase 7 — Monitoring & Logging

🚀 PHASE 1 — Prepare the Application Code

You already have the microservices:

application-service (NodeJS)
patient-service (NodeJS)
order-service (Spring Boot)

=============================================================
Step 1

Clone / prepare repository.

hackathon-usecase
 ├ application-service
 ├ patient-service
 ├ order-service
 ├ terraform
 ├ helm
 └ azure-pipelines
===============================================================

🔎 Step 2 — Test the Microservices Locally (Before Docker)
Goal

Verify that each microservice runs correctly on your local machine before containerizing it.

If the application itself is broken, Docker and Kubernetes will also fail.

Think of it like this:

Code → Works Locally → Containerize → Deploy → Scale

If it doesn't work locally, everything after that will fail.

🧠 Why This Step Is Critical (Production Reality)

In real production environments:

If you skip local testing	Result
Code has missing dependencies	Docker build fails
Wrong port configuration	Kubernetes service unreachable
Database config wrong	Pod crashes
Runtime errors	CrashLoopBackOff

So DevOps engineers always validate locally first.

📦 Your Microservices

From your repository:

application-service (NodeJS)
patient-service (NodeJS)
order-service (Spring Boot / Java)

Each must run individually.
-----
🧩 Step 2.1 — Run Patient Service (NodeJS)

Navigate to the service directory.

cd patient-service/src

Install dependencies.

npm install

Run the service.

node index.js

If successful you should see something like:

Server started on port 3000
Test the API

Use curl or Postman.

Example:

curl http://localhost:3000/patients

Expected response:

[
 { "id":1, "name":"John" }
]
------

🧩 Step 2.2 — Run Application Service (NodeJS)

Navigate to the directory.

cd application-service/src

Install dependencies.

npm install

Run it.

node index.js

Test:

curl http://localhost:3001
------

🧩 Step 2.3 — Run Order Service (Spring Boot)

Go to the order-service.

cd order-service

Build the application.

mvn clean package

This produces:

target/order-service.jar

Run the jar file.

java -jar target/order-service.jar

You should see logs like:

Tomcat started on port(s): 8080
Started OrderServiceApplication
🧪 Test the API

Example:

curl http://localhost:8080/orders

Expected output:

[
 { "orderId":101, "patientId":1 }
]
🔎 Verify Ports

Each microservice should run on different ports.

Example:

Service	Port
patient-service	3000
application-service	3001
order-service	8080

Check using:

netstat -tulnp
⚠️ Common Issues You May Encounter
1️⃣ Missing Dependencies

Error:

module not found

Fix:

npm install
2️⃣ Maven Build Failure

Error:

BUILD FAILED

Fix:

mvn clean install
3️⃣ Port Already Used

Error:

Port 3000 already in use

Find process:

lsof -i :3000

Kill it:

kill -9 <pid>
🧠 DevOps Best Practice

Before containerizing, ensure:

✔ Service starts successfully
✔ APIs return responses
✔ No runtime errors
✔ Ports are accessible

Checklist:

Application starts
Logs look clean
Endpoints reachable
Dependencies installed

❌ Problem

Your terminal shows:

bash: npm: command not found
bash: node: command not found

This means Node.js is NOT installed on your system (or not added to PATH).

Since your patient-service and application-service are NodeJS microservices, you must install Node first.

✅ Step 1 — Install Node.js

Go to the official site:

👉 https://nodejs.org

Download LTS version.

Choose:

Node.js LTS (Recommended)

During installation make sure this is checked:

✔ Add to PATH
✅ Step 2 — Restart Terminal

After installation:

Close the terminal and open Git Bash again.

Then verify installation.

node -v

Example output:

v20.10.0

Now check npm:

npm -v

Example:

10.2.3

If you see versions → installation successful.

✅ Step 3 — Go to Patient Service

Navigate again:

cd ~/hackathon-usecase/patient-service/src

Check files:

ls

You should see:

index.js
⚠️ IMPORTANT THING

Your folder does not contain package.json.

Node projects normally require:

package.json

Since it's missing, run:

npm init -y

This will create:

package.json
✅ Step 4 — Run the Service

Now start the service.

node index.js

Expected output:

Server running on port 3000
🧪 Step 5 — Test the API

Open browser or curl.

http://localhost:3000

or

curl localhost:3000
🧠 Why This Happened

Your system currently has:

Git Bash

But Node runtime is missing.

NodeJS microservices require:

Node Runtime
npm Package Manager
🚀 After This Works

Next steps in the hackathon:

Step 1  Run services locally  ✅
Step 2  Create Dockerfile
Step 3  Build Docker Image
Step 4  Push to ACR
Step 5  Deploy to AKS

-------

❌ Error You Got

From your screenshot:

Error: Cannot find module 'express'
code: 'MODULE_NOT_FOUND'

This means:

👉 Your application is trying to use the Express framework, but it is not installed.

Your index.js probably contains something like:

const express = require("express");

But Node cannot find the express package.

🧠 Why This Happened

Node projects require dependencies installed via npm.

Normally this is done using a package.json file.

Your folder currently only has:

index.js

So the dependencies must be installed manually.

✅ Step 1 — Initialize Node Project

Inside this directory:

patient-service/src

Run:

npm init -y

This creates:

package.json

Example:

{
  "name": "patient-service",
  "version": "1.0.0"
}
✅ Step 2 — Install Express

Now install the missing dependency.

npm install express

This will create:

node_modules/
package-lock.json
package.json

Now your directory should look like:

patient-service
   src
      index.js
      package.json
      package-lock.json
      node_modules/
✅ Step 3 — Run the Service

Now start the application again:

node index.js

Expected output:

Server running on port 3000
🧪 Step 4 — Test the API

Open browser or run:

curl http://localhost:3000

You should get a response from the service.

🧠 What Just Happened (Important DevOps Concept)

Node applications require dependency management.

Application Code
        +
Dependencies (express, body-parser, etc.)
        ↓
npm installs them
        ↓
Application runs successfully

This is exactly what CI pipelines will also do later.

Example CI step:

- script: npm install
🚀 Your Current Progress in the Hackathon

You are here:

Step 1  Clone repo
Step 2  Run services locally   ✅ (you are fixing this)
Step 3  Containerization (Docker)
Step 4  Push images to ACR
Step 5  Terraform infra
Step 6  Deploy to AKS
Step 7  Monitoring
⭐ Pro DevOps Tip

Once everything works, add express to package.json properly.

Check it using:

cat package.json

You should see:

"dependencies": {
  "express": "^4.18.2"
}

This ensures the CI pipeline installs dependencies automatically.

![alt text](image-1.png)

Node.js Microservice Setup and Execution Guide
Project: patient-service

This document explains the step-by-step process followed to install dependencies, run the Node.js microservice locally, and troubleshoot issues encountered during the setup.

1. Verify Node.js Installation

Node.js applications require Node Runtime and npm package manager.

Command
node -v
Expected Output
v20.x.x

Verify npm:

npm -v

Expected output:

10.x.x
2. Troubleshooting Node Installation
Issue
bash: node: command not found
bash: npm: command not found
Cause

Node.js is not installed or not added to system PATH.

Solution

Download Node.js from:

https://nodejs.org

Install Node.js LTS version

During installation ensure:

✔ Add to PATH

Restart terminal.

Verify installation:

node -v
npm -v
3. Navigate to Node.js Project

Navigate to the Node.js microservice directory.

cd ~/hackathon-usecase/patient-service/src

Verify files:

ls

Expected output:

index.js
4. Initialize Node Project

If the project does not contain package.json, initialize the project.

Command
npm init -y

This creates:

package.json

Example structure:

{
  "name": "patient-service",
  "version": "1.0.0",
  "main": "index.js"
}
5. Install Application Dependencies

The application requires Express.js framework.

Install dependency:

npm install express

This creates the following structure:

patient-service
 ├ src
 │   ├ index.js
 │   ├ node_modules/
 │   ├ package.json
 │   └ package-lock.json
6. Troubleshooting Dependency Issues
Issue
Error: Cannot find module 'express'
Cause

The Express dependency was not installed.

Solution

Run:

npm install express

Then start the application again.

7. Run the Node.js Application

Start the application:

node index.js

Expected terminal output:

Server running on port 3000
8. Verify Application Endpoint

Open browser:

http://localhost:3000

Possible output:

Cannot GET /

This is normal if the root endpoint is not defined.

9. Access Application API Endpoint

Open:

http://localhost:3000/patients

Example response:

{
 "message":"Patients retrieved successfully",
 "count":2,
 "patients":[
   {"id":"1","name":"John Doe","age":30,"condition":"Healthy"},
   {"id":"2","name":"Jane Smith","age":45,"condition":"Hypertension"}
 ]
}

This confirms the API endpoint is functioning correctly.

10. Troubleshooting Browser Access Issues
Issue

Browser shows:

ERR_ADDRESS_INVALID
Cause

Using incorrect URL:

http://0.0.0.0:3000
Solution

Use:

http://localhost:3000

or

http://127.0.0.1:3000
11. Architecture Flow

Request flow inside the Node.js microservice:

Client Request
      ↓
Express Router
      ↓
Controller Logic
      ↓
Business Logic
      ↓
Response Returned

Example code structure:

Component	File
Server Initialization	index.js
Framework	Express.js
API Endpoint	/patients
12. DevOps Pipeline Context

The local execution process simulates what happens during the CI pipeline build stage.

Future pipeline flow:

Source Code Repository
        ↓
Azure DevOps CI Pipeline
        ↓
Checkout Code
        ↓
npm install
        ↓
Application Build
        ↓
Docker Image Build
        ↓
Push Image → Azure Container Registry
        ↓
Deploy → Azure Kubernetes Service
13. Validation Checklist
Validation Step	Status
Node Installed	✅
npm Installed	✅
Dependencies Installed	✅
Application Started	✅
API Endpoint Accessible	✅
--------------

Spring Boot Microservice Setup and Execution Guide
Project: order-service

This document explains the step-by-step process followed to build and run the Spring Boot microservice locally, including troubleshooting steps encountered during the setup.

1. Verify Java Installation

Spring Boot applications require Java Runtime Environment (JDK).

Command
java -version
Expected Output
openjdk version "25.0.2"
OpenJDK Runtime Environment
OpenJDK 64-Bit Server VM
Troubleshooting

Issue

bash: java: command not found

Cause

Java is not installed or not added to system PATH.

Solution

Download Java from
https://adoptium.net

Install JDK 17 or higher

Ensure installation adds Java to PATH.

Verify again:

java -version
2. Install Apache Maven

Spring Boot projects use Maven as the build tool.

Download Maven

https://maven.apache.org/download.cgi

Download:

apache-maven-3.9.x-bin.zip

Extract Maven to a permanent directory:

C:\tools\apache-maven-3.9.13

Directory structure:

apache-maven-3.9.13
 ├ bin
 ├ boot
 ├ conf
 └ lib
3. Verify Maven Installation

Navigate to Maven binary directory.

cd /c/tools/apache-maven-3.9.13/bin

Run:

./mvn.cmd -v
Expected Output
Apache Maven 3.9.13
Java version: 25
OS: Windows 11
4. Troubleshooting Maven Issues
Issue
bash: mvn: command not found
Cause

Git Bash cannot detect Windows .cmd executables automatically.

Temporary Solution

Use the Maven executable directly:

./mvn.cmd clean package
Permanent Solution

Add Maven bin directory to system PATH.

Example:

C:\tools\apache-maven-3.9.13\bin

Restart terminal and verify:

mvn -v
5. Navigate to Spring Boot Project

Go to the microservice directory.

cd ~/hackathon-usecase/order-service

Project structure:

order-service
 ├ src
 │  └ main
 │     └ java
 │        └ controller
 │        └ model
 │        └ repository
 │        └ service
 │        └ OrderServiceApplication.java
 ├ resources
 │  └ application.properties
 └ pom.xml

The file pom.xml defines all dependencies and build configuration.

6. Build the Spring Boot Application

Run the Maven build lifecycle.

/c/tools/apache-maven-3.9.13/bin/mvn.cmd clean package
Maven Lifecycle
clean  → removes previous build artifacts
compile → compiles Java source code
test → runs unit tests
package → creates executable JAR
Expected Output
BUILD SUCCESS
7. Verify Build Artifacts

After the build completes, check the target directory.

ls target

Expected files:

target/
 ├ classes/
 ├ generated-sources/
 ├ generated-test-sources/
 ├ maven-status/
 ├ surefire-reports/
 ├ test-classes/
 ├ order-service-0.0.1-SNAPSHOT.jar
 └ order-service-0.0.1-SNAPSHOT.jar.original

The important artifact is:

order-service-0.0.1-SNAPSHOT.jar

This JAR file contains the compiled Spring Boot application.

8. Run the Spring Boot Application

Start the application using Java.

java -jar target/order-service-0.0.1-SNAPSHOT.jar
Expected Logs
Tomcat started on port(s): 8080
Started OrderServiceApplication

This indicates the Spring Boot application is running successfully.

9. Verify Application Endpoint

Open the application endpoint in a browser.

http://localhost:8080/orders

Example API response:

[]

This indicates the API endpoint is reachable but currently contains no data.

10. Architecture Flow

The request flow for the Spring Boot microservice:

Client Request
     ↓
Spring Boot Controller
     ↓
Service Layer
     ↓
Repository Layer
     ↓
Database / Mock Data
     ↓
Response Returned

Code structure mapping:

Layer	File
Controller	OrderController.java
Service	OrderService.java
Repository	OrderRepository.java
Model	Order.java
11. DevOps Pipeline Context

This local build process simulates the CI pipeline build stage.

Future pipeline execution:

Source Code Repository
        ↓
Azure DevOps CI Pipeline
        ↓
Checkout Code
        ↓
mvn clean package
        ↓
Build JAR Artifact
        ↓
Docker Build
        ↓
Push Image → Azure Container Registry
        ↓
Deploy → Azure Kubernetes Service
12. Validation Checklist
Validation Step	Status
Java Installed	✅
Maven Installed	✅
Project Build Successful	✅
Executable JAR Generated	✅
Application Started	✅
API Endpoint Accessible	✅




🚀 PHASE 1 — Prepare the Application Code  -  completed

🐳 PHASE 2 — Containerization


