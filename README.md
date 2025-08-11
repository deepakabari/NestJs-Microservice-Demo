# NestJS Microservices (TCP)

A documentation for a NestJS microservices architecture using the TCP transporter. The project contains the following services:

- **api-gateway** — API gateway (HTTP) that routes requests to microservices
- **auth-service** — Registration and Login
- **user-service** — User management (CRUD)
- **product-service** — Product management (CRUD)
- **shared-lib** — Common implementation

Common features implemented across services:

- JWT-based authentication
- Guards (AuthGuard, RolesGuard)
- Role-based access control (RBAC)
- Global filters (exception filters) and interceptors (logging, transform)
- Validation pipes and DTOs
- TCP transporter for microservice communication

---

## Table of contents

- [Overview](#-Overview)
- [Repo structure](#-repo-structure)
- [Prerequisites](#-prerequisites)
- [Installation](#-installation)
- [Environment variables](#-environment-variables)
- [Running the services](#-running-the-services)
- [Notes & best practices](#-notes-and-best-practices)

---

## Overview

This template demonstrates a simple microservice setup using NestJS with TCP transporter, plus an HTTP API gateway. The gateway handles incoming HTTP requests and forwards them over TCP to microservices (auth, user, product). RBAC is enforced using guards that check roles embedded in JWT claims.

## 2 — Repo structure

```
repo-root/
├─ api-gateway/
│  ├─ src/
│  └─ package.json
├─ auth-service/
│  ├─ src/
│  └─ package.json
├─ user-service/
│  ├─ src/
│  └─ package.json
├─ product-service/
│  ├─ src/
│  └─ package.json
├─ shared-lib/
│  ├─ src/
│  └─ package.json
│  README.md
└─ package.json
```

Each service is a standalone NestJS app. Shared DTOs, interfaces, and constants can be placed in a `@nestjs/shared-lib` package.

## 3 — Prerequisites

- Node.js v18+ (or compatible)
- npm or yarn

## 4 — Installation

For each service folder:

```bash
cd api-gateway
npm install

cd ../auth-service
npm install

cd ../user-service
npm install

cd ../product-service
npm install

cd ../shared-lib
npm install
```

## 5 — Environment variables (example)

Create a `.env` in each service with service-specific settings.

**api-gateway/.env**

```
JWT_SECRET=your_jwt_secret_here
```

**auth-service/.env**

```
JWT_SECRET=your_jwt_secret_here
DB_HOST=
DB_PORT=
DB_USERNAME=
DB_PASSWORD=
DB_NAME=
```

**user-service/.env**

```
JWT_SECRET=your_jwt_secret_here
DB_HOST=
DB_PORT=
DB_USERNAME=
DB_PASSWORD=
DB_NAME=
```

**product-service/.env**

```
JWT_SECRET=your_jwt_secret_here
DB_HOST=
DB_PORT=
DB_USERNAME=
DB_PASSWORD=
DB_NAME=
```

> Use strong secrets in production and store them in a secrets manager.

## 6 — Running the services (local)

You can start services individually or run all at once.

**Option 1: Start each microservice manually**

```bash
# auth-service
cd auth-service && npm run start:dev -- --port=4001

# user-service
cd user-service && npm run start:dev -- --port=4002

# product-service
cd product-service && npm run start:dev -- --port=4003

# api-gateway
cd api-gateway && npm run start:dev -- --port=3000
```

**Option 2: Start all services concurrently from root**

```bash
npm run start:all
```

Each microservice should create a TCP listener using the NestJS `MicroserviceOptions` with `Transport.TCP`.

## 7 — Notes & best practices

- **Shared contracts:** Keep DTOs and message patterns in a shared library to avoid mismatch across services.
- **Timeouts & retries:** Use timeouts for client calls and handle retries/backoff when appropriate.
- **Logging & tracing:** Implement structured logging and distributed tracing for observability.
- **Security:** Keep JWT secrets and other sensitive data in environment variables / secret managers. Use HTTPS for gateway in production.

---

### Example: Auth login flow (HTTP -> gateway -> auth service over TCP)

1. Client POST `/auth/login` to gateway with `{ email, password }`.
2. Gateway forwards to `authClient.send({ cmd: 'login' }, { email, password })`.
3. Auth service validates credentials and returns `{ accessToken }`.
4. Gateway returns HTTP 200 with token to client.

---
