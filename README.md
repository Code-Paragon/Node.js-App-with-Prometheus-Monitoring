#  Node.js App with Prometheus Monitoring

A lightweight Node.js web application instrumented with Prometheus metrics and designed for containerized deployment and Kubernetes integration. Useful for demonstrating basic monitoring, instrumentation, and service exposure.

---

##  Features

- **Express-based Web Server** with basic team info UI
- **Prometheus Metrics Endpoint** at `/metrics`
- **Custom Metrics:** request counts and durations
- **Dockerized Build** with a minimal `Dockerfile`
- **Kubernetes YAMLs** for deployment, service, and monitoring integration
- **Images served over REST** for profile pictures

---

##  Tech Stack

- Node.js + Express
- prom-client (Prometheus instrumentation)
- Docker
- Kubernetes
- Prometheus Operator (ServiceMonitor)

---

##  Project Structure

```text
app/
├─ index.html        # Team dashboard UI
├─ server.js         # Express server with metrics
├─ images/           # Profile images
k8s/
├─ k8s-config.yaml   # K8S deployment + service + ServiceMonitor
Dockerfile           # Docker image build file
package.json         # Node.js dependencies
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
