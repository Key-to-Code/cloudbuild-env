# GitOps-style Continuous Delivery For Kubernetes Engine With Cloud Build

This repository contains the code used in the **GitOps-style Continuous Delivery with Cloud Build** tutorial.

## Overview

GitOps is a Continuous Delivery approach **first described by Weaveworks** that is popular in the Kubernetes community. A key part of GitOps is the idea of "environments-as-code": describing your deployments declaratively by files (for example, Kubernetes manifests) stored in a Git repository.

In this tutorial, you create a CI/CD pipeline that automatically builds a container image from committed code, stores the image in Google Artifact Registry, updates a Kubernetes manifest in a Git repository and triggers a deployment to Kubernetes Engine using that manifest.

## Architecture

This tutorial uses two Git repositories:

- **app repository**: Contains the application source code
- **env repository**: Contains the Kubernetes deployment manifests

When a change is pushed to the application repository, tests are run, a container image is built and pushed to Artifact Registry. Once the image is pushed, the deployment manifests are updated to use that new image and they are pushed to the candidate branch of the env repository. This triggers the actual deployment in Kubernetes. Once the deployment is finished, the new manifests are copied over to the production branch of the env repository.

## How It Works

Cloud Build is a service that executes your builds on Google Cloud. It can import source code, execute builds, and more.

In this lab, you create a continuous integration and continuous deployment (CI/CD) pipeline that automatically builds a container image from committed code, stores the image in Artifact Registry, updates a Kubernetes manifest in a Git repository, and deploys the application to Google Kubernetes Engine using that manifest.

### Repository Structure

For this lab you create 2 Git repositories:

- **`app`**: Contains the application source code
- **`env`**: Contains the Kubernetes deployment manifests

### Deployment Flow

When you push a change to the `app` repository, the Cloud Build pipeline runs tests, builds a container image, and pushes the change to Artifact Registry. After pushing the image, Cloud Build updates the deployment manifest and pushes it to the `env` repository. This triggers another Cloud Build pipeline that applies the manifest to the GKE cluster and, if successful, stores the manifest in another branch of the `env` repository.

The `app` and `env` repositories are kept separate because they have different lifecycles and uses. The `app` repository is dedicated to a specific application, and is used mostly by actual humans. The `env` repository may be shared by several applications and is used by automated systems (such as Cloud Build). The `env` repository can have several branches, each mapping to a specific environment and reference a specific container image; the `app` repository does not.

## Benefits

In the end, you have a system where:

- The **candidate branch** is a history of the deployment attempts
- The **production branch** is a history of the successful deployments
- You have a view of successful and failed deployments in Cloud Build
- You can rollback to any previous deployment by re-executing the corresponding job in Cloud Build. A rollback also updates the production branch to truthfully reflect the history of deployments

When you finish this lab, your system can easily:

- Distinguish between failed and successful deployments by looking at the Cloud Build history
- Access the manifest currently used by looking at the production branch of the `env` repository
- Rollback to any previous version by re-executing the corresponding Cloud Build build

## Objectives

In this lab, you learn how to perform the following:

- Create Kubernetes Engine clusters
- Create GitHub repositories
- Trigger Cloud Build from GitHub repositories
- Automate tests and publish a deployable container image via Cloud Build
- Manage resources deployed in a Kubernetes Engine cluster via Cloud Build
