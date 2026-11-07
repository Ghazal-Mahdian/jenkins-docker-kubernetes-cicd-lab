# Implementation

The Jenkinsfile, Dockerfile, Helm chart, and Kubernetes manifests are in the companion repository [cicd-hello-world](https://github.com/Ghazal-Mahdian/cicd-hello-world); none are copied or recreated here. This page records only what the screenshots show.

## Pipeline stages (Jenkins build #42)

Source: `assets/screenshots/jenkins-pipeline-build-42-stage-graph.png`

| # | Stage | Result | Duration |
|---|---|---|---|
| 1 | Checkout SCM | Passed | 0.91 s |
| 2 | Checkout Code | Passed | 0.7 s |
| 3 | Build (Java 17) | Passed | 5 s |
| 4 | Run Unit Tests (Java 11) | Passed | 5 s |
| 5 | Code Analysis (Java 8) | Passed | 20 s |
| 6 | Build Docker Image | Passed | 3 s |
| 7 | Security Scan (Trivy) | Passed | 2 min 11 s |
| 8 | Push to Docker Hub | Passed | not visible |
| 9 | Deploy with Helm | Passed | not visible |
| 10 | Check Pods | Passed | not visible |
| 11 | Post Actions | Passed ("Pipeline completed successfully!") | 0.11 s |

Build metadata: started by Ghazal Mahdian, queued 56 ms, total 3 min 5 s, captured "23 hr" after the start.

## Application source (transcription)

Source: `assets/screenshots/vscode-project-structure-and-app-java.png`. **Transcribed from a screenshot**, not copied from the original file. All 16 lines are legible.

```java
// src/main/java/com/example/App.java — transcription from screenshot
package com.example;

public class App {
    public static void main(String[] args) throws InterruptedException {
        System.out.println("Hello World from CI/CD Pipeline!");

        while (true) {
            Thread.sleep(10000);
        }
    }

    public static String getMessage() {
        return "Hello World from CI/CD Pipeline!";
    }
}
```

The program prints one line and then sleeps forever. `getMessage()` is presumably what `AppTest.java` tests, but the test file's content is not visible. The long-running loop keeps a container alive in Kubernetes, but it exposes no port or health endpoint.

## Developer workflow (from the VS Code terminal)

Source: same screenshot. Prompt and host name redacted.

- Two `git push origin main` operations; the second pushes commit `fe20291` "update jenkins" (1 file changed, 9 insertions, 9 deletions). This matches the latest commit on the GitHub page.
- `helm repo add prometheus-community https://prometheus-community.github.io/helm-charts` fails with `zsh: command not found: helm` on the local machine.

## SonarQube result

Source: `assets/screenshots/sonarqube-hello-world-quality-gate-failed.png`

| Metric | Value |
|---|---|
| Quality gate | Failed |
| Bugs | 1 (Reliability E) |
| Vulnerabilities | 0 (Security A) |
| Security hotspots reviewed | – (A) |
| Code smells | 2 (Maintainability A) |
| Coverage | 0.0% |
| Duplications | 0.0% |
| Lines | 57 (XML, Java) |

The bug and code-smell details are not shown. SonarQube also warns that it is using its embedded database, which is for evaluation only.

## Argo CD application

Source: `assets/screenshots/argocd-hello-world-app-synced-degraded.png`

| Field | Value |
|---|---|
| Name | hello-world-app |
| Project | default |
| Status | Degraded, Synced |
| Repository | https://github.com/Ghazal-Mahdian/cicd-hello-world |
| Target revision | HEAD |
| Path | helm/hello-world |
| Destination / namespace | in-cluster / default |
| Created / last sync | 27 March 2026 21:06:51 / 21:06:57 |

## Grafana

Source: `assets/screenshots/grafana-kubernetes-dashboard-list.png`. A dashboard search for "pod" returns seven dashboards tagged `kubernetes-mixin`: four "Kubernetes / Compute Resources / …", "Kubernetes / Kubelet", "Kubernetes / Networking / Namespace…", and "Kubernetes / Networking / Pod". No dashboard contents were captured.
