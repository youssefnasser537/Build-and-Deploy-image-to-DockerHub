# Build and Deploy Image to Docker Hub

This project contains a GitHub Actions workflow that automates the process of building a Docker image (specifically an Nginx image in this case), pushing it to Docker Hub, and then deploying/running it as a container.

## 🚀 Workflow Overview

This workflow is designed to:

1.  **Build** a Docker image from your repository.

2.  **Authenticate** with Docker Hub.

3.  **Push** the built image to your Docker Hub repository.

4.  **Deploy/Run** the pushed image as a Docker container.

## ⚙️ Workflow Details

* **Name**: `Build and Deploy image to dockerhub`

* **Trigger**: `workflow_dispatch` - This workflow can be manually triggered from the "Actions" tab in your GitHub repository.

### Environment Variables

The workflow uses the following environment variables:

* `IMAGE_NAME`: `nginx-youssef` - The name that will be given to your Docker image.

* `IMAGE_TAG`: `v1` - The tag for your Docker image version.

### Jobs

The workflow consists of two sequential jobs:

#### 1. `build-and-push`

This job is responsible for building the Docker image and pushing it to Docker Hub.

* **`runs-on: ubuntu-latest`**: Executes on the latest Ubuntu runner provided by GitHub Actions.

* **Steps**:

    * **Checkout Code**: Checks out your repository's code, making it available to the workflow.

    * **Docker Build**: Builds the Docker image. It uses `vars.REG_URL`, `vars.DOCKER_USERNAME`, `env.IMAGE_NAME`, and `env.IMAGE_TAG` to construct the full image tag.

    * **Docker Login**: Logs into Docker Hub using the provided `secrets.DOCKER_PASSWORD` and `vars.DOCKER_USERNAME`.

    * **Docker Push**: Pushes the newly built Docker image to your Docker Hub repository.

#### 2. `deploy`

This job runs after `build-and-push` successfully completes and is responsible for running the container.

* **`needs: build-and-push`**: Ensures this job only runs after the `build-and-push` job has finished successfully.

* **`runs-on: ubuntu-latest`**: Executes on the latest Ubuntu runner.

* **Steps**:

    * **Docker Login**: Logs into Docker Hub again to pull the image for deployment.

    * **Run Container**: Starts a Docker container from the pushed image.

        * `-d`: Runs the container in detached mode (in the background).

        * `-p 8080:80`: Maps port 8080 on the host machine to port 80 inside the container (where Nginx typically runs).

        * The image used is the one built and pushed in the previous job.

## 🔒 Required GitHub Secrets and Variables

For this workflow to function correctly, you **must** configure the following in your GitHub repository's settings (`Settings > Secrets and variables > Actions`):

### Repository Secrets

* `DOCKER_PASSWORD`: Your Docker Hub personal access token or password.

### Repository Variables

* `REG_URL`: The Docker registry URL (e.g., `docker.io`).

* `DOCKER_USERNAME`: Your Docker Hub username.

## ▶️ How to Run

1.  **Configure Secrets and Variables**: Ensure `DOCKER_PASSWORD`, `REG_URL`, and `DOCKER_USERNAME` are set in your repository's GitHub Actions secrets and variables.

2.  **Trigger Manually**: Navigate to the "Actions" tab in your GitHub repository, select the "Build and Deploy image to dockerhub" workflow, and click "Run workflow".

## 📦 Project Structure

```
.
├── application/                    # Your application source code
├── Dockerfile                      # Dockerfile for building the image
├── .git/                           # Git repository data
├── .github/                        # GitHub Actions workflow configurations
│   └── workflows/
│       └── main.yml
└── README.md                       # This README file
