#  Node.js App with CI/CD & Kubernetes Monitoring

A containerized Node.js web application enhanced with Prometheus instrumentation, GitHub Actions CI/CD pipeline, and full Kubernetes deployment support via Terraform. This project demonstrates hands-on DevOps practices including build/test automation, cloud-native deployment, and observability using Prometheus & Grafana.

---

##  Features

- **Express-based Web Server** with profile dashboard UI

- **Prometheus Metrics Endpoint** exposed at /metrics

- **Custom Application Metrics:** HTTP request counts and durations

- **Dockerized Build** with a lightweight Dockerfile

- **CI/CD Pipeline** via GitHub Actions:

    - Run tests

    - SonarCloud code quality scan

    - Docker build and push

    - Kubernetes deployment

- **Infrastructure as Code** with Terraform for AWS EKS provisioning

- **Prometheus Stack** installed via Helm

- **Monitoring Dashboard** powered by Grafana (CPU, memory, etc.)

---

##  Tech Stack

- Node.js + Express

- prom-client (Prometheus metrics)

- Docker + DockerHub

- GitHub Actions (CI/CD)

- Terraform (EKS Infrastructure)

- Kubernetes

- Prometheus + Grafana (Observability Stack)

- SonarCloud (Static Code Analysis)

---

##  Project Structure

```text
nodejs-app-monitoring/
├── .github/
│   └── workflows/
│       └── ci-cd.yaml            # GitHub Actions CI/CD pipeline
├── app/
│   ├── images/                   # Profile images served by the app
│   ├── index.html                # UI for team info
│   └── server.js                 # Express server with Prometheus metrics
├── k8s/
│   └── k8s-config.yaml           # Kubernetes Deployment, Service, ServiceMonitor
├── Terraform/
│   ├── eks-cluster.tf            # EKS cluster provisioning
│   ├── providers.tf              # AWS provider configuration
│   ├── terraform.tfvars          # Customizable CIDR block variables
│   ├── vpc.tf                    # VPC and networking resources
│   └── kubeconfig_myapp-eks-cluster  # Generated kubeconfig (excluded by .gitignore)
├── Dockerfile                    # Docker build config
├── package.json                  # Node.js dependencies and scripts
├── package-lock.json             # Exact versions for reproducible installs
├── .dockerignore                 # Files ignored during Docker build
├── .gitignore                    # Files ignored by Git
├── .sonarcloud.properties        # SonarCloud project configuration
└── README.md                     # Project documentation
```
---

##  Metrics Exposed

| Metric                       | Description                          |
|------------------------------|--------------------------------------|
| `http_request_operations_total` | Total number of HTTP requests        |
| `http_request_duration_seconds` | Histogram of request durations       |

Access metrics at:

```arduino
http://<your-service-ip>:3000/metrics
```
---

##  Docker Usage

```bash
docker build -t your-repo/node-monitoring:latest .
docker push your-repo/node-monitoring:latest
```
---

##  Kubernetes Deployment

Ensure Prometheus operator is running and apply:

```bash
kubectl apply -f k8s/k8s-config.yaml
```

---
## CI/CD and Infrastructure Deployment (EKS + GitHub Actions)
This project has a full CI/CD pipeline configured via GitHub Actions, deploying the app to an EKS cluster provisioned using Terraform, with integrated testing, code analysis (SonarCloud), Dockerization, and monitoring setup using Prometheus and Grafana.

### Pipeline Stages
1. Build & Test

   - Runs npm ci and npm test with Jest

2. SonarCloud Scan

   - Static code analysis via SonarCloud

3. Dockerize & Push

   - Builds and pushes the image to Docker Hub

4. Deploy to EKS

   - Updates Kubeconfig and deploys via kubectl
---
### Installs Prometheus stack using Helm

#### Required GitHub Secrets
| Secret Name           | Purpose                                                              |
|------------------------|----------------------------------------------------------------------|
| AWS_ACCESS_KEY_ID      | IAM User Access Key for AWS                                          |
| AWS_SECRET_ACCESS_KEY  | IAM User Secret Key                                                  |
| AWS_REGION             | AWS region of EKS (e.g., eu-central-1)                               |
| DOCKERHUB_USERNAME     | Your Docker Hub username                                             |
| DOCKERHUB_TOKEN        | Docker Hub personal access token (under Account Settings > Security) |
| SONAR_TOKEN            | From SonarCloud                                                      |
| KUBECONFIG_DATA        | Base64 encoded content of your `kubeconfig` file after EKS is provisioned |

### How to Get KUBECONFIG_DATA
After provisioning your EKS with Terraform:

```bash
aws eks update-kubeconfig --name <your-cluster-name> --region <your-region>
cat ~/.kube/config | base64
```
Copy the base64 output and add it as the KUBECONFIG_DATA GitHub secret. This is auto-decoded and used in your pipeline to deploy via kubectl.

### Terraform Infrastructure Setup
Before running the pipeline the first time, you must manually provision the infrastructure:

1. Navigate to your terraform/ directory.

2. Create a file named terraform.tfvars with values like:

```hcl
vpc_cidr_block            = "10.0.0.0/16"
private_subnet_cidr_blocks = ["10.0.1.0/24", "10.0.2.0/24", "10.0.3.0/24"]
public_subnet_cidr_blocks  = ["10.0.4.0/24", "10.0.5.0/24", "10.0.6.0/24"]
```
3. Then run:

```bash
terraform init
terraform apply
```
After use, destroy infrastructure manually:

```bash
terraform destroy
```

### Testing the App & Monitoring Tools
#### Check if Node.js App Works
- Get external IP:

```bash
kubectl get svc -n default
```
- Access in browser:

```bash
http://<external-ip>:80
```
#### Check Prometheus
Port-forward if local:

```bash
kubectl port-forward svc/prometheus-operated 9090:9090 -n monitoring
```
- Then open:

```arduino
http://localhost:9090
```
#### Check Grafana
Port-forward if local:

```bash
kubectl port-forward svc/prometheus-grafana 3000:80 -n monitoring
```
Login:

- User: admin

- Pass: prom-operator (default unless changed)

Explore Dashboards ➝ Kubernetes ➝ Pods