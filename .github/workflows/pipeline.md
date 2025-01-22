# GitHub Actions CI/CD Pipeline for Docker Deployment

This GitHub Actions pipeline automates the process of cloning a repository, building Docker images, and deploying an application to a remote server using Docker Compose.

## Table of Contents

1. [Workflow Trigger](#workflow-trigger)
2. [Workflow Overview](#workflow-overview)
3. [Steps in the Build Job](#steps-in-the-build-job)
4. [Steps in the Deploy Job](#steps-in-the-deploy-job)
5. [Secrets Required for Deployment](#secrets-required-for-deployment)

## Workflow Trigger

The workflow is triggered under the following conditions:

1. `Manual Trigger (workflow_dispatch)`: The workflow can be manually triggered from the GitHub Actions dashboard.

2. `Push to the main Branch`: When a commit is pushed to the main branch, the workflow is automatically triggered.

3. `Workflow Call`: The workflow can also be called from other workflows, requiring specific secrets:

   - `SSH_PRIVATE_KEY`: The private SSH key for accessing the remote server.
   - `REMOTE_HOST`: The IP address or hostname of the remote server.
   - `REMOTE_USER`: The username for the remote server.
   - `TARGET`: The target directory on the remote server where files will be uploaded.

## Workflow Overview

The workflow consists of two main jobs:

1. **Build Job**: This job builds and pushes Docker images for the frontend and backend of the application. The images are pushed to a Docker registry (GitHub Container Registry in this case).

2. **Deploy Job**: This job handles the deployment of the application to the remote server. The necessary artifacts (.env, docker-compose.yaml) are transferred, and the application is started on the remote server using Docker Compose.

## Steps in the Build Job:

1. **Checkout Repository** (`actions/checkout`):

   - The first step of the job is to clone the repository. This ensures that the latest version of the code is available.

2. **Setup Docker Buildx** (`docker/setup-buildx-action`):

   - This action sets up Docker Buildx, which is necessary for advanced build features like multi-architecture builds.

3. **Docker Login**:
   - The `docker/login-action` is used to authenticate with the GitHub Container Registry (GHCR) in order to push Docker images.
   - The GitHub Personal Access Token (`GHCR_PAT`) is used for authentication.
4. **Extract Docker Metadata**:

   - The `docker/metadata-action` is used to extract metadata from the Dockerfiles for both frontend and backend images. This step generates labels for the Docker images.

5. **Create .env File**:

   - The `.env` file is copied from example.env, and the GitHub commit SHA (GITHUB_SHA) is added to it. This allows for tracking which commit the build corresponds to.

6. **Build and Push Docker Images for Frontend and Backend**:

   - The frontend and backend images are built and pushed using the `docker/build-push-action`.
   - The images are tagged with the GitHub commit SHA (`${{ github.sha }}`) and pushed to the GitHub Container Registry (`ghcr.io`).

7. **Upload Artifacts**:
   - The `.env` and `docker-compose.yaml` files are uploaded as artifacts. These files will be used in the deployment step to configure the application on the remote server.

## Steps in the Deploy Job:

1. **Checkout Repository** `(actions/checkout)`:

   - The repository is checked out again in the deploy job to ensure that the latest version of the code and the necessary files are available.

2. **Download Deployment Artifacts** (`actions/download-artifact`):

   - The artifacts uploaded in the build job (`.env` and `docker-compose.yaml`) are downloaded to the deploy job. These files are needed to configure the application on the remote server.

3. **Setup SSH Key**:

   - The private SSH key (`SSH_PRIVATE_KEY`) is set up for authenticating with the remote server. This allows secure SSH access to the remote host.

4. **SSH into the Remote Server and Prepare Directory**:

   - An SSH connection is established to the remote server to ensure that the target directory exists and has the correct permissions. The directory is created if it doesn't exist, and the ownership is set correctly.

5. **Use rsync to Transfer Files**:

   - The `.env` and `docker-compose.yaml` files are transferred to the target directory on the remote server using rsync. The `--rsync-path="sudo rsync"` option ensures that the files are transferred with elevated privileges if needed.

6. **Deploy the Application with Docker Compose**:
   - After the files are transferred, an SSH command is executed to:
     - Log into the Docker registry using the GitHub token (`GHCR_PAT`).
     - Navigate to the target directory.
     - Run the following Docker Compose commands:
       `docker compose down --remove-orphans:` Stops and removes any orphaned containers.
       `docker system prune -af:` Cleans up unused Docker images, containers, and volumes.
       `docker compose pull:` Pulls the latest Docker images from the registry.
       `docker compose up -d:` Starts the containers in detached mode.

## Secrets Required for Deployment:

- `SSH_PRIVATE_KEY`: Private SSH key used for authenticating with the remote server.
- `REMOTE_HOST`: The IP address or domain of the remote server.
- `REMOTE_USER`: The username used to access the remote server.
- `TARGET`: The target directory on the remote server where the application will be deployed.
- `GHCR_PAT`: A GitHub Personal Access Token for authenticating with the GitHub Container Registry.
