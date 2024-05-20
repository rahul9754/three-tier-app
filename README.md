# Three-Tier Application Deployment Guide

This guide outlines the steps to deploy a three-tier application on Azure Kubernetes Service (AKS) using Azure Pipelines. The application consists of the following components:

## Architecture Diagram

![Architecture Diagram](architecture.png)

The architecture diagram illustrates the components and interactions of the three-tier application.

1. **Front-End Web App (Vote)**: A Python web application that allows users to vote between two options.

2. **Redis**: A Redis database used to collect new votes.

3. **Worker**: A .NET worker application that consumes votes and stores them.

4. **Database (Postgres)**: A Postgres database backed by a azure managed disk using a storage class.

5. **Result Web App**: A Node.js web application that displays the real-time voting results.

## Folder Structure

The project includes the following folders:

1. **k8s-specifications**: Contains Kubernetes manifests for deploying the application components.

2. **result**: Contains the source code and Dockerfile for the Result web application.

3. **vote**: Contains the source code and Dockerfile for the Vote web application.

4. **worker**: Contains the source code and Dockerfile for the Worker application.


## Prerequisites

Before deploying the application, ensure the following prerequisites are met:

- An AKS cluster is provisioned and configured. If you wish to deploy the Infra using terraform, refer [this](https://github.com/rahul9754/terraform-aks.git) github repository.

- `Service connections` for pipeline is set-up.
- `Azure Container Registry` (ACR) is provisioned to store Docker images.


## Deployment Steps

Follow these steps to deploy the three-tier application:

1. **Set Up AKS Cluster**: Ensure you have an AKS cluster provisioned and configured in your Azure subscription.


2. **Configure Service connections**: Set up `service connections` 
- for `Azure Container Registry` using connection type as `Docker Registry`.
- for `Kubernetes cluster` using connection type as `Kubernetes`.
- for `Storage class` and `PV`, create connection type as `Azure Resource Manager`.

3. **Configure Azure Container Registry (ACR)**: Set up an `Azure Container Registry` (ACR) to store your Docker images. Make sure to grant `AcrPull` permission to `managed identity` resource of your AKS cluster on the `Azure container registry`.

4. **Build Docker Images**: Build Docker images for each component of the application (Vote, Worker, Result) using the provided Dockerfiles. We will use Azure Pipeline to perform the CI.

5. **Push Docker Images to ACR**: Push the built Docker images to your Azure Container Registry. We will use Azure Pipeline to push the image to the registry.

6. **Configure Kubernetes Manifests**: Update the Kubernetes manifests in the `k8s-manifests` folder to reference the correct image URLs from your ACR. This will be achieved using `container` key in our `task` block.

7. **Configure Azure Pipelines YAML**: Update the Azure Pipelines YAML file (`azure-pipelines.yml`) to define the pipeline stages and jobs for building and deploying the application.

8. **Run the Pipeline**: Trigger the Azure Pipelines build pipeline to build and deploy the application to your AKS cluster.

9. **Monitor Deployment**: Monitor the pipeline execution and verify that all application components are deployed successfully.

10. **Access the Application**: Once deployed, access the application using the provided endpoints for the Vote and Result web apps. Incase of any issues do validate the nsg rules having inbound rules allowed for the respective `nodeport`. 
-   to access the vote, http://<public-ip-node>:31000
- to access the result, http://<public-ip-node>:31001