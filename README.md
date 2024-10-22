# Prometheus-Grafana-Setup-On-Kind_Cluster
- To install Prometheus and Grafana using Helm in your kind cluster, follow these step-by-step instructions.
![image](https://github.com/user-attachments/assets/3d4d4501-8350-4abc-8ebf-0068992cfd57)

## Step-by-Step Guide

## Step- 1. Create a Kind Cluster (If not already running)

### Install Docker:
```
sudo apt install docker.io
```
### Install kubectl:
```
sudo snap install kubectl --classic
kubectl version --client
Client Version: version.Info{Major:"1", Minor:"26", GitVersion:"v1.26.0", ...}
```

### Install kind:
```
curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.20.0/kind-linux-amd64
chmod +x ./kind
sudo mv ./kind /usr/local/bin/kind
```

### Create a kind cluster:

```
kind create cluster
```

### Create a Configuration File for the `Multi-Node` Cluster
- Create a `kind.yaml` file:
- You can find the kind configuration file here: [kind.yaml](kind.yaml).
```
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
  - role: control-plane
  - role: worker
  - role: worker

```
- Create the Cluster
```
kind create cluster --config kind.yaml
```
- Verify the nodes:
```
kubectl get nodes
```
- Delete the Cluster
```
kind delete cluster
```
## Step- 2. Install Helm (If not installed)
```
curl https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash
```
```
helm version
```

## Step- 3. Add Prometheus and Grafana Helm Repositories
- Add the official Prometheus and Grafana Helm chart repositories:
```
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo add grafana https://grafana.github.io/helm-charts
helm repo update
```
## Step- 4. Install Prometheus Using Helm
- Deploy the Prometheus stack in your kind cluster using:
```
helm install prometheus prometheus-community/kube-prometheus-stack --namespace monitoring --create-namespace
```
- This will install Prometheus, AlertManager, and node exporters as part of the kube-prometheus-stack.

## Step- 5. Verify Prometheus Installation
- Check if the pods and services are running:
```
kubectl get all -n monitoring
```
- You should see prometheus-server, alertmanager, and other related pods.

## Step- 6. Install Grafana Using Helm
- Next, install Grafana using the following command:
```
helm install grafana grafana/grafana --namespace monitoring
```
## Step- 7. Optional: Expose Services via NodePort
- To avoid port-forwarding, you can expose services via NodePort:
```
kubectl patch svc grafana -n monitoring -p '{"spec": {"type": "NodePort"}}'
kubectl patch svc prometheus-kube-prometheus-prometheus -n monitoring -p '{"spec": {"type": "NodePort"}}'
```
## Step- 8. Accessing Prometheus and Grafana
- Port Forward Prometheus:
```
kubectl port-forward svc/prometheus-kube-prometheus-prometheus -n monitoring 9090:9090
```
Access Prometheus in your browser at:  `http://localhost:9090`

- Port Forward Grafana:
```
kubectl port-forward svc/grafana -n monitoring 3000:3000
```
Access Grafana at:
http://localhost:3000

Default Grafana Credentials:
Username: admin
Password: admin

## Step- 9. Add Prometheus as a Data Source in Grafana
- URL Use --> `http://localhost:3000` , `http://prometheus-kube-prometheus-prometheus.monitoring.svc:9090`

## Step- 10. Create Dashboards in Grafana
