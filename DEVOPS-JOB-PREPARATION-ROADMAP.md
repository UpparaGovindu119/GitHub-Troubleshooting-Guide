# DevOps Job-Preparation Roadmap

This guide turns the repository's troubleshooting references into a focused job-preparation plan. It is a study plan, not a guarantee of employment. Mark a skill ready only after you can explain it and demonstrate it yourself.

## First: avoid studying every file repeatedly

Use these files for their distinct purposes:
- **Learn concepts:** use your main DevOps learning guide and official documentation.
- **Fast revision:** `TOOL-WISE-INTERVIEW-QUICK-SHEET.md`.
- **Debugging method:** `INTERVIEW-READY.md`.
- **Hands-on failures:** `OFFICIAL-DOCS-PRACTICAL-TROUBLESHOOTING-LABS.md`, `GITHUB-ACTIONS-TROUBLESHOOTING-SCENARIOS.md`, and `EXPANDED-DEVOPS-INTERVIEW-SCENARIO-BANK.md`.
- **Look up details:** `tool-wise-troubleshooting.md`, `KUBERNETES-ALL-YAML-TYPES.md`, and the log references.

The general troubleshooting guide and logs guides overlap somewhat with the tool-wise guide. Do not memorise all repeated paragraphs; practise scenarios and use reference files when you need detail.

## Phase 1 — Core foundations

- [ ] Linux: files, permissions, processes, services, CPU/memory/disk, logs, shell commands.
- [ ] Networking: IP/CIDR, DNS, ports, TCP/UDP, HTTP/HTTPS, TLS, routes, NAT, firewalls.
- [ ] Git/GitHub: branching, merge conflicts, pull requests, revert/reset, remote/authentication basics.
- [ ] Bash or Python: variables, conditions, loops, functions, exit codes, simple automation.
- [ ] Explain each concept in your own words and solve small command-line tasks.

## Phase 2 — Core DevOps tools

- [ ] Docker: images, containers, Dockerfiles, ports, networks, volumes, logs and image build failures.
- [ ] CI/CD: GitHub Actions workflow syntax, triggers, jobs/steps, runners, secrets, permissions, artifacts and caching.
- [ ] Kubernetes: Pods, Deployments, Services, ConfigMaps, Secrets, probes, Ingress, storage, RBAC, rollouts and debugging.
- [ ] Terraform: providers, resources, variables, validation, outputs, modules, state, import, drift, plan/apply, lifecycle and safe cleanup.
- [ ] AWS: IAM, EC2, S3, VPC, subnets, route tables, IGW/NAT, security groups/NACLs, load balancing and CloudWatch.
- [ ] Monitoring: metrics, logs, alerts, dashboards and incident response.

## Phase 3 — Build one demonstrable project

Build a small application pipeline in a sandbox:
1. Store source in GitHub with a clear README.
2. Build and run the application using Docker.
3. Add a GitHub Actions workflow for lint/test/build.
4. Provision a small AWS lab environment with Terraform, or use a local/simulated environment when cloud cost is unsuitable.
5. Deploy to a local Kubernetes cluster or a controlled test environment if resources allow.
6. Add health checks, logs, basic monitoring, and a rollback/runbook.
7. Document architecture, commands, decisions, failures, fixes, evidence, cleanup, and estimated cost.

Do not publish cloud credentials, tokens, private keys, kubeconfigs, Terraform state, or other sensitive values. Review costs before provisioning resources and destroy only resources created for the lab.

## Phase 4 — Interview preparation

- [ ] Prepare a truthful 60–90 second introduction tailored to the role.
- [ ] Explain the project architecture and your own contribution without claiming work you did not do.
- [ ] Practise 10–15 troubleshooting stories using: impact → evidence → root cause → safe fix → verification → prevention.
- [ ] Practise scenario questions: pipeline fails, Pod crashes, service has no endpoints, Terraform wants replacement, EC2 cannot reach internet, deployment health check fails.
- [ ] Prepare trade-off answers: public vs private subnet, SG vs NACL, Deployment vs StatefulSet, image vs container, Terraform state vs configuration, cache vs artifact.
- [ ] Explain a mistake, what you learned, and how you prevented recurrence.
- [ ] Practise a mock interview aloud; identify weak answers and create a lab for each gap.

## Phase 5 — Resume and applications

- [ ] Put only skills you can explain and demonstrate on the resume.
- [ ] Describe project outcomes and technical decisions truthfully; avoid invented production experience or metrics.
- [ ] Link working repositories with useful READMEs, setup instructions, architecture diagrams, and screenshots/log excerpts with secrets removed.
- [ ] Tailor the resume to the actual job description; distinguish hands-on practice from professional experience.
- [ ] Keep a tracker for applications, role requirements, interview feedback, and topics to revise.

## Weekly routine (adjust to your available time)

- 40% hands-on labs and projects
- 25% concepts and official docs
- 20% interview questions and spoken explanations
- 15% revision, README improvements, and applications

## Job-readiness self-check

You are approaching interview-ready when you can, without copying a tutorial:
1. Build and explain a small end-to-end project.
2. Diagnose common failures using evidence rather than guessing.
3. Explain core tools and trade-offs in simple language.
4. Show safe practices for permissions, secrets, cost and cleanup.
5. Walk through your own code and answer follow-up questions honestly.

No checklist can cover every employer's interview. Use the job description to prioritise the skills actually requested.
