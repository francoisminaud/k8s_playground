Source for Terraform files: https://github.com/hashicorp/learn-terraform-provision-gke-cluster

TODO: Find a way to automate "Enable Compute Engine API" when creating a new project
TODO: Find a way to automate "Enable Kubernetes Engine API" when creating a new project

### Howto


1) Spin up the cluster
Make sure terraform is installed and version > 0.12
terraform init
terraform plan -out=plan.out
terraform apply "plan.out"

2) Make sure your kubectl has got the right context


gcloud container clusters get-credentials $(terraform output kubernetes_cluster_name) --region $(terraform output region)

Fetching cluster endpoint and auth data.
kubeconfig entry generated for terraform-k8s-gke-playground-gke.

3) Deploy a webserver

kubectl apply -f deployment.yml

4) Make sure it's up and running

kubectl get deployment deployment-nginx
kubectl get po

5) Expose the service to the outside world

kubectl expose deployment nginx-deployment --type=LoadBalancer --name=my-nginx-service

6) Wait a bit until your service gets an external IP address

7) kubectl get svc my-nginx-service

NAME               TYPE           CLUSTER-IP      EXTERNAL-IP     PORT(S)        AGE
my-nginx-service   LoadBalancer   10.147.244.21   35.189.80.117   80:30954/TCP   16m

8) Try connecting to the webserver
