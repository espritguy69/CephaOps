# Infra Overview – CephasOps

This folder defines **how CephasOps is deployed** in different environments.

- Local dev: docker-compose
- Staging/Prod: Kubernetes (optionally via Terraform in the cloud)
- Monitoring: Prometheus + Grafana

## Structure

- `k8s/` – Kubernetes deployment, service, ingress, configmap.
- `terraform/` – Cloud infrastructure templates.
- `monitoring/` – Prometheus + Grafana configs.

Infra must remain **stateless**, all business state lives in the DB and object storage.
