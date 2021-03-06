# Quick demonstration deployment using Minikube

This how-to suggests you have a working [Minikube](https://kubernetes.io/docs/setup/minikube/)
installation at hands. If not, follow the upstream instructions to get started.

First you will need to create a physical volume to store data on:
```
kubectl create -f k8s/utils/pv-hostPath.yaml
```

Now let's create some secrets that will be used for Postgres, DataCite DOI
registration and API unblocking:
```
kubectl create -f k8s/utils/demo-secrets.yaml
```
It deploys very simple non productive secrets, stored in cleartext at [demo-secrets.yaml](../k8s/utils/demo-secrets.yaml)

Let's deploy PostgreSQL and Solr now:
```
kubectl apply -f k8s/utils/postgresql
kubectl apply -f k8s/solr
```

Once PostgreSQL and Solr are ready, deploy Dataverse:
```
kubectl apply -f k8s/dataverse
```

Once the deployment finishes successfully, you will need to bootstrap the installation.
You can simply create the job, it will wait for Postgres, Solr and Dataverse to be ready.
```
kubectl apply -f k8s/dataverse/jobs/bootstrap.yaml
```

You can check the status of the containers and the bootstrapping job from
the output of `kubectl get pods,jobs` and `kubectl logs`.

If you want to use this basic deployment for development, testing or demo cases,
you can just execute the following to open Dataverse in your browser:
```
kubectl expose deployment dataverse --type=NodePort --name=dataverse-local
minikube service dataverse-local
```
