# GitHub-Troubleshooting-Guide

Troubleshooting references, tool-wise guides, Kubernetes YAML examples, logs, interview preparation, and hands-on DevOps labs.

## Start here

1. [**All scenarios coverage matrix — master checklist**](ALL-SCENARIOS-COVERAGE-MATRIX.md) — one clear index across Linux, Git, Docker, Kubernetes, Terraform, AWS/VPC/IAM, GitHub Actions, Jenkins, monitoring/SRE, security, end-to-end project failures, and interview answers.
2. [DevOps job-preparation roadmap](DEVOPS-JOB-PREPARATION-ROADMAP.md) — focused order for skills, projects, interviews, resume and applications.
3. [Tool-wise troubleshooting](tool-wise-troubleshooting.md) — symptoms, causes, fixes, and verification.
4. [Interview-ready troubleshooting script](INTERVIEW-READY.md) — explain a systematic debugging approach.
5. [Tool-wise interview quick sheet](TOOL-WISE-INTERVIEW-QUICK-SHEET.md) — fast revision across DevOps tools.
6. [Official documentation practical labs](OFFICIAL-DOCS-PRACTICAL-TROUBLESHOOTING-LABS.md) — Terraform state/import, Kubernetes failures, GitHub Actions, and AWS VPC route diagnosis.
7. [Expanded DevOps interview scenario bank](EXPANDED-DEVOPS-INTERVIEW-SCENARIO-BANK.md) — common cross-tool failures and evidence-based diagnosis.
8. [GitHub Actions troubleshooting scenarios](GITHUB-ACTIONS-TROUBLESHOOTING-SCENARIOS.md) — workflow triggers, YAML, permissions, secrets, artifacts, Docker builds, runners, and deployment health checks.
9. [Kubernetes YAML types](KUBERNETES-ALL-YAML-TYPES.md) — resource configuration examples.
10. [Log files reference](LOG-FILES-REFERENCE.md) — where to look when debugging.
11. [General troubleshooting guide](troubleshooting-guide.md) — structured diagnosis.
12. [Logs debugging](logs-debugging.md) — log-based troubleshooting.

## How to use the master checklist

Open **ALL-SCENARIOS-COVERAGE-MATRIX.md** first. For each scenario, practise: **symptom → evidence → root cause → safe fix → verification → prevention**. Mark your own readiness only after you can demonstrate and explain it without copying.

The matrix covers common and important scenarios; it is not a promise that every possible production failure is documented or that reading alone proves practical skill.

## Official documentation

- [Terraform tutorials](https://developer.hashicorp.com/terraform/tutorials)
- [Kubernetes debugging tasks](https://kubernetes.io/docs/tasks/debug/)
- [GitHub Actions workflow troubleshooting](https://docs.github.com/en/actions/how-tos/troubleshoot-workflows)
- [AWS VPC route tables](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Route_Tables.html)

## Standard debugging sequence

**Exact symptom → logs/events → inspect current state → evidence-based root cause → smallest safe fix → verification → prevention.**

Practise in a disposable lab. Do not expose secrets in logs or commit them to the repository.
