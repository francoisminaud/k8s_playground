Source for Terraform files: https://github.com/hashicorp/learn-terraform-provision-gke-cluster

TODO: Find a way to automate "Enable Compute Engine API" when creating a new project
TODO: Find a way to automate "Enable Kubernetes Engine API" when creating a new project

### Howto


1) Spin up the cluster

Make sure terraform is installed and version > 0.12

```
terraform version
```

```
terraform init
terraform plan -out=plan.out
terraform apply "plan.out"
```

2) Make sure your kubectl has got the right context

```
gcloud container clusters get-credentials $(terraform output kubernetes_cluster_name) --region $(terraform output region)
```

> Fetching cluster endpoint and auth data.
> kubeconfig entry generated for terraform-k8s-gke-playground-gke.

3) Deploy a webserver

```
kubectl apply -f deployment.yml
```

4) Make sure it's up and running

```
kubectl get deployment deployment-nginx
kubectl get po
```

5) Expose the service to the outside world

```
kubectl expose deployment nginx-deployment --type=LoadBalancer --name=my-nginx-service
```

6) Wait a bit until your service gets an external IP address

7) kubectl get svc my-nginx-service

```
NAME               TYPE           CLUSTER-IP      EXTERNAL-IP     PORT(S)        AGE
my-nginx-service   LoadBalancer   10.147.244.21   35.189.80.117   80:30954/TCP   16m
```

8) Try connecting to the webserver



### Prometheus + Grafana

## Install Helm

https://www.metricfire.com/blog/monitoring-kubernetes-tutorial-using-grafana-and-prometheus/

Create a monitor namespace

```
kubectl create ns monitor
```

```
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo add stable https://charts.helm.sh/stable
helm repo update
helm install my-prometheus prometheus-community/kube-prometheus-stack --namespace monitor
```

[https://github.com/prometheus-community/helm-charts/tree/main/charts/kube-prometheus-stack#configuration](https://github.com/prometheus-community/helm-charts/tree/main/charts/kube-prometheus-stack#configuration)

```
kubectl port-forward -n monitor prometheus-my-prometheus-kube-prometh-prometheus-0 9090
```

```
kubectl get pods  --namespace monitor -l app.kubernetes.io/name=grafana
```

To fetch the grafana password:
```
kubectl get secret --namespace monitor my-prometheus-grafana -o jsonpath="{.data.admin-password}" | base64 --decode ; echo
```

Should be "prom-operator"


To access th Grafana page locally (localhost:3000)
```
kubectl port-forward -n monitor $(kubectl get pods  --namespace monitor -l app.kubernetes.io/name=grafana) 3000
```
