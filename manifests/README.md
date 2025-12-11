The CI/CD pipeline only rebuilds containers and restarts deployments.



For initial setup, manually apply:

oc apply -f postgresql.yaml

oc apply -f redis.yaml

oc apply -f backend.yaml

oc apply -f frontend.yaml

oc apply -f route.yaml

