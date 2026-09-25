\# Trend – DevOps CI/CD and Kubernetes Deployment



\## 1. Project Overview



This project demonstrates a complete DevOps CI/CD workflow for deploying a production-ready React static application.



The application is available as compiled production files inside the `dist/` directory. No Node.js build process is required for deployment.



\### DevOps Workflow



```text

GitHub

&#x20;  ↓

GitHub Webhook

&#x20;  ↓

Jenkins

&#x20;  ↓

Docker Build

&#x20;  ↓

DockerHub

&#x20;  ↓

Amazon EKS

&#x20;  ↓

Kubernetes Service

&#x20;  ↓

AWS LoadBalancer

&#x20;  ↓

Trend Application

```



Monitoring is implemented using:



```text

Kubernetes

&#x20;  ↓

Prometheus

&#x20;  ↓

Grafana

```



\---



\## 2. Technologies Used



\* GitHub

\* Jenkins

\* Docker

\* DockerHub

\* Kubernetes

\* Amazon EKS

\* Amazon EC2

\* AWS LoadBalancer

\* Terraform

\* Helm

\* Prometheus

\* Grafana

\* Nginx



\---



\## 3. Repository Structure



```text

Trend/

│

├── dist/

│   ├── index.html

│   └── vite.svg

│

├── k8s/

│   ├── deployment.yaml

│   └── service.yaml

│

├── terraform/

│   └── main.tf

│

├── Dockerfile

├── nginx.conf

└── README.md

```



\---



\## 4. Application



The application consists of production-ready static files in the `dist/` directory.



The project intentionally does not contain:



\* `package.json`

\* Node.js dependencies

\* Source-code build steps



The compiled files are served using Nginx.



\---



\## 5. Docker



The application is containerized using Nginx.



The Docker image is:



```text

trend-app:latest

```



The container exposes port:



```text

80

```



\### Build Docker Image



```bash

docker build -t trend-app:latest .

```



\### Run Locally



```bash

docker run -d -p 8080:80 trend-app:latest

```



The application can then be accessed at:



```text

http://localhost:8080

```



\---



\## 6. DockerHub



The Jenkins pipeline pushes the Docker image to DockerHub.



DockerHub repository:



```text

sandhyareddy12/trend-app

```



Docker image:



```text

sandhyareddy12/trend-app:latest

```



The image is automatically pushed by Jenkins after a successful Docker build.



\---



\## 7. Jenkins CI/CD



Jenkins is hosted on an AWS EC2 instance.



The Jenkins pipeline performs the following stages:



1\. Checkout source code from GitHub

2\. Build Docker image

3\. Login to DockerHub

4\. Push Docker image to DockerHub

5\. Deploy the application to Amazon EKS

6\. Verify Kubernetes rollout



\### Jenkins Pipeline



```text

GitHub

&#x20;  ↓

Checkout

&#x20;  ↓

Docker Build

&#x20;  ↓

DockerHub Push

&#x20;  ↓

Deploy to EKS

&#x20;  ↓

Rollout Verification

```



\---



\## 8. GitHub Webhook



A GitHub webhook is configured to automatically trigger the Jenkins pipeline when changes are pushed to the repository.



Webhook endpoint:



```text

http://13.62.46.167:8080/github-webhook/

```



The webhook was tested successfully and automatically triggered a Jenkins build after a GitHub repository change.



\---



\## 9. AWS Infrastructure



The infrastructure is deployed in:



```text

AWS Region: eu-north-1

```



Amazon EKS cluster:



```text

trend-eks

```



Managed node group:



```text

trend-nodes

```



Terraform is used to provision the AWS infrastructure.



\---



\## 10. Amazon EKS



The application runs on Amazon EKS.



The cluster currently has two worker nodes.



Check the nodes:



```bash

kubectl get nodes

```



The Kubernetes deployment is:



```text

trend-app

```



Check the deployment:



```bash

kubectl get deployment trend-app

```



Check the application pods:



```bash

kubectl get pods

```



\---



\## 11. Kubernetes Deployment



The Kubernetes deployment is defined in:



```text

k8s/deployment.yaml

```



The deployment uses the DockerHub image:



```text

sandhyareddy12/trend-app:latest

```



The application container listens on port:



```text

80

```



\---



\## 12. Kubernetes Service



The application is exposed using a Kubernetes `LoadBalancer` service.



Service name:



```text

trend-app

```



Service port:



```text

3000

```



Container target port:



```text

80

```



Check the service:



```bash

kubectl get svc trend-app

```



\---



\## 13. Application URL



The deployed application is available through the AWS internet-facing LoadBalancer:



```text

http://a083997d3586e4a88befcdae41001b0d-166738597.eu-north-1.elb.amazonaws.com:3000

```



LoadBalancer name:



```text

a083997d3586e4a88befcdae41001b0d

```



LoadBalancer type:



```text

Classic Load Balancer

```



Scheme:



```text

Internet-facing

```



\---



\## 14. Monitoring



Monitoring is implemented using the open-source Prometheus and Grafana stack.



The monitoring components were installed using Helm.



Helm repository:



```text

prometheus-community

```



Chart:



```text

kube-prometheus-stack

```



Monitoring namespace:



```text

monitoring

```



\### Monitoring Components



\* Prometheus

\* Grafana

\* Alertmanager

\* Prometheus Operator

\* Kubernetes State Metrics

\* Node Exporter



Check monitoring pods:



```bash

kubectl get pods -n monitoring

```



\---



\## 15. Prometheus



Prometheus collects metrics from the Kubernetes environment.



Prometheus is deployed as part of the `kube-prometheus-stack`.



Check Prometheus:



```bash

kubectl get pods -n monitoring

```



The Prometheus pod should show a `Running` status.



Prometheus is responsible for collecting Kubernetes and infrastructure metrics used by Grafana.



\---



\## 16. Grafana



Grafana is used to visualize Kubernetes metrics collected by Prometheus.



Grafana is accessed using Kubernetes port forwarding:



```bash

kubectl port-forward svc/monitoring-grafana 3001:80 -n monitoring

```



Grafana URL:



```text

http://localhost:3001

```



Grafana provides Kubernetes monitoring dashboards showing information such as:



\* CPU usage

\* Memory usage

\* Nodes

\* Pods

\* Kubernetes workloads

\* Network metrics



The Kubernetes dashboards were verified successfully and displayed monitoring data.



\---



\## 17. Useful Kubernetes Commands



\### Check Nodes



```bash

kubectl get nodes

```



\### Check Application Pods



```bash

kubectl get pods

```



\### Check Application Service



```bash

kubectl get svc trend-app

```



\### Check Deployment



```bash

kubectl get deployment trend-app

```



\### Check Deployment Rollout



```bash

kubectl rollout status deployment/trend-app

```



\### Check Monitoring



```bash

kubectl get pods -n monitoring

```



\### Access Grafana



```bash

kubectl port-forward svc/monitoring-grafana 3001:80 -n monitoring

```



\---



\## 18. CI/CD Result



The completed CI/CD workflow automatically:



1\. Detects a GitHub change through the webhook.

2\. Starts Jenkins.

3\. Checks out the latest code.

4\. Builds the Docker image.

5\. Pushes the image to DockerHub.

6\. Deploys the application to Amazon EKS.

7\. Verifies the Kubernetes rollout.



This provides an automated deployment workflow from GitHub to the Kubernetes environment.



\---



\## 19. Final Result



The Trend application is successfully:



\* Containerized with Docker

\* Served using Nginx

\* Stored in DockerHub

\* Automatically built by Jenkins

\* Automatically triggered through a GitHub webhook

\* Deployed to Amazon EKS

\* Exposed through an AWS internet-facing LoadBalancer

\* Monitored using Prometheus

\* Visualized using Grafana



The project demonstrates a complete DevOps workflow covering:



\*\*CI/CD → Containerization → DockerHub → Kubernetes → AWS EKS → LoadBalancer → Monitoring\*\*



<<<<<<< HEAD
No package.json is needed
CI/CD pipeline configured with Jenkins and EKS.
=======
>>>>>>> f60f5aa (Update project documentation)
