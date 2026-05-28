# my-first-pipeline

A full CI/CD pipeline built from scratch using GitHub Actions, Docker, and AWS EC2. Code pushed to the `staging` branch deploys automatically to the staging server. Code merged into `main` deploys automatically to production.

---

## Architecture
Developer pushes code
│
▼
GitHub Actions Pipeline
├── Build Docker image
├── Push image to Docker Hub
└── SSH into target server and run container
│
├── staging branch ──► Staging Server (EC2)
└── main branch    ──► Production Server (EC2)
## Tech Stack

| Tool | Purpose |
|---|---|
| GitHub Actions | CI/CD pipeline automation |
| Docker | Containerize the app |
| Docker Hub | Store and distribute Docker images |
| AWS EC2 | Staging and production servers |
| AWS VPC | Custom network with public subnets |
| nginx | Serve the HTML app inside the container |

---

## Project Structure
my-first-pipeline/
├── .github/
│   └── workflows/
│       └── pipeline.yml
├── app/
│   └── index.html
├── Dockerfile
├── .dockerignore
└── README.md
## Pipeline Flow

On push to staging branch:
1. Checkout code
2. Build Docker image tagged as staging
3. Push image to Docker Hub
4. SSH into staging EC2 and deploy container

On push to main branch:
1. Checkout code
2. Build Docker image tagged as production
3. Push image to Docker Hub
4. SSH into production EC2 and deploy container

---

## GitHub Secrets Required

| Secret | Description |
|---|---|
| `DOCKER_USERNAME` | Docker Hub username |
| `DOCKER_PASSWORD` | Docker Hub password or access token |
| `STAGING_SERVER_IP` | Public IP of staging EC2 instance |
| `STAGING_SSH_KEY` | Private SSH key for staging server |
| `PROD_SERVER_IP` | Public IP of production EC2 instance |
| `PROD_SSH_KEY` | Private SSH key for production server |

---

## Server Setup

Both EC2 instances run Amazon Linux 2023 with Docker installed:

```bash
sudo yum update -y && \
sudo yum install -y docker && \
sudo systemctl start docker && \
sudo systemctl enable docker && \
sudo usermod -aG docker ec2-user
```

Security group inbound rules required:
SSH    TCP    22    0.0.0.0/0
HTTP   TCP    80    0.0.0.0/0
---

## Deployment Workflow

```bash
# Make changes and deploy to staging
git checkout staging
git add .
git commit -m "your change"
git push origin staging

# Promote to production
git checkout main
git merge staging
git push origin main
```

---

## AWS Infrastructure

- **VPC:** Custom VPC with CIDR 10.0.0.0/16
- **Subnets:** Two public subnets across two availability zones
- **Internet Gateway:** Attached to VPC for public internet access
- **Route Table:** 0.0.0.0/0 routed to internet gateway
