# Guestbook CI/CD Pipeline

**Project:** Guestbook with Automated Deployment  
**Team:** Anwar Mousa, Vineeta Singh, and Yaser Salha  
**Date:** December 14, 2025

---

## Overview

This project shows how we built a complete CI/CD pipeline for a Guestbook application using GitHub Actions and Red Hat OpenShift. Our goal was simple: automate everything from code commit to production deployment without any manual steps.

Working together on this project was really fun! It felt like working on a real team project where we had to talk through problems, help each other out, and figure out solutions together. We started by discussing what we wanted to achieve, and that helped us all get on the same page. The coolest part was watching our code changes automatically trigger builds in GitHub Actions and then seeing those changes go live in the application within minutes!

## Live Application

**Check it out here:** https://frontend-ysalha-dev.apps.rm3.7wse.p1.openshiftapps.com

## How It Works
```
GitHub Push → GitHub Actions → Build Containers → Push to GHCR → Deploy to OpenShift
```

### What's Inside

- **Backend:** Go REST API (2 replicas)
- **Frontend:** Nginx web server (2 replicas)
- **PostgreSQL:** Database with persistent storage
- **Redis:** Cache layer

---

## CI/CD Pipeline - Our Main Focus

Since this is a CI/CD course, we focused on building a good automated pipeline rather than the application itself. Here's what we built:

### 1. Automatic Triggers

The pipeline runs automatically when:
- Someone pushes code to the `main` branch
- A pull request is created for the `main` branch

This means every code change automatically starts the whole deployment process.

### 2. Smart Change Detection

One of our favorite features! The pipeline checks what files changed and only builds what's needed:
```yaml
- name: Detect changed components
  run: |
    if git diff --name-only HEAD~1 HEAD | grep -q '^backend/'; then
      echo "backend_changed=true"
    fi
    
    if git diff --name-only HEAD~1 HEAD | grep -q '^frontend/'; then
      echo "frontend_changed=true"
    fi
```

**Why this is useful:**
- Change only `frontend/index.html`? Only the frontend builds and deploys
- Change only `backend/main.go`? Only the backend builds and deploys
- This saves about 50% of our build time!

### 3. Building Containers

**Backend:**
- Uses multi-stage Docker build with Go 1.23-alpine
- Final image is super small: ~15MB
- Only builds when backend code changes

**Frontend:**
- Uses Nginx-alpine with static files
- Adds build timestamp during Docker build
- Only builds when frontend code changes

### 4. Storing Images in GHCR

We publish all images as private packages in GitHub Container Registry:
- Backend: `ghcr.io/anwarm391/ocp-guestbook-cicd/backend:latest`
- Frontend: `ghcr.io/anwarm391/ocp-guestbook-cicd/frontend:latest`

We use `imagePullSecrets` in OpenShift so it can pull these private images.

### 5. Deploying to OpenShift

The deployment happens in three simple steps:
```bash
oc set image deployment/frontend frontend=ghcr.io/.../frontend:latest
oc rollout restart deployment/frontend
oc rollout status deployment/frontend
```

This makes sure:
- New images get pulled (even with `:latest` tag)
- Pods restart with the new version
- We know the deployment worked before moving on

### 6. Handling ConfigMap Changes

We added special handling for configuration changes:
```yaml
if git diff --name-only HEAD~1 HEAD | grep -q 'configmap.yaml'; then
  oc rollout restart deployment/backend
  oc rollout restart deployment/frontend
fi
```

**Why?** When configuration changes, pods need to restart to load the new settings. This happens automatically now!

---

## Problems We Ran Into (And Fixed!)

Working on this taught us a lot about real CI/CD challenges. Here are the main problems we hit and how we solved them:

### Problem 1: InvalidImageName

**What happened:** Pods got stuck with `InvalidImageName` status  

**Why:** Our GitHub repo name `AnwarM391/ocp-guestbook-cicd` has uppercase letters, but container registries need everything lowercase

**Our fix:** We added a step to convert the name to lowercase:
```yaml
- name: Set lowercase repository name
  run: |
    echo "repository_lowercase=$(echo '${{ github.repository }}' | tr '[:upper:]' '[:lower:]')"
```

### Problem 2: ImagePullBackOff - Manifest Unknown

**What happened:** Got `manifest unknown` error when trying to pull images

**Why:** After fixing the lowercase thing, the backend image didn't exist yet with the new lowercase name

**Our fix:** We triggered a backend rebuild by making a small change to `backend/main.go`, which created the image with the right lowercase name


### Problem 3: Missing Namespace

**What happened:** Got error `flag needs an argument: 'n' in -n`

**Why:** GitHub secret for the namespace was empty or wrong

**Our fix:** We just hardcoded the namespace `ysalha-dev` directly in the workflow instead of using the secret

---

## What We Learned

1. **Case sensitivity matters:** GHCR needs everything lowercase - we learned this the hard way!

2. **Smart builds save time:** Detecting what changed cut our build times in half

3. **Always pull latest:** Using `imagePullPolicy: Always` is needed with `:latest` tag

4. **Restart after update:** Need to run `oc rollout restart` after `oc set image` with `:latest` tag

5. **Keep it simple:** Sometimes hardcoding values works better than using secrets, especially for things that don't change

---

## Project Structure
```
manifests/
├── postgresql.yaml    # Database + storage + service
├── redis.yaml         # Cache + service
├── backend.yaml       # Backend deployment + service
├── frontend.yaml      # Frontend deployment + service
├── configmap.yaml     # Backend configuration
└── route.yaml         # HTTPS route
```

---

## Secrets We Need

**In GitHub:**
- `OPENSHIFT_SERVER`: Where OpenShift is
- `OPENSHIFT_TOKEN`: Token to access OpenShift
- `GITHUB_TOKEN`: GitHub gives us this automatically

**In OpenShift:**
- `ghcr-secret`: Login info for pulling our private images
- `postgresql-secret`: Database login info
- `redis-secret`: Redis password

---

## Testing the Pipeline

Want to see it work? Here's how:

1. Change any file in `frontend/` or `backend/` (we used both the browser and GitHub Desktop app - both work great!)
2. Commit and push to `main` branch
3. Go to the "Actions" tab in GitHub
4. Watch the magic happen!
5. Visit the application URL to see your changes live

---

## Group Work

We all worked together on this, but here's what each of us focused on:

### Anwar Mousa
- Set up the GitHub repository and project structure
- Created the initial backend files (`Containerfile`, `go.mod`, `main.go`)
- Configured GitHub Container Registry for private packages
- Set up the Personal Access Token for authentication
- Fixed various issues in the deploy file and namespace errors
- Debugged backend build problems and Go version issues
- Tested UI updates and backend functionality

### Vineeta Singh
- Created the initial frontend files (`Containerfile`, `nginx.conf`, `index.html`)
- Built the first version of `deploy.yml` for GitHub Actions
- Fixed namespace issues by removing `-n` flags from the workflow
- Updated `index.html` several times to improve the UI
- Tested the workflow and UI changes
- Helped write documentation and README

### Yaser Salha
- Created all Kubernetes manifests (backend, frontend, postgresql, redis, route, configmap)
- Redesigned the entire frontend interface with a modern, clean look
- Added the deployment timestamp feature so people can see when each version was deployed
- Fixed the lowercase repository name problem
- Tested the whole CI/CD pipeline end-to-end
- Solved major issues like InvalidImageName, ImagePullBackOff, and ConfigMap setup

---

## Why This Project Was Special

This felt different from typical projects. We worked like a real team - talking through problems, helping each other debug issues, and celebrating when things finally worked. The CI/CD aspect was especially cool because we could see our automation working in real-time. Push code, watch GitHub Actions build it, and boom - it's live in OpenShift within seconds/minutes!

Focusing on CI/CD instead of just building an application taught us how modern software teams actually work. We learned that good automation isn't just about writing scripts - it's about understanding the whole deployment process, handling weird edge cases, and making the pipeline smart enough to save everyone time.

---

## Quick Start Guide
```bash
# Get the code
git clone https://github.com/AnwarM391/ocp-guestbook-cicd.git
cd ocp-guestbook-cicd

# Login to OpenShift
oc login --token=<your-token> --server=<your-server>

# Create a new project
oc new-project <your-namespace>

# Create the secret for pulling images
oc create secret docker-registry ghcr-secret \
  --docker-server=ghcr.io \
  --docker-username=<your-username> \
  --docker-password=<your-token>

# Deploy everything
oc apply -f manifests/

# Check if it's working
oc get pods
oc get route frontend
```

---

## References

- **Original project we started from:** https://github.com/jonasbjork/ocp-guestbook
- **Our repository:** https://github.com/AnwarM391/ocp-guestbook-cicd

---

**Course:** Containerteknologi DevOps 24  
**Date:** December 14, 2025

---
