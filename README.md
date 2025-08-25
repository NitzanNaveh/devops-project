🚀 DevOps & CI/CD Pipeline
Beyond its features, this project is built on a fully automated DevOps pipeline that handles everything from infrastructure creation to application deployment on AWS. The entire workflow is defined as code, ensuring a robust, repeatable, and scalable deployment process for this full-stack application.
The pipeline is designed to build the React frontend, integrate it with the Node.js backend, containerize them together, and deploy the final product automatically.
Category	Technology	Purpose
Cloud Provider	AWS	Hosting the production environment (EC2, VPC).
Infrastructure as Code	Ansible	Provisioning the VPC, EC2 instance, and installing Docker.
Containerization	Docker & Docker Compose	Packaging the full-stack app into a single, portable container.
Container Registry	Docker Hub	Storing and distributing the application's Docker image.
CI/CD Automation	GitHub Actions	Orchestrating the entire build, test, and deployment workflow.
Automated Testing	Selenium with Python	Performing automated end-to-end testing on the live application.
Automated Workflow Architecture
The pipeline is event-driven, triggered by developer actions in GitHub, and ensures that only tested and validated code reaches production.
[Code Push] -> [GitHub Pull Request] -> [CI Pipeline: Build & Test] -> [Merge to Main] -> [CD Pipeline: Push & Deploy] -> [Live on AWS EC2]
1. Infrastructure Provisioning with Ansible
The entire AWS infrastructure is provisioned from scratch using an Ansible Playbook. This IaC approach guarantees a consistent and reproducible environment.
Network Setup: Creates a custom VPC, Public Subnet, Internet Gateway, and Route Tables.
Server Provisioning: Launches an EC2 instance (Ubuntu) and configures a Security Group to allow HTTP (port 80) and SSH (port 22) traffic.
Dependency Installation: Automatically installs Docker and Docker Compose on the server, preparing it to run the containerized application.
2. Full-Stack Containerization with Docker
A multi-stage Dockerfile is used to create an optimized and self-contained image for the entire full-stack application.
Build Stage: An initial stage uses a node image to build the React frontend. It runs npm install and npm run build to generate a directory of static files.
Final Stage: A second node image is used for the production environment. It sets up the Express.js backend and—critically—copies the static files from the build stage. The Express server is configured to serve both the API and the React frontend.
The docker-compose.yml file manages the container on the production server, handling port mapping (exposing port 80) and passing necessary environment variables like MONGODB_URI and LISTEN_NOTES_API_KEY to the application.
3. CI/CD Automation with GitHub Actions
The pipeline is orchestrated by two workflows in GitHub Actions:
A. CI Workflow (On Pull Request)
This workflow acts as a quality gate before code is merged into the main branch.
Build: Creates the full-stack Docker image using the multi-stage build process.
Test: Runs the container in the GitHub Actions environment and executes the Selenium test suite against it to perform end-to-end functional validation.
Validate: A pull request can only be merged if all tests pass, preventing regressions and bugs from entering the main codebase.
B. CD Workflow (On Merge to main)
This workflow is triggered automatically after a pull request is successfully merged.
Build & Push: Builds the final Docker image and pushes it to Docker Hub with a new tag.
Deploy:
Securely connects to the production EC2 instance via SSH.
Runs docker-compose pull to fetch the latest image from Docker Hub.
Runs docker-compose up -d --force-recreate to restart the service with the new, updated container, resulting in zero-downtime deployment.
