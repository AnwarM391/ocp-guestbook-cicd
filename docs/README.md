\# OpenShift Guestbook with CI/CD



Guestbook application with automated deployment to OpenShift.



\## Architecture



\- \*\*Frontend\*\*: Nginx (2 replicas)

\- \*\*Backend\*\*: Go REST API (2 replicas)

\- \*\*Database\*\*: PostgreSQL (persistent storage)

\- \*\*Cache\*\*: Redis



\## CI/CD Pipeline



GitHub Actions automatically builds and deploys to OpenShift on push to `main`.



\## Live Application



🔗 https://frontend-ysalha-dev.apps.rm3.7wse.p1.openshiftapps.com



\## Local Development



\# Build backend

cd backend

docker build -t guestbook-backend .



\# Build frontend

cd frontend

docker build -t guestbook-frontend .





\## Deployment



Automated via GitHub Actions. Manual deployment:

&nbsp;

oc apply -f manifests/





\## Author



Anwar Mousa and Yaser Salha - DevOps 24

