# Copilot Instructions for Todolist Kubernetes Project

## Architecture Overview
This project deploys a todolist application on Kubernetes with a MariaDB database and a Flask-based frontend app. The backend deployment (`backend.yaml`) runs MariaDB, exposed internally via ClusterIP service. The frontend deployment (`frontend.yaml`) runs the app container, using an init container to wait for database readiness before starting.

Key components:
- **Database**: MariaDB:latest, configured via ConfigMap (`todolist-config.yaml`) and Secret (`todolist-secret.yaml`)
- **App**: ghcr.io/bennyro-mta/todolist:1.5, connects to DB using env vars from ConfigMap/Secret
- **Networking**: Frontend exposed via LoadBalancer on port 5000, backend internal on 3306

## Critical Patterns
- **Dependency Management**: Use init containers for service waits (e.g., `nslookup backend` in `frontend.yaml`)
- **Configuration**: Non-sensitive data in ConfigMap, passwords in Secret; injected via `envFrom`
- **Service Exposure**: LoadBalancer for external access, ClusterIP for internal services
- **Naming**: Services named to match deployment labels (e.g., `app: backend` selector)

## Developer Workflows
- **Deploy**: `kubectl apply -f EX4/`
- **Status Check**: `kubectl get pods,svc -n default`
- **Debugging**: `kubectl logs <pod-name>`; use `kubectl describe` for events
- **Port Forwarding**: `kubectl port-forward svc/frontend-svc 5000:5000` for local access

## Project Conventions
- Single replica deployments for simplicity
- Root DB user with password from Secret (not production-ready)
- Busybox init containers for lightweight dependency checks
- YAML structure: Deployment + Service per component

Reference files: `EX4/backend.yaml`, `EX4/frontend.yaml`, `EX4/todolist-config.yaml`, `EX4/todolist-secret.yaml`