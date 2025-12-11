# \# OpenShift Guestbook with CI/CD

# 

# Cloud-native guestbook application with automated deployment to OpenShift.

# 

# \## Architecture

# 

# \- \*\*Frontend\*\*: Nginx (2 replicas)

# \- \*\*Backend\*\*: Go REST API (2 replicas)

# \- \*\*Database\*\*: PostgreSQL (persistent storage)

# \- \*\*Cache\*\*: Redis

# 

# \## CI/CD Pipeline

# 

# GitHub Actions automatically builds and deploys to OpenShift on push to `main`.

# 

# 

# \## Deployment

# 

# Automated via GitHub Actions. Manual deployment:

# 

# oc apply -f manifests/

# 

## Author

# Anwar Mousa, Vineeta and Yaser Salha - DevOps 24
#

