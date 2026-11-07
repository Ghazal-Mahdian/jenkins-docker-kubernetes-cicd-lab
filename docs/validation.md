# Validation

Status key:

- **Demonstrated**: visible in a supplied screenshot.
- **Partial**: visible only as a passed stage or an install, with no output or result.
- **Failed**: evidence shows the expected outcome did not occur.
- **Not shown**: required or proposed, with no evidence.

No written report was supplied, so there are no "reported only" claims for this project.

| Test | Expected outcome | Observed outcome | Evidence | Status |
|---|---|---|---|---|
| Pipeline runs end to end | All stages succeed | Build #42: 11/11 stages green, 3 min 5 s | `jenkins-pipeline-build-42-stage-graph.png` | Demonstrated |
| Build on Java 17 | Compiles | Stage passed (5 s) | same | Partial (no log) |
| Unit tests on Java 11 | Tests pass | Stage passed (5 s); test count not shown | same | Partial |
| SonarQube analysis on Java 8 | Project analyzed | `hello-world` analyzed; 57 lines | `sonarqube-hello-world-quality-gate-failed.png` | Demonstrated |
| Code meets quality gate | Gate passes | Gate **Failed**: 1 bug (E), 0.0% coverage | same | Failed |
| Failed gate stops pipeline | Pipeline aborts | Build #42 still succeeded. The timing of the screenshots suggests the analysis and the build are from the same run (both about 23 h old), but this is not confirmed | both screenshots | Not demonstrated |
| Docker image built | Image created | Stage passed (3 s) | Jenkins stage graph | Partial |
| Image scanned with Trivy | Vulnerability report | Stage passed (2 min 11 s); findings and severity threshold not shown | same | Partial |
| Image pushed to Docker Hub | Tag in registry | Stage passed; registry not shown | same | Partial |
| Deploy to Kubernetes | Pods running | `Deploy with Helm` and `Check Pods` passed; no `kubectl` output | same | Partial |
| GitOps sync | App Synced and Healthy | Synced, **Degraded** | `argocd-hello-world-app-synced-degraded.png` | Failed (health) |
| Monitoring | Dashboards with cluster metrics | Kubernetes dashboards installed; no panels or data shown | `grafana-kubernetes-dashboard-list.png` | Partial |
| Source under version control | Repo with pipeline-as-code | Public repo with Jenkinsfile, Dockerfile, Helm chart, 36 commits | `github-cicd-hello-world-repository-files.png` | Demonstrated |
| Jenkins/SonarQube on a custom Docker network | Containers on `cicd-network` | Not shown | — | Not shown |
| Three Java environment containers | 17/11/8 containers | Stage names only | — | Not shown |
| Kubernetes cluster (Minikube/k3s) ready | `kubectl get nodes` Ready | Not shown | — | Not shown |

## Timing note

The Jenkins and SonarQube screenshots both say the work happened about 23 hours earlier (around 22:00 on 26 March 2026). The Argo CD application was created at 21:06 on 27 March, about 26 minutes before its screenshot. Build #42's "Check Pods" stage and the Argo CD Degraded status therefore describe the cluster at different times and do not contradict each other.
