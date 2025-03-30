# UdaPeople CI/CD Pipeline Project

## Overview

This project demonstrates a comprehensive CI/CD pipeline for the UdaPeople application - a human resources platform designed for small businesses. The implementation showcases modern DevOps practices including Infrastructure as Code, automated testing, and blue-green deployments.

## Architecture

The application consists of:

- **Frontend**: A React single-page application
- **Backend**: Node.js API with PostgreSQL database
- **Infrastructure**: AWS (EC2, S3, CloudFront, RDS)
- **CI/CD**: CircleCI with integrated monitoring

## Project Structure

```
.
├── .circleci/           # CircleCI configuration and deployment files
│   ├── ansible/         # Ansible playbooks for configuration management
│   ├── files/           # CloudFormation templates
│   └── config.yml       # CircleCI pipeline definition
├── backend/             # Node.js backend application
├── frontend/            # React frontend application
└── util/                # Utility scripts and docker configurations
```

## CI/CD Pipeline Features

The pipeline implements a full workflow including:

1. **Build Phase**
   - Compiles frontend and backend code
   - Fails fast if code cannot be built

2. **Test Phase**
   - Runs unit tests for both frontend and backend
   - Prevents progression if tests fail

3. **Analysis Phase**
   - Performs security scans on dependencies
   - Alerts on critical vulnerabilities

4. **Infrastructure Phase**
   - Provisions/updates AWS resources using CloudFormation
   - Configures servers using Ansible

5. **Deployment Phase**
   - Executes database migrations
   - Deploys frontend to S3
   - Deploys backend to EC2 instances

6. **Verification Phase**
   - Performs smoke tests on deployed applications
   - Automatically rolls back if tests fail

7. **Promotion Phase**
   - Implements blue-green deployment using CloudFront
   - Handles traffic switching without downtime

8. **Cleanup Phase**
   - Removes previous infrastructure after successful deployment
   - Helps control AWS costs

9. **Monitoring**
   - Prometheus and Node Exporter for system metrics
   - Alerting for critical system conditions

## Setup Instructions

### Prerequisites

- AWS Account and configured AWS CLI
- CircleCI Account connected to your GitHub repository
- PostgreSQL database (can use the provided Docker setup)
- Node.js v13.8.0 (recommended)

### Local Development

1. **Clone the repository**
   ```bash
   git clone <repository-url>
   cd <repository-name>
   ```

2. **Setup Backend**
   ```bash
   cd backend
   npm install
   ```

   Create a `.env` file in the backend directory with:
   ```
   NODE_ENV=local
   VERSION=1
   TYPEORM_CONNECTION=postgres
   TYPEORM_MIGRATIONS_DIR=./src/migrations
   TYPEORM_ENTITIES=./src/modules/domain/**/*.entity.ts
   TYPEORM_MIGRATIONS=./src/migrations/*.ts
   TYPEORM_HOST=localhost
   TYPEORM_PORT=5532
   TYPEORM_USERNAME=postgres
   TYPEORM_PASSWORD=password
   TYPEORM_DATABASE=glee
   ```

3. **Setup Frontend**
   ```bash
   cd frontend
   npm install
   ```

4. **Run Local Database**
   ```bash
   cd util
   docker-compose up
   ```

5. **Run Migrations**
   ```bash
   cd backend
   npm run migrations
   ```

6. **Start Applications**
   
   Backend:
   ```bash
   cd backend
   npm run build
   npm run start
   ```
   
   Frontend:
   ```bash
   cd frontend
   npm run build
   npm run start
   ```

### CircleCI Configuration

1. Connect your GitHub repository to CircleCI

2. Add the following environment variables in the CircleCI project settings:
   - `AWS_ACCESS_KEY_ID` - Your AWS access key
   - `AWS_SECRET_ACCESS_KEY` - Your AWS secret key
   - `AWS_DEFAULT_REGION` - Your AWS region
   - `TYPEORM_CONNECTION` - postgres
   - `TYPEORM_MIGRATIONS_DIR` - ./src/migrations
   - `TYPEORM_ENTITIES` - ./src/modules/domain/**/*.entity.ts
   - `TYPEORM_MIGRATIONS` - ./src/migrations/*.ts
   - `TYPEORM_HOST` - Your RDS endpoint
   - `TYPEORM_PORT` - Your RDS port (typically 5432)
   - `TYPEORM_USERNAME` - Your RDS username
   - `TYPEORM_PASSWORD` - Your RDS password
   - `TYPEORM_DATABASE` - Your RDS database name

3. Add your SSH key to CircleCI for EC2 access

## Deployment

The application employs a blue-green deployment strategy:

1. New infrastructure is provisioned alongside the existing production environment
2. Code is deployed and tested on the new infrastructure
3. Traffic is switched from old to new infrastructure if all tests pass
4. Old infrastructure is removed to save costs

All deployments are triggered automatically on merges to the `master` branch through CircleCI.

## Monitoring

The project includes monitoring with Prometheus:

- System metrics from Node Exporter
- Alerts for critical conditions (disk space, memory, etc.)
- Dashboard for visualizing performance

## Troubleshooting

- **Failed Builds**: Check CircleCI logs for specific error messages
- **Failed Deployments**: Ensure AWS credentials are correctly configured
- **Database Connection Issues**: Verify RDS endpoint and credentials
- **Infrastructure Errors**: Check CloudFormation stack events for details

## Technology Stack

- **Frontend**: React, Redux
- **Backend**: Node.js, Express, TypeORM
- **Database**: PostgreSQL
- **Infrastructure**: AWS (EC2, S3, CloudFront, RDS)
- **CI/CD**: CircleCI
- **Configuration Management**: Ansible
- **Infrastructure as Code**: CloudFormation
- **Monitoring**: Prometheus, AlertManager
