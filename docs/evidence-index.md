# Evidence Index

Source archive: `DevOps535-Midterm.zip` (six screenshots, all 27 March 2026). No report, recording, or source files were supplied. All six screenshots are published; none were duplicates.

| Published file | Original | Shows | Redaction / crop |
|---|---|---|---|
| `jenkins-pipeline-build-42-stage-graph.png` | Screenshot 9.10.43 PM | Jenkins build #42 stages and durations | Browser tabs, bookmarks bar, and profile photo cropped off |
| `grafana-kubernetes-dashboard-list.png` | 9.11.34 PM | Grafana dashboards tagged kubernetes-mixin | Browser chrome cropped; Grafana avatar masked |
| `sonarqube-hello-world-quality-gate-failed.png` | 9.11.45 PM | SonarQube project metrics, version | Browser chrome cropped |
| `github-cicd-hello-world-repository-files.png` | 9.12.04 PM | Repo file list, commits | Browser chrome cropped |
| `vscode-project-structure-and-app-java.png` | 9.12.20 PM | Project tree, App.java, terminal | macOS menu bar cropped; terminal user/host name and push URLs redacted |
| `argocd-hello-world-app-synced-degraded.png` | 9.32.53 PM | Argo CD app status | Browser chrome cropped |

## Claim → source

| Claim | Source |
|---|---|
| 11 stages, 3 min 5 s, started by Ghazal Mahdian | `jenkins-pipeline-build-42-stage-graph.png` |
| Trivy stage 2 min 11 s | same |
| Quality gate failed; 1 bug; 0.0% coverage; SonarQube 9.9.8 | `sonarqube-hello-world-quality-gate-failed.png` |
| Argo CD v3.3.6; Synced/Degraded; path helm/hello-world | `argocd-hello-world-app-synced-degraded.png` |
| Repo files and 36 commits; public | `github-cicd-hello-world-repository-files.png` |
| App.java contents (transcribed) | `vscode-project-structure-and-app-java.png` |
| Local `helm: command not found` | same |
| Kubernetes dashboards in Grafana | `grafana-kubernetes-dashboard-list.png` |

## Names left visible

The GitHub user name `Ghazal-Mahdian` (repository page, Argo CD repo URL) and "Started by Ghazal Mahdian" (Jenkins) are intentionally left visible: this is the author's own portfolio.
