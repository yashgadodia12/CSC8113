# Cloud-Native Bookstore Microservices Platform

A full-stack bookstore platform built using a **microservices architecture** and deployed with **Docker and Kubernetes**.

The application separates catalog, user, and order functionality into independently deployable Node.js services, with a React frontend and MongoDB persistence.

The project demonstrates practical cloud-native engineering concepts including service decomposition, REST APIs, containerization, Kubernetes orchestration, service discovery, authentication, ingress routing, and scalable application deployment.

---

## Architecture

```mermaid
flowchart LR
    User[User]

    Frontend[React Frontend]
    Ingress[NGINX Ingress]

    Catalog[Catalog Service]
    UserService[User Service]
    Order[Order Service]

    MongoDB[(MongoDB)]

    User --> Frontend
    Frontend --> Ingress

    Ingress -->|/catalog| Catalog
    Ingress -->|/user| UserService
    Ingress -->|/order| Order

    Catalog --> MongoDB
    UserService --> MongoDB
    Order --> MongoDB

    Order --> Catalog
```

---

## Overview

The platform follows a microservices-based architecture where major business capabilities are separated into individual services.

### Catalog Service

Handles bookstore inventory and book-related operations.

Responsibilities include:

- Retrieve all books
- Retrieve individual book details
- Add books
- Update book information
- Delete books
- Manage inventory
- Reduce stock when purchases are completed

### User Service

Handles identity and user management.

Responsibilities include:

- User registration
- User login
- Password hashing
- JWT-based authentication
- Protected routes
- User profile management

### Order Service

Handles the purchasing workflow.

Responsibilities include:

- Create orders
- Retrieve user orders
- Retrieve individual order details
- Manage order information
- Validate authenticated requests
- Coordinate inventory updates

### Frontend

A React-based user interface providing:

- Book browsing
- Book detail pages
- Registration and login
- Shopping cart
- Checkout
- Order history
- User profile management

The production frontend is built as a static application and served using NGINX.

---

## Technology Stack

| Category | Technology |
|---|---|
| Frontend | React, JavaScript |
| Backend | Node.js, Express.js |
| Database | MongoDB, Mongoose |
| Authentication | JWT, bcrypt |
| API Communication | REST, Axios |
| Containers | Docker |
| Orchestration | Kubernetes |
| Routing | NGINX Ingress |
| Cloud Platform | Google Cloud Platform |
| Container Registry | Google Artifact Registry |
| Web Server | NGINX |

---

## Cloud-Native Architecture

The backend is divided into independently deployable services:

```text
                   ┌─────────────────────┐
                   │    React Frontend   │
                   └──────────┬──────────┘
                              │
                              ▼
                   ┌─────────────────────┐
                   │     NGINX Ingress   │
                   └──────────┬──────────┘
                              │
              ┌───────────────┼───────────────┐
              │               │               │
              ▼               ▼               ▼
      ┌───────────────┐ ┌─────────────┐ ┌───────────────┐
      │ Catalog       │ │ User        │ │ Order         │
      │ Service       │ │ Service     │ │ Service       │
      │ :5001         │ │ :5002       │ │ :5003         │
      └───────┬───────┘ └──────┬──────┘ └───────┬───────┘
              │                │                 │
              └────────────────┼─────────────────┘
                               │
                               ▼
                     ┌───────────────────┐
                     │      MongoDB      │
                     └───────────────────┘
```

Each backend service is exposed internally through a Kubernetes `ClusterIP` service.

External API traffic is routed through an NGINX Ingress controller.

---

## API Routing

Ingress routes incoming requests to the appropriate backend service.

| Route | Service |
|---|---|
| `/catalog` | Catalog Service |
| `/user` | User Service |
| `/order` | Order Service |

This provides the frontend with a single entry point while keeping backend services independently deployable.

---

## Kubernetes Deployment

The application uses Kubernetes resources for deployment and service discovery.

The backend contains Kubernetes configurations for:

- Catalog Service
- User Service
- Order Service
- MongoDB
- Kubernetes Secrets
- Network configuration
- Ingress routing

Backend services are deployed with multiple replicas to improve availability and demonstrate horizontal scaling.

Example architecture:

```text
Catalog Service
    │
    ├── Pod 1
    └── Pod 2

User Service
    │
    ├── Pod 1
    └── Pod 2

Order Service
    │
    ├── Pod 1
    └── Pod 2
```

---

## Docker

Each application component is containerized independently.

The backend services use Node.js-based Docker images:

```text
catalog-service
user-service
order-service
```

The frontend uses a multi-stage Docker build:

```text
Node.js
   │
   │ npm run build
   ▼
React Production Build
   │
   ▼
NGINX
```

This keeps the final frontend container lightweight and suitable for production deployment.

---

## Repository Structure

```text
cloud-native-bookstore-microservices/
│
├── backend/
│   │
│   ├── catalog-service/
│   │   ├── controllers/
│   │   ├── models/
│   │   ├── routes/
│   │   ├── Dockerfile
│   │   └── server.js
│   │
│   ├── user-service/
│   │   ├── controllers/
│   │   ├── middleware/
│   │   ├── models/
│   │   ├── routes/
│   │   ├── Dockerfile
│   │   └── server.js
│   │
│   ├── order-service/
│   │   ├── controllers/
│   │   ├── middleware/
│   │   ├── models/
│   │   ├── routes/
│   │   ├── Dockerfile
│   │   └── server.js
│   │
│   └── k8s/
│       ├── catalog-deployment.yaml
│       ├── user-deployment.yaml
│       ├── order-deployment.yaml
│       ├── mongodb.yaml
│       ├── ingress.yaml
│       └── allow-mongo-egress.yaml
│
├── frontend/
│   ├── src/
│   │   ├── api/
│   │   ├── components/
│   │   ├── context/
│   │   └── pages/
│   │
│   ├── k8s/
│   ├── Dockerfile
│   ├── nginx.conf
│   └── package.json
│
└── README.md
```

---

## Application Features

### Book Management

- Browse available books
- View detailed book information
- Add new books
- Update book information
- Delete books
- Track available stock
- Reduce inventory after purchases

### Authentication

- User registration
- Secure login
- JWT-based authentication
- Password hashing
- Protected backend endpoints
- User profile management

### Shopping

- Add books to cart
- Review shopping cart
- Checkout workflow
- Place orders
- Review previous orders

### Infrastructure

- Dockerized frontend and backend
- Kubernetes-based orchestration
- Multiple backend replicas
- Internal Kubernetes service discovery
- NGINX Ingress routing
- Kubernetes Secrets
- MongoDB persistence
- Google Artifact Registry integration

---

## Local Development

### Prerequisites

Install:

- Node.js 18+
- npm
- MongoDB
- Docker
- Git

Clone the repository:

```bash
git clone https://github.com/yashgadodia12/cloud-native-bookstore-microservices.git
cd cloud-native-bookstore-microservices
```

> If the repository has not yet been renamed, use the current GitHub repository URL instead.

---

## Environment Variables

Sensitive application configuration should be supplied using environment variables.

Example:

```env
MONGO_URI=mongodb://localhost:27017/bookstore
JWT_SECRET=replace-with-a-secure-random-value
```

Do not commit real credentials or production secrets to Git.

For Kubernetes deployments, secrets should be created separately.

Example:

```bash
kubectl create secret generic mongodb-secret \
  --from-literal=MONGO_URI="mongodb://username:password@mongodb:27017/bookstore"
```

---

## Run the Frontend

Navigate to the frontend directory:

```bash
cd frontend
```

Install dependencies:

```bash
npm install
```

Start the development server:

```bash
npm start
```

For a production build:

```bash
npm run build
```

---

## Docker Deployment

The project contains individual Dockerfiles for the application services.

Example image build:

```bash
docker build -t bookstore-catalog ./backend/catalog-service
```

Repeat the process for:

```text
catalog-service
user-service
order-service
frontend
```

For cloud deployment, images can be tagged and pushed to a container registry such as Google Artifact Registry.

---

## Kubernetes Deployment

Ensure a Kubernetes cluster is available and `kubectl` is configured.

Deploy MongoDB:

```bash
kubectl apply -f backend/k8s/mongodb.yaml
```

Deploy the catalog service:

```bash
kubectl apply -f backend/k8s/catalog-deployment.yaml
```

Deploy the user service:

```bash
kubectl apply -f backend/k8s/user-deployment.yaml
```

Deploy the order service:

```bash
kubectl apply -f backend/k8s/order-deployment.yaml
```

Deploy the frontend:

```bash
kubectl apply -f frontend/k8s/frontend-deployment.yaml
```

Deploy Ingress:

```bash
kubectl apply -f backend/k8s/ingress.yaml
```

Verify the deployment:

```bash
kubectl get pods
kubectl get deployments
kubectl get services
kubectl get ingress
```

---

## Google Cloud Deployment

The project supports a container-based deployment workflow on Google Cloud.

A typical workflow is:

```text
Application Code
      │
      ▼
Docker Image
      │
      ▼
Google Artifact Registry
      │
      ▼
Kubernetes Deployment
      │
      ▼
Kubernetes Services
      │
      ▼
NGINX Ingress
      │
      ▼
Application
```

Container images are versioned and referenced by Kubernetes Deployment manifests.

---

## Security

The application includes several security-related mechanisms:

- JWT-based authentication
- Password hashing
- Protected backend routes
- Kubernetes Secrets
- Environment-based configuration
- Internal `ClusterIP` services
- Controlled external routing through Ingress

For a production deployment, additional controls should include:

- HTTPS/TLS
- Managed secret storage
- Database access controls
- API rate limiting
- Container vulnerability scanning
- Kubernetes RBAC
- Network policies
- Regular dependency updates

---

## Engineering Concepts Demonstrated

This project demonstrates practical experience with:

- Cloud-native architecture
- Microservices
- REST API development
- Docker containerization
- Kubernetes orchestration
- Kubernetes Deployments
- Kubernetes Services
- Ingress routing
- Service-to-service communication
- Horizontal replication
- MongoDB integration
- Authentication and authorization
- Container registries
- Environment-based configuration
- Frontend/backend separation

---

## Future Improvements

Potential enhancements include:

- GitHub Actions CI/CD pipelines
- Helm charts
- Terraform-based infrastructure provisioning
- Horizontal Pod Autoscaling
- Kubernetes readiness probes
- Kubernetes liveness probes
- HTTPS with cert-manager
- Managed database services
- Prometheus monitoring
- Grafana dashboards
- Centralized logging
- Distributed tracing
- External secret management
- API rate limiting
- Automated integration testing

---

## Project Status

This project serves as a practical implementation of a containerized, Kubernetes-based microservices architecture and demonstrates the deployment of a full-stack application using modern cloud-native technologies.

---

## Author

**Yash Gadodia**

Cloud & DevOps Engineer focused on cloud infrastructure, Kubernetes, Infrastructure as Code, CI/CD, automation, and cloud-native platforms.

[LinkedIn](https://www.linkedin.com/in/yash-gadodia) · [GitHub](https://github.com/yashgadodia12)
