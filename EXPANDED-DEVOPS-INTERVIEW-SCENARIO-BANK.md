# Expanded DevOps Interview Scenario Bank

Use this file alongside the tool-wise troubleshooting guide and official documentation labs. The point is to diagnose with evidence, not to memorise random fixes.

## Standard response pattern
**Impact → scope → recent change → evidence → hypothesis → smallest safe fix → verification → prevention.**
State assumptions. Avoid destructive changes until you understand impact. Never expose secrets in terminal output, screenshots, or commits.

## 1. Linux / operating system
- Disk full: compare df -h and df -i; use du to locate growth; inspect logs and deleted-open files safely; clean only known-safe data.
- Service down: systemctl status, journalctl, configuration validation, dependency/port check; restart only after understanding why.
- Permission denied: inspect identity, ownership, mode, parent-directory permissions, ACLs and service user.
- High CPU/memory: top/ps/free, process history and logs; check OOM events and workload change.
- Port already in use: ss/lsof to identify listener; validate intended service before changing ports/processes.
- DNS vs network: resolve name first, then test route/connectivity/port/TLS separately.

## 2. Git / GitHub
- Merge conflict: inspect status and conflict markers, resolve intentionally, run tests, commit.
- Rejected push: inspect remote/branch divergence and branch protection; do not blindly force-push.
- Detached HEAD: preserve needed commit/changes by creating a branch before switching.
- Wrong commit: choose revert for shared history; reset only when history/coordination makes it safe.
- Authentication/403: verify account, SSH/PAT/credential helper and least-privilege repository permissions; never paste tokens in chat/logs.
- CI status check blocks merge: inspect failed job and branch protection required checks.

## 3. Docker
- Container exits: docker ps -a, logs, inspect exit code, command/entrypoint and env.
- Build fails: inspect Dockerfile, build context, .dockerignore, dependency availability and cache.
- Port inaccessible: compare published port, container listening address, host firewall and app health.
- Data disappeared: check volume/bind mount and container lifecycle.
- ImagePull failure: verify name/tag, registry access, authentication and architecture.
- Security: run non-root where practical, minimise image, avoid embedding secrets, scan dependencies/images.

## 4. Kubernetes
- Pending: describe Pod/events, resource capacity, selectors, taints, PVC.
- CrashLoopBackOff: current/previous logs, command/args, config, dependencies, probes and OOM.
- ImagePullBackOff: image/tag, registry and pull secret.
- Service has no endpoints: compare selectors, labels and readiness.
- Ingress unreachable: check controller, IngressClass, DNS, Service endpoints, TLS and routing.
- PVC pending/mount failure: StorageClass, capacity, access mode, events and permissions.
- 401/403: active identity and RBAC bindings.
- Rollout stuck: rollout status/history, ReplicaSet/Pod events, readiness and image/config change.
- DNS/network policy: test from a permitted debug Pod; inspect service names, namespace, policy and endpoints.
- Verify fixes with events, logs, endpoints, readiness and rollout state.

## 5. Terraform
- Plan wants unexpected replacement: inspect diff, immutable fields, address/index changes, provider changes and lifecycle.
- Import causes diff: ensure resource address and configuration match real attributes; review plan before applying.
- State lock: identify lock owner and operation; never force-unlock a live operation.
- Drift: compare plan with real infrastructure and agree whether code or infrastructure is source of truth.
- for_each/count address change: understand resource addresses; use state mv or moved blocks where appropriate and plan carefully.
- Provider/backend issue: validate version constraints, init state, credentials and backend configuration.
- Partial apply: inspect state and real objects before retrying; do not assume nothing was created.
- Sensitive output: state may contain secrets; restrict access and never commit state files.

## 6. AWS / VPC
- Public EC2 no internet: public IPv4/EIP as applicable, subnet route to attached IGW, SG/NACL, OS firewall and listener.
- Private EC2 no outbound IPv4: private route to NAT, NAT in public subnet, NAT subnet route to IGW, NAT health and NACL return path.
- Two instances cannot connect: routes, SG source, NACL, host firewall, listening process and correct private address.
- One destination fails: longest-prefix route, route-table association, peering/TGW/VPN path, DNS and egress controls.
- S3 access denied: identity/resource policy, bucket policy, endpoint policy, KMS policy and requested action/resource.
- EC2 role denied: attached role, trust policy, permission policy, resource policy and service control policy if applicable.
- High cost: identify service/region/usage, tags and budgets; avoid deleting shared resources without ownership checks.

## 7. GitHub Actions / Jenkins CI/CD
- Workflow not triggered: workflow path, event, branch/path filters and YAML.
- Job queued: runner labels, capacity and self-hosted runner status.
- Permission denied: workflow token permissions, secret scope, environment approvals and event restrictions.
- Works locally but fails in CI: versions, OS, working directory, env vars, service dependencies and case sensitivity.
- Artifact missing: upload/download names, path, job dependency and retention.
- Flaky tests: isolate nondeterminism, timing, shared state, network dependency and retries; do not mask real failures with infinite retries.
- Jenkins credential failure: credential ID/scope, binding, agent permissions and logs; do not print secret values.
- Deployment failed: health check, rollout, artifact/version, access, migration compatibility and rollback strategy.

## 8. Monitoring / incident response
- Alert without impact: verify signal, scope, threshold, recent change and user impact.
- Metrics vs logs vs traces: use metrics to detect, logs for event detail, traces for request path; correlate timestamps and IDs safely.
- Latency spike: inspect percentiles, saturation, errors, dependencies, recent releases and traffic change.
- Incident review: timeline, impact, detection, contributing causes, response, recovery and blameless prevention actions.

## Lab evidence template
- Scenario and impact:
- Evidence collected:
- Hypothesis and why:
- Root cause:
- Safest fix:
- Verification:
- Prevention:
- Interview answer:
