# Build → Test → Deploy Notes

Comprehensive quick-reference for production-ready build/test/run patterns and Dockerfiles for common stacks in this order:
1. Java / Spring Boot
2. Node.js (TypeScript) / React / Angular
3. Python (Flask / FastAPI / Django)
4. .NET (ASP.NET Core)

---

# Quick common pipeline (applies to all stacks)
1. **Build** — compile/transpile and produce an artifact (jar/war, built JS, wheel, published output).
2. **Test** — run unit tests, integration tests, and (optionally) E2E tests in separate CI stages.
3. **Package** — produce runtime artifact. Container images are the de-facto standard for cloud/K8s.
4. **Deploy** — push image to registry and deploy to environment (VM, PaaS, or Kubernetes).
5. **Run & Operate** — liveness/readiness probes, logging to stdout, metrics, tracing, secrets.

> Tip: always produce a versioned artifact (semver or CI-SHA). Avoid `latest` in production.

---

# 1. Java / Spring Boot

## Overview
- Typical artifacts: `jar` (fat jar / executable jar with embedded Tomcat) or `war` (for external servlet container).
- Common production practice: build a runnable JAR (embedded server) and run `java -jar` or use container image with JRE.

## Build tools (alternatives)
- **Maven** (`mvn`) — dependency + lifecycle management.
- **Gradle** (`./gradlew`) — faster, scriptable build logic.
- **Ant** — older, more manual build tool; can be used with Ivy for deps.

## Test tools
- **JUnit 5** — unit testing
- **Mockito** — mocking
- **Spring Test / SpringBootTest** — integration tests
- **Testcontainers** — run real infra (DB, Kafka) in tests
- **JaCoCo** for coverage

## Packaging / runtime
- **Embedded server** (Spring Boot): produce executable JAR and run `java -jar app.jar` — most common in containers.
- **External Tomcat**: produce a `war` and deploy into `tomcat:9-jdk11` image.

## Example Dockerfile (Spring Boot, fat JAR)
```dockerfile
FROM eclipse-temurin:17-jre-alpine
WORKDIR /app
COPY target/myapp.jar /app/myapp.jar
EXPOSE 8080
ENTRYPOINT ["java", "-jar", "/app/myapp.jar"]
```

## Example Dockerfile (WAR → Tomcat)
```dockerfile
FROM tomcat:9.0-jdk11-openjdk
COPY target/myapp.war /usr/local/tomcat/webapps/ROOT.war
# Tomcat exposes 8080
```

## Kubernetes snippet (Deployment + Service)
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: springboot-app
spec:
  replicas: 3
  selector:
    matchLabels:
      app: springboot-app
  template:
    metadata:
      labels: { app: springboot-app }
    spec:
      containers:
      - name: app
        image: myrepo/springboot-app:1.2.3
        ports:
        - containerPort: 8080
        readinessProbe:
          httpGet: { path: /actuator/health/readiness, port: 8080 }
          initialDelaySeconds: 10
          periodSeconds: 10
        livenessProbe:
          httpGet: { path: /actuator/health/liveness, port: 8080 }
          initialDelaySeconds: 30
          periodSeconds: 20
---
apiVersion: v1
kind: Service
metadata:
  name: springboot-svc
spec:
  type: ClusterIP
  selector: { app: springboot-app }
  ports: [{ port: 80, targetPort: 8080 }]
```

## CI tips
- Run `mvn -DskipTests=false clean package` in build stage; run tests in a separate stage.
- Use Testcontainers for integration tests that require DBs.
- Push image to registry (ECR/GCR/Harbor) with immutable tags.

---

# 2. Node.js (TypeScript) / React / Angular

This section covers both backend Node.js with TypeScript and frontend frameworks (React / Angular).

## Node.js (TypeScript) backend

### Build tools / package managers (alternatives)
- **npm** (`npm ci`, `npm run build`) — default
- **yarn** (classic) — `yarn install --frozen-lockfile`
- **pnpm** — fast, efficient, `pnpm install --frozen-lockfile`

### Transpilers / bundlers
- **TypeScript**: `tsc` (compile to `dist/`)
- Bundlers (if needed): **esbuild**, **webpack**, **rollup**

### Test tools
- **Jest** — widely used unit/integration test runner
- **Mocha + Chai** — alternative
- **Supertest** — HTTP integration testing
- **ts-jest** or `@types/jest` for TS support

### Runtime / servers
- Run compiled JS: `node dist/index.js`
- For production process management (not necessary inside K8s): `pm2` (but prefer single process in container)

### Example multi-stage Dockerfile (Node + TypeScript)
```dockerfile
# Builder
FROM node:20-alpine AS builder
WORKDIR /app
COPY package.json pnpm-lock.yaml ./
RUN npm install -g pnpm && pnpm install --frozen-lockfile
COPY . .
RUN pnpm build

# Runtime
FROM node:20-alpine
WORKDIR /app
ENV NODE_ENV=production
COPY --from=builder /app/dist ./dist
COPY package.json package-lock.json ./
RUN npm ci --only=production
EXPOSE 3000
CMD ["node","dist/index.js"]
```

### Kubernetes notes
- Expose port your app listens on (3000 typical)
- Add readiness/liveness endpoints (e.g., `/healthz`)

---

## React / Angular (frontend)

### Build tools / package managers
- **npm / pnpm / yarn** to install and run `build` script
- Builders: **Create React App**, **Vite** (recommended for modern apps), **Angular CLI** for Angular

### Test tools
- **Jest** + React Testing Library (React)
- **Karma + Jasmine** or **Jest** (Angular supports Jest too)
- E2E: **Cypress**, **Playwright**, **Selenium**

### Serving production build
- **Static hosting** (recommended): S3 + CloudFront, Netlify, Vercel
- **Nginx**: for custom headers, rewrites, or when colocating with other services

### Example Dockerfile (React built + nginx)
```dockerfile
# build
FROM node:20-alpine AS build
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build

# serve
FROM nginx:stable-alpine
COPY --from=build /app/build /usr/share/nginx/html
# copy custom nginx.conf if needed for SPA fallback
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

### Kubernetes notes
- React/nginx image runs like any service. For static sites, prefer CDN for performance.
- Ingress + TLS termination for domain + caching headers.

---

# 3. Python (Flask / FastAPI / Django)

## Packaging / env managers (alternatives)
- **pip + venv** (standard)
- **poetry** (dependency and packaging, recommended)
- **pipenv** (alternative)

## Test tools
- **pytest** (recommended)
- **unittest** (builtin)
- **pytest-asyncio** (for async code)

## Servers (production)
- **Flask / Django (WSGI)**: use **Gunicorn** or **uWSGI** as the process manager (Gunicorn common).
- **FastAPI (ASGI)**: use **Uvicorn** (or Gunicorn + Uvicorn workers) or **Hypercorn**.

## Example Dockerfile (FastAPI — recommended style)
```dockerfile
FROM python:3.11-slim
WORKDIR /app
# install system deps (if any)
RUN apt-get update && apt-get install -y build-essential && rm -rf /var/lib/apt/lists/*
COPY pyproject.toml poetry.lock ./
RUN pip install poetry && poetry config virtualenvs.create false && poetry install --no-dev
COPY . .
EXPOSE 8000
CMD ["gunicorn","-k","uvicorn.workers.UvicornWorker","main:app","-b","0.0.0.0:8000","-w","4"]
```

## Example Dockerfile (Flask basic with Gunicorn)
```dockerfile
FROM python:3.11-slim
WORKDIR /app
COPY requirements.txt ./
RUN pip install -r requirements.txt
COPY . .
EXPOSE 8000
CMD ["gunicorn","-w","4","-b","0.0.0.0:8000","myapp:app"]
```

## Example Dockerfile (Django)
```dockerfile
FROM python:3.11-slim
WORKDIR /app
COPY requirements.txt ./
RUN pip install -r requirements.txt
COPY . .
ENV DJANGO_SETTINGS_MODULE=myproject.settings
RUN python manage.py collectstatic --noinput
EXPOSE 8000
CMD ["gunicorn","myproject.wsgi:application","--bind","0.0.0.0:8000","--workers","4"]
```

## Kubernetes notes
- Use `ConfigMap` for non-sensitive settings and `Secret` for credentials (DB passwords, API keys).
- Add readiness probe pointing to a simple endpoint (`/health` or `/ready`).
- Use Horizontal Pod Autoscaler (HPA) for scaling based on CPU or custom metrics.

---

# 4. .NET (ASP.NET Core)

## Build / tooling (alternatives)
- **dotnet CLI** (`dotnet build`, `dotnet test`, `dotnet publish`) — default
- CI systems may call `dotnet restore` then `dotnet publish -c Release -o out`

## Test tools
- **xUnit.net** — common
- **NUnit** — alternative
- **MSTest** — built-in option

## Runtime
- **Kestrel** is the built-in cross-platform web server. In production you can place a reverse proxy in front of Kestrel (Nginx/Apache/IIS) — in containers, usually expose Kestrel directly.

## Example multi-stage Dockerfile
```dockerfile
# Build stage
FROM mcr.microsoft.com/dotnet/sdk:8.0 AS build
WORKDIR /src
COPY *.csproj ./
RUN dotnet restore
COPY . .
RUN dotnet publish -c Release -o /app/publish

# Runtime
FROM mcr.microsoft.com/dotnet/aspnet:8.0
WORKDIR /app
COPY --from=build /app/publish .
EXPOSE 80
ENTRYPOINT ["dotnet", "MyApp.dll"]
```

## Kubernetes notes
- Expose port 80 (or configured port) and use readiness probes. Kestrel supports graceful shutdown.
- Consider configuring `ASPNETCORE_ENVIRONMENT` and `ASPNETCORE_URLS` via env vars.

---

# Cross-cutting production best practices
- **Health checks**: Provide `/health/liveness` and `/health/readiness` endpoints.
- **Logging**: write to stdout/stderr; centralize logs (ELK/Fluentd/Loki).
- **Metrics & Tracing**: expose Prometheus metrics and use OpenTelemetry for tracing.
- **Secrets**: don't store in images. Use Vault, AWS Secrets Manager, or Kubernetes External Secrets.
- **Immutability**: treat images as immutable; deploy by replacing images not by changing containers.
- **Non-root**: run app as non-root user in the container where possible.
- **Resource requests/limits**: set them in K8s to avoid noisy-neighbor problems.
- **CI**: run linting, unit tests, integration tests (with Testcontainers or ephemeral infra), build image, run smoke tests in staging, then deploy to prod with a controlled release strategy (canary/blue-green/rolling).

---

# Generic Kubernetes Deployment (template)
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: app-name
spec:
  replicas: 2
  selector:
    matchLabels: { app: app-name }
  template:
    metadata:
      labels: { app: app-name }
    spec:
      containers:
      - name: app
        image: <registry>/app-name:1.0.0
        ports:
        - containerPort: 8080 # change per app
        env:
        - name: ENV
          value: production
        readinessProbe:
          httpGet: { path: /health/ready, port: 8080 }
          initialDelaySeconds: 10
        livenessProbe:
          httpGet: { path: /health, port: 8080 }
          initialDelaySeconds: 30
        resources:
          requests: { cpu: "250m", memory: "256Mi" }
          limits: { cpu: "1000m", memory: "1Gi" }
```

---

# Cheat-sheet: commands by stack
- **Java**: `mvn test`, `mvn -DskipTests=false clean package`, `java -jar target/app.jar`
- **Node (TS)**: `pnpm install`, `pnpm build`, `pnpm test`, `node dist/index.js`
- **React**: `npm run build` → serve build with CDN or nginx
- **Python**: `pytest`, `gunicorn -w 4 -b 0.0.0.0:8000 myapp:app`, or `uvicorn main:app --workers 4`
- **.NET**: `dotnet test`, `dotnet publish -c Release -o out`, `dotnet out/MyApp.dll`

---

# Next steps (optional extras you can ask me for)
- Full GitHub Actions or Jenkins CI pipeline for any one stack above.
- Full K8s manifests (Deployment, Service, Ingress, HPA) for a chosen stack.
- Example `nginx.conf` for SPA routing + caching.
- Security hardening checklist for container images.

---

*End of notes.*

