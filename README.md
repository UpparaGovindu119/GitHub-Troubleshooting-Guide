# GitHub-Troubleshooting-Guide

Troubleshooting references, tool-wise guides, Kubernetes YAML examples, logs, interview preparation, and hands-on DevOps labs.

## Start here

1. [Tool-wise troubleshooting](tool-wise-troubleshooting.md) — symptoms, causes, fixes, and verification.
2. [Interview-ready troubleshooting script](INTERVIEW-READY.md) — explain a systematic debugging approach.
3. [Tool-wise interview quick sheet](TOOL-WISE-INTERVIEW-QUICK-SHEET.md) — fast revision across DevOps tools.
4. [Official documentation practical labs](OFFICIAL-DOCS-PRACTICAL-TROUBLESHOOTING-LABS.md) — Terraform state/import, Kubernetes failures, GitHub Actions, and AWS VPC route diagnosis.
5. [Expanded DevOps interview scenario bank](EXPANDED-DEVOPS-INTERVIEW-SCENARIO-BANK.md) — common cross-tool failures and evidence-based diagnosis.
6. [Kubernetes YAML types](KUBERNETES-ALL-YAML-TYPES.md) — resource configuration examples.
7. [Log files reference](LOG-FILES-REFERENCE.md) — where to look when debugging.
8. [General troubleshooting guide](troubleshooting-guide.md) — structured diagnosis.
9. [Logs debugging](logs-debugging.md) — log-based troubleshooting.

## Official documentation

- [Terraform tutorials](https://developer.hashicorp.com/terraform/tutorials)
- [Kubernetes debugging tasks](https://kubernetes.io/docs/tasks/debug/)
- [GitHub Actions workflow troubleshooting](https://docs.github.com/en/actions/how-tos/troubleshoot-workflows)
- [AWS VPC route tables](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Route_Tables.html)

## Standard debugging sequence

**Exact symptom → logs/events → inspect current state → evidence-based root cause → smallest safe fix → verification → prevention.**

Practise in a disposable lab. Do not expose secrets in logs or commit them to the repository.
