# ☁️ Nextcloud Kubernetes Deployment

This repository contains Kubernetes manifests for deploying a Nextcloud environment, including its dependencies, such as a PostgreSQL database, Redis, and a mail server.

## Prerequisites

Before deploying the application, ensure the following prerequisites are met:

1. **Kubernetes Cluster**: A functional Kubernetes cluster is required.
2. **kubectl**: The Kubernetes CLI tool is installed and configured to connect to the cluster.
3. **Persistent Storage**: Ensure that the cluster has access to persistent storage for PostgreSQL data.
4. **Ingress Controller (Optional)**: If you want to expose Nextcloud externally with a domain name, ensure an ingress controller is installed.

## Deployment Overview

The deployment consists of the following components:

1. **Nextcloud Web Application**: Runs the Nextcloud instance.
2. **PostgreSQL Database**: Stores Nextcloud data.
3. **Redis**: Handles caching for improved performance.
4. **Mail Server**: Provides SMTP services for Nextcloud.

## Components and Configuration

### ConfigMap
The `ConfigMap` defines environment variables shared across components. These include database, Redis, and mail server settings.

### Secrets
The `Secret` object contains sensitive data such as the PostgreSQL password. Ensure to base64 encode secret values.

### Services
- **`nextcloud-web-service`**: Exposes the Nextcloud application.
- **`nextcloud-database`**: Exposes the PostgreSQL database internally.
- **`nextcloud-redis`**: Exposes the Redis service internally.
- **`nextcloud-mail-server` and `nextcloud-mail-server-ui`**: Expose the mail server for SMTP and UI.

### Deployments
- **Nextcloud Web Application**: A deployment with 3 replicas for high availability.
- **PostgreSQL Database**: A single-replica deployment with persistent storage.
- **Redis**: A single-replica deployment for caching.
- **Mail Server**: A lightweight deployment of MailDev with 2 replicas for SMTP and UI access.

## Deployment Steps

### 1. Apply Configurations
Run the following command to apply the manifests:
```sh
kubectl apply -f <filename>.yaml
```

### 2. Verify Deployments
Check the status of all deployments:
```sh
kubectl get deployments
```
Ensure all pods are running:
```sh
kubectl get pods
```

### 3. Access Nextcloud
Once all services are running, access Nextcloud via the `nextcloud-web-service`. If using a `LoadBalancer`, retrieve the external IP:
```sh
kubectl get services
```
## Links

- **NextCloud** :  https://localhost/80
- **MailDev** :  http://localhost:1080

## Environment Variables

### ConfigMap Variables
| Variable              | Description                    |
|-----------------------|--------------------------------|
| `MAIL_DOMAIN`         | Domain for sending emails.    |
| `MAIL_FROM_ADDRESS`   | Sender email address.         |
| `POSTGRES_DB`         | PostgreSQL database name.     |
| `POSTGRES_HOST`       | PostgreSQL host.              |
| `POSTGRES_USER`       | PostgreSQL user.              |
| `REDIS_HOST`          | Redis host.                   |
| `REDIS_HOST_PORT`     | Redis port.                   |
| `SMTP_AUTHTYPE`       | SMTP authentication type.     |
| `SMTP_HOST`           | SMTP server host.             |
| `SMTP_PORT`           | SMTP server port.             |

### Secret Variables
| Variable              | Description                |
|-----------------------|----------------------------|
| `POSTGRES_PASSWORD`   | PostgreSQL user password.  |

## Notes

- **Persistent Storage**: The PostgreSQL deployment uses a `hostPath` volume. In a production environment, consider using a networked storage solution such as NFS or a cloud provider's persistent storage.
- **Scaling**: The web and mail services are scaled for high availability. Adjust replica counts as needed.
- **Load Balancer**: The services `nextcloud-web-service` and `nextcloud-mail-server-ui` use `LoadBalancer` type. Ensure your Kubernetes cluster supports it.

## Troubleshooting

- **Pods Not Running**: Check pod logs for errors:
  ```sh
  kubectl logs <pod-name>
  ```
- **Service Unavailable**: Verify service endpoints and cluster networking:
  ```sh
  kubectl describe services
  ```
- **Persistent Storage Issues**: Ensure the `hostPath` directory exists and is writable.