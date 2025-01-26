# Kubernetes Observability with Prometheus and Grafana

## Project Overview
This project demonstrates setting up observability for a Kubernetes application using Kind (Kubernetes in Docker), Prometheus, and Grafana.

## Prerequisites
- Linux Server/Instance
- Docker
- Helm
- kubectl

## Step-by-Step Setup

### 1. System Preparation
```bash
# Update system packages
sudo apt-get update

# Install Docker
sudo apt-get install docker.io

# Give Docker permissions
sudo usermod -aG docker $USER && newgrp docker
```

### 2. Install Kind (Kubernetes in Docker)
```bash
# Create Kubernetes cluster
kind create cluster --config=config.yml --name=my-cluster

# Verify nodes
kubectl get nodes
```

### 3. Deploy Application
```bash
# Apply all Kubernetes manifests
kubectl apply -f .

# Verify deployments
kubectl get all
```

### 4. Install Prometheus and Grafana using Helm
```bash
# Add Helm repositories
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo add stable https://charts.helm.sh/stable
helm repo update

# Create monitoring namespace
kubectl create namespace monitoring

# Install Prometheus Stack
helm install kind-prometheus prometheus-community/kube-prometheus-stack \
    --namespace monitoring \
    --set prometheus.service.nodePort=30000 \
    --set prometheus.service.type=NodePort \
    --set grafana.service.nodePort=31000 \
    --set grafana.service.type=NodePort
```

### 5. Port Forwarding for Access
```bash
# Forward Prometheus
kubectl port-forward svc/kind-prometheus-kube-prome-prometheus -n monitoring 9090:9090 --address=0.0.0.0 &

# Forward Grafana
kubectl port-forward svc/kind-prometheus-grafana -n monitoring 31000:80 --address=0.0.0.0 &
```

### 6. Monitoring and Metrics
- Access Prometheus: `http://[PublicIP]:9090`
- Access Grafana: `http://[PublicIP]:31000`
  - Default Login: admin/prom-operator

### Sample PromQL Queries
```promql
# CPU Usage Percentage
sum(rate(container_cpu_usage_seconds_total{namespace="default"}[1m])) / sum(machine_cpu_cores) * 100
```

## Key Observability Components
- **Prometheus**: Time-series metrics database
- **Grafana**: Visualization and dashboarding
- **Node Exporter**: Metrics collection for system resources

## Recommended Grafana Dashboards
- Kubernetes Cluster Monitoring
- Node Exporter Full
- Custom Application Dashboards

## Troubleshooting
- Ensure ports are opened in security groups
- Check namespace configurations
- Verify Helm and kubectl configurations

## Contributing
Contributions are welcome! Please fork the repository and submit pull requests.
