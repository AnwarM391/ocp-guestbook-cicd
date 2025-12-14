# Guestbook CI/CD Pipeline

[](https://github.com/ysalha2003/ocp-guestbook-cicd#guestbook-cicd-pipeline)

**Project:**  Guestbook with Automated Deployment  
**Course:**  Containerteknologi DevOps 24  
**Team:**  Anwar Mousa , Vineeta Singh and Yaser Salha  
**Date:**  December 14, 2025

## Overview

[](https://github.com/ysalha2003/ocp-guestbook-cicd#overview)

This project implements a complete CI/CD pipeline for the Guestbook application using GitHub Actions, deploying automatically to Red Hat OpenShift Sandbox.

## Architecture

[](https://github.com/ysalha2003/ocp-guestbook-cicd#architecture)

```
GitHub Push → GitHub Actions → Build Containers → Push to GHCR → Deploy to OpenShift

```

### Components

[](https://github.com/ysalha2003/ocp-guestbook-cicd#components)

-   **Backend:**  Go REST API (2 replicas)
-   **Frontend:**  Nginx web server (2 replicas)
-   **PostgreSQL:**  Database with persistent storage
-   **Redis:**  Cache layer

## CI/CD Pipeline

[](https://github.com/ysalha2003/ocp-guestbook-cicd#cicd-pipeline)

### Trigger

[](https://github.com/ysalha2003/ocp-guestbook-cicd#trigger)

Pipeline runs automatically on:

-   Push to  `main`  branch
-   Pull requests to  `main`  branch

### Pipeline Steps

[](https://github.com/ysalha2003/ocp-guestbook-cicd#pipeline-steps)

1.  **Build Backend Container**
    
    -   Multi-stage Docker build
    -   Go 1.24 Alpine base
    -   Final image: ~15MB
2.  **Build Frontend Container**
    
    -   Nginx Alpine base
    -   Static assets + configuration
3.  **Push to GitHub Container Registry**
    
    -   Backend:  `ghcr.io/ysalha2003/ocp-guestbook-cicd/backend:latest`
    -   Frontend:  `ghcr.io/ysalha2003/ocp-guestbook-cicd/frontend:latest`
4.  **Deploy to OpenShift**
    
    -   Updates deployment images
    -   Triggers rolling update
    -   Verifies deployment success

## Application URL

[](https://github.com/ysalha2003/ocp-guestbook-cicd#application-url)

**Production:**  [https://frontend-ysalha-dev.apps.rm3.7wse.p1.openshiftapps.com](https://frontend-ysalha-dev.apps.rm3.7wse.p1.openshiftapps.com/)

### Clone Repository

[](https://github.com/ysalha2003/ocp-guestbook-cicd#clone-repository)

git clone https://github.com/ysalha2003/ocp-guestbook-cicd.git
cd ocp-guestbook-cicd


### Deploy to OpenShift

[](https://github.com/ysalha2003/ocp-guestbook-cicd#deploy-to-openshift)

# Login to OpenShift
oc login --token --server

# Apply manifests
oc apply -f manifests/

## Deployment Structure

[](https://github.com/ysalha2003/ocp-guestbook-cicd#deployment-structure)

```
manifests/
├── postgresql.yaml    # Database + PVC + Service
├── redis.yaml         # Cache + Service
├── backend.yaml       # Backend Deployment + Service
├── frontend.yaml      # Frontend Deployment + Service
└── route.yaml         # HTTPS Route

```

## Secrets Management

[](https://github.com/ysalha2003/ocp-guestbook-cicd#secrets-management)

Required secrets in GitHub:

-   `OPENSHIFT_SERVER`: OpenShift API endpoint
-   `OPENSHIFT_TOKEN`: Service account token
-   `OPENSHIFT_NAMESPACE`: Project namespace

Required secrets in OpenShift:

-   `postgresql-secret`: Database credentials
-   `redis-secret`: Cache password


### Access application

[](https://github.com/ysalha2003/ocp-guestbook-cicd#access-application)

oc get route frontend

## Bonus Features

[](https://github.com/ysalha2003/ocp-guestbook-cicd#bonus-features)

### Smart Deployment

[](https://github.com/ysalha2003/ocp-guestbook-cicd#smart-deployment)

-   Only modified components are redeployed :- Time is saved as only 	 one modified components are repdeployed instead of deploying all the components (backend, frontend and databse etc.)
-   ConfigMap changes trigger pod restart automatically :- By this all the modifications will be deployed without our interference. And it shows us that the restart pods has all the notification that we have done is correct and in place.

### Change Detection

[](https://github.com/ysalha2003/ocp-guestbook-cicd#change-detection)

# If only backend changed:
git diff --name-only HEAD~1 | grep backend/
# Result: Only backend rebuilds and deploys

# If ConfigMap changed:
oc apply -f manifests/configmap.yaml
oc rollout restart deployment/backend  # Restart to pick up new config


# Team Contributions

[](https://github.com/ysalha2003/ocp-guestbook-cicd#team-contributions)

**Anwar Mousa:**

-   GHCR package configuration
-   Migration from the old project
-   Resource limits and health checks

**Vineeta:**

-   Frontend application Migration
-   Participated in Kubernetes manifest creation
-   Participated in creating Readme.md file

**Yaser Salha:**

-   CI/CD pipeline implementation and testing
-   OpenShift deployment configuration
-   Testing and verification

## License

[](https://github.com/ysalha2003/ocp-guestbook-cicd#license)

Educational project 

## References

[](https://github.com/ysalha2003/ocp-guestbook-cicd#references)

-   Repository:  [https://github.com/jonasbjork/ocp-guestbook](https://github.com/jonasbjork/ocp-guestbook)



