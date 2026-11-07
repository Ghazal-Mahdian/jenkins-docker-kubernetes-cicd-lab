# Architecture

## Assignment design vs. evidenced pipeline

| Assignment step | Evidenced | Notes |
|---|---|---|
| Checkout code from GitHub | `Checkout SCM` (0.91 s), `Checkout Code` (0.7 s) | Two checkout stages: the implicit SCM checkout plus an explicit one |
| Build with Java 17 | `Build (Java 17)` (5 s) | |
| Unit tests with Java 11 | `Run Unit Tests (Java 11)` (5 s) | `AppTest.java` exists; test results not shown |
| SonarQube analysis with Java 8 | `Code Analysis (Java 8)` (20 s); SonarQube project `hello-world` analyzed | Quality gate Failed |
| Build Docker image | `Build Docker Image` (3 s) | |
| — (extension) | `Security Scan (Trivy)` (2 min 11 s) | Assignment listed Trivy as an improvement |
| Push to Docker Hub | `Push to Docker Hub` | Duration and image not visible |
| Deploy to Kubernetes with manifests | `Deploy with Helm`; `deployment.yaml` also at repo root | Assignment used `kubectl apply`; Helm was an improvement |
| Verify pods | `Check Pods` | Output not visible |
| — (extension) | Argo CD `hello-world-app` | GitOps improvement |
| — (extension) | Grafana with Kubernetes dashboards | Monitoring improvement |
| Jenkins and SonarQube in Docker on a shared network | Not shown | Services are reachable on `localhost:8080` and `:9000`; runtime not visible |
| Three Java containers (17/11/8) | Not shown | Stage names match; mechanism unknown |
| Minikube or k3s | Not shown | Argo CD destination is `in-cluster` |

## Components and endpoints (as seen in browser address bars)

| Component | Address | Version |
|---|---|---|
| Jenkins | localhost:8080 | not shown |
| SonarQube | localhost:9000 | 9.9.8 Community (build 100196) |
| Argo CD | localhost:9090 (HTTPS, self-signed: "Not Secure") | v3.3.6 |
| Grafana | localhost:3001 | not shown |

These are local lab services. Port-forwarding or container mappings are not documented.

## Repository layout (from GitHub and VS Code screenshots)

```
cicd-hello-world/
├── helm/hello-world/
│   ├── Chart.yaml
│   ├── values.yaml
│   └── templates/deployment.yaml
├── src/main/java/com/example/App.java
├── src/test/java/com/example/AppTest.java
├── target/                (build output, committed)
├── .DS_Store              (macOS file, committed)
├── application.yaml
├── deployment.yaml
├── Dockerfile
├── Jenkinsfile
└── pom.xml
```

`application.yaml` was committed with the message "create application.yaml" two days before capture, around when Argo CD was introduced. It may be the Argo CD Application manifest, but its contents are not visible.

## Two deployment paths

Jenkins has a `Deploy with Helm` stage, and Argo CD syncs the same `helm/hello-world` path from `HEAD`. If both target the same release and namespace, each sync can overwrite the other. Which tool owned the deployment at capture time is not documented.
