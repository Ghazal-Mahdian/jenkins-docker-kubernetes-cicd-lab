# Jenkins, Docker, and Kubernetes CI/CD Lab

A CI/CD pipeline for a small Java application, built for a graduate DevOps course midterm (CYB535). Jenkins builds, tests, and analyzes the code across three Java versions, builds a Docker image, scans it with Trivy, pushes it to a registry, and deploys it to Kubernetes with Helm. Argo CD and Grafana were added alongside. The assignment listed these as "real industry improvements" beyond the core task.

This repository is a **screenshot-based case study** of six screenshots from 27 March 2026. The pipeline code, Dockerfile, Helm chart, and application source are in the companion repository **[Ghazal-Mahdian/cicd-hello-world](https://github.com/Ghazal-Mahdian/cicd-hello-world)** (see [Source code](#source-code)).

## At a glance

| | |
|---|---|
| Context | CYB535 midterm: CI/CD with Jenkins, SonarQube, Docker, Kubernetes |
| Strongest evidence | Jenkins build #42: all 11 stages green, 3 min 5 s |
| Quality result | SonarQube quality gate **Failed** (1 bug, 0% coverage) while the pipeline still passed |
| Deployment state | Argo CD shows the app **Synced but Degraded** |
| Source | [cicd-hello-world](https://github.com/Ghazal-Mahdian/cicd-hello-world) (Jenkinsfile, Dockerfile, Helm chart, Java app) |
| Missing | Stage logs, Trivy findings, Docker Hub image, `kubectl` output, written report |

## Business problem and lab context

The assignment asked for a pipeline that checks out Java code from GitHub, compiles it with Java 17, tests it with Java 11, analyzes it with SonarQube on Java 8, builds and pushes a Docker image, and deploys it to Kubernetes (Minikube or k3s). It also asked for Jenkins and SonarQube in Docker containers on a shared network. It closed with suggested extensions: Helm, Jenkins agents, Argo CD (GitOps), Trivy, and Prometheus/Grafana.

## My contribution

The screenshots show:

- A Jenkins pipeline job (`pipeline-CI-CD`). Build #42 was "Started by Ghazal Mahdian".
- A public GitHub repository, `Ghazal-Mahdian/cicd-hello-world`, with 36 commits and a single listed contributor. It contains a Jenkinsfile, Dockerfile, Kubernetes and Helm manifests, a Maven `pom.xml`, and Java source and test files.
- A local VS Code workspace for the same project, with pushes to that repository.
- SonarQube, Argo CD, and Grafana instances running locally.

This was individual work. No written report was produced for this project.

## Tools and technologies evidenced

| Tool | Evidence | Detail visible |
|---|---|---|
| Jenkins | Build #42 stage view (`localhost:8080`) | 11 stages incl. Java 17/11/8 stages, Trivy, Docker Hub push, Helm deploy |
| SonarQube | Projects page (`localhost:9000`) | Community Edition v9.9.8 (build 100196), embedded database |
| Docker | Stage "Build Docker Image"; `Dockerfile` in repo | Image name and registry path not visible |
| Trivy | Stage "Security Scan (Trivy)", 2 min 11 s | Findings not visible |
| Helm | Stage "Deploy with Helm"; `helm/hello-world` chart | `Chart.yaml`, `values.yaml`, `templates/deployment.yaml` |
| Kubernetes | Stage "Check Pods"; Argo CD destination `in-cluster` | Distribution (Minikube/k3s) not shown |
| Argo CD | Applications page (`localhost:9090`) | v3.3.6; app tracks `helm/hello-world` at `HEAD` |
| Grafana | Dashboard list (`localhost:3001`) | Kubernetes dashboards tagged `kubernetes-mixin`; no metrics panel captured |
| Java / Maven | `App.java`, `AppTest.java`, `pom.xml` | Java versions per stage names only |
| GitHub | Repository page | 36 commits, `main` branch |

Prometheus is not shown directly. Jenkins agents are not evidenced.

## Architecture overview

```
 Developer (VS Code) ── git push ──► GitHub: cicd-hello-world (main)
                                          │                    ▲
                                          ▼                    │ watches helm/hello-world @ HEAD
 Jenkins pipeline-CI-CD (#42, 3m05s)                           │
   Checkout SCM → Checkout Code → Build (Java 17) → Unit Tests (Java 11)
   → Code Analysis (Java 8) ──► SonarQube (quality gate: Failed)
   → Build Docker Image → Security Scan (Trivy) → Push to Docker Hub
   → Deploy with Helm ──► Kubernetes cluster ◄── Argo CD (Synced / Degraded)
   → Check Pods → Post Actions                     │
                                                   └──► Grafana (Kubernetes dashboards)
```

How Helm-from-Jenkins and Argo CD divide responsibility for the same chart is not documented. Both appear to target the cluster (see [docs/architecture.md](docs/architecture.md)).

![Jenkins build 42 stage graph](assets/screenshots/jenkins-pipeline-build-42-stage-graph.png)
*Jenkins build #42 of `pipeline-CI-CD`: every stage from Checkout SCM to Post Actions passed, total 3 min 5 s. Visible stage times: Build (Java 17) 5 s, Unit Tests (Java 11) 5 s, Code Analysis (Java 8) 20 s, Docker build 3 s, Trivy 2 min 11 s.*

## Implementation highlights

- **Multi-JDK stages.** Build, test, and analysis stages are named for Java 17, 11, and 8, following the assignment's three-environment design. How each JDK was provided (containers, tool installers, or agents) is not visible.
- **Security in the pipeline.** A Trivy stage runs after the image build and before the push. It is the longest stage at 2 min 11 s.
- **GitOps alongside push-based deploy.** Argo CD tracks the Helm chart path in the same repository, and Jenkins also has a Helm deploy stage.
- **Observability.** Grafana is running with Kubernetes compute-resource, kubelet, and networking dashboards installed.

![SonarQube project hello-world quality gate failed](assets/screenshots/sonarqube-hello-world-quality-gate-failed.png)
*SonarQube 9.9.8: project `hello-world` fails its quality gate with 1 bug (E rating), 0 vulnerabilities, 2 code smells, 0.0% coverage, 57 lines (XML, Java).*

![Argo CD hello-world-app synced but degraded](assets/screenshots/argocd-hello-world-app-synced-degraded.png)
*Argo CD v3.3.6: `hello-world-app` is Synced to `HEAD` of `helm/hello-world` but its health is Degraded. Created and last synced 27 March 2026 21:06.*

![GitHub repository files](assets/screenshots/github-cicd-hello-world-repository-files.png)
*The source repository at capture time: `helm/`, `src/`, `target/`, `.DS_Store`, `Dockerfile`, `Jenkinsfile`, `application.yaml`, `deployment.yaml`, `pom.xml`; 36 commits.*

## Validation summary

| Area | Status |
|---|---|
| Pipeline runs end to end | Demonstrated (build #42) |
| SonarQube analysis | Demonstrated, quality gate failed |
| Quality gate blocks the pipeline | Not demonstrated; pipeline passed despite the failed gate |
| Trivy scan | Stage passed; findings not shown |
| Image pushed to Docker Hub | Stage passed; registry not shown |
| Deployed workload healthy | Not demonstrated; Argo CD reports Degraded |
| Monitoring | Dashboards installed; no data shown |

Details: [docs/validation.md](docs/validation.md).

## Source code

The application, Jenkinsfile, Dockerfile, Helm chart, and Kubernetes manifests are in the public repository **[github.com/Ghazal-Mahdian/cicd-hello-world](https://github.com/Ghazal-Mahdian/cicd-hello-world)**. That repository is the source of truth for the pipeline. This one documents the run results and the surrounding tooling. The source files were not reviewed while this case study was being prepared, so details of stage logic below are still described from stage names.

The only code shown in the screenshots is `App.java`, transcribed in [docs/implementation.md](docs/implementation.md).

## Limitations and missing artifacts

- Pipeline and deployment files live in the companion repository, not here. Statements in this repository about stage logic are based on stage names and screenshots.
- Build #42's console log, Trivy report, Docker Hub tag, and `kubectl get pods` output are not available.
- The cause of the Argo CD Degraded status is unknown.
- The Grafana screenshot shows dashboard names only, not metrics.
- The VS Code terminal shows `helm: command not found` when adding the Prometheus community Helm repo locally. How Prometheus and Grafana were installed is not documented.
- The SonarQube default `admin/admin` login is part of the assignment instructions. Whether it was changed is not shown.
- Software versions are recorded only where a screenshot shows them (SonarQube 9.9.8, Argo CD 3.3.6).

## Lessons learned

No written reflection was supplied for this project.

**Suggested reflections — for your review before publishing:**
- A failed SonarQube quality gate did not stop the build. Adding a `waitForQualityGate abortPipeline: true` step (or equivalent) would make code quality a release gate rather than a report.
- 0.0% coverage alongside a passing unit-test stage suggests no coverage report was sent to SonarQube; a JaCoCo report would close that gap.
- Deploying the same chart from both Jenkins (Helm) and Argo CD can cause the two tools to fight over cluster state; choosing one deployment path, or having Jenkins update Git and letting Argo CD deploy, is the usual GitOps pattern.
- The application is a loop that prints and sleeps. It has no HTTP endpoint, which limits what health checks and monitoring can show; it may also be related to the Degraded status.
- `target/` and `.DS_Store` were committed to the source repository; a `.gitignore` would keep build output and OS files out.

## Reviewing this repository

1. Read [docs/validation.md](docs/validation.md) for what each screenshot does and does not show.
2. [docs/architecture.md](docs/architecture.md) compares the assignment design with the evidenced pipeline.
3. [docs/evidence-index.md](docs/evidence-index.md) maps each claim to a screenshot.

There is nothing to run in this repository. To see the pipeline definition, open the [Jenkinsfile in cicd-hello-world](https://github.com/Ghazal-Mahdian/cicd-hello-world). Reproducing the run also requires local Jenkins, SonarQube, Docker, Kubernetes, and Docker Hub credentials, which are not documented here.

## Use and licensing

No license is applied. The assignment belongs to the course instructor; screenshots are shared for portfolio review only.
