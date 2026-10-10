# All DevOps Troubleshooting Scenarios — Coverage Matrix

This is the **master index** for interview preparation and hands-on troubleshooting. It makes the scenario scope visible in one place, so you can track what to read, practise, and explain.

> **Coverage note:** This is a structured checklist of common and important scenarios, not a claim that every possible production failure is documented. A topic is not “completed” until you can reproduce or diagnose a safe lab example, explain the evidence, apply a fix, and verify the result.

## How to use this checklist

For each scenario, practise this sequence:

1. **Symptom:** What is broken and what changed?
2. **Evidence:** Which command, log, event, metric, or configuration proves it?
3. **Root cause:** What does the evidence establish (not just guess)?
4. **Fix:** What is the smallest safe change?
5. **Verification:** How do you prove the service works now?
6. **Prevention:** What test, alert, review, or runbook would prevent recurrence?

## Coverage status key

- [ ] Not yet demonstrated in a hands-on lab
- [x] Scenario category is represented in the repository; **you still need to practise it** to mark your personal readiness complete

The checkboxes below are a learning tracker. Do not tick them just because you read the page.

## 1. Linux, processes, storage, and logs

| Scenario | Evidence / first checks | Typical investigation focus |
|---|---|---|
| Service fails to start | systemctl status; journalctl -u SERVICE | Invalid config, missing dependency, permission, port conflict |
| Service is active but app is unavailable | ss -lntp; curl -v; app logs | Wrong bind address, wrong port, upstream failure |
| High CPU | top; ps; process metrics | Hot process, runaway loop, traffic spike |
| High memory / OOM kill | free -h; ps; kernel logs | Leak, limit too low, workload spike |
| Disk full / inode exhaustion | df -h; df -i; du | Large logs, deleted-but-open files, too many small files |
| Permission denied | id; namei -l; ls -l | Owner/group/mode, parent directory, service identity |
| DNS resolution failure | getent hosts; dig; resolvectl | Resolver, search domain, record, network path |
| Host cannot reach another service | ip route; ss; curl; nc | Route, firewall, listener, DNS, TLS |
| Log has no useful information | service config and logging settings | Log level, wrong file, rotation, structured fields |
| Scheduled job does not run | cron/systemd timer logs and environment | PATH, user context, timezone, permissions |

Reference: [Log files reference](LOG-FILES-REFERENCE.md), [Logs debugging](logs-debugging.md).

## 2. Git and GitHub

| Scenario | Evidence / first checks | Typical investigation focus |
|---|---|---|
| Push rejected | git status; git fetch; push output | Non-fast-forward, branch protection, permission |
| Merge conflict | git status; conflict markers; diff | Resolve intended content; test before commit |
| Detached HEAD / wrong branch | git status -sb; git branch -avv | Correct branch and preserve needed commits |
| Wrong commit or accidental file change | git log; git diff; git reflog | Revert vs reset; avoid rewriting shared history |
| Authentication or permission failure | remote URL, credential helper, repository access | Token/SSH key, SSO, repo role; never paste credentials |
| GitHub Actions workflow not triggered | workflow path, event, branch filters, Actions tab | Trigger syntax, filters, disabled workflow |
| Branch protection blocks merge | PR checks and repository rules | Required checks, reviews, up-to-date branch |
| Large/secret file committed | history and secret scanner | Revoke exposed secret first; remove safely and follow incident process |

## 3. Docker and container images

| Scenario | Evidence / first checks | Typical investigation focus |
|---|---|---|
| Container exits immediately | docker ps -a; docker logs CONTAINER | Entrypoint, command, missing config, app crash |
| Container keeps restarting | docker inspect; logs; exit code | Health check, dependency, startup command |
| Image build fails | build output, Dockerfile, build context | Missing files, invalid instruction, dependency download |
| Image builds but app fails | docker run; logs; env/config | Runtime-only dependency, permissions, wrong command |
| Port not reachable | docker port; docker inspect; host listener | Missing publish mapping, wrong app bind address |
| Container cannot resolve/reach dependency | docker exec; DNS and network inspection | Network membership, DNS name, firewall, readiness |
| Volume data missing / permissions wrong | docker inspect; mount details, ownership | Wrong mount path, host permissions, ephemeral filesystem |
| Container is unhealthy | health-check output and app logs | Probe command, startup delay, actual dependency health |
| Image is too large or build is slow | image history and build output | Build context, cache, multi-stage builds, unnecessary packages |

## 4. Kubernetes

| Scenario | First checks | Typical investigation focus |
|---|---|---|
| Pod stuck Pending | kubectl describe pod; events; node capacity | Insufficient resources, taints, affinity, PVC |
| ImagePullBackOff / ErrImagePull | pod events, image and registry settings | Image/tag typo, pull secret, registry access |
| CrashLoopBackOff | kubectl logs POD --previous; describe pod | App crash, command, config, dependency, probe |
| Pod Running but not Ready | readiness probe, endpoints, app logs | Probe path/port, startup delay, dependency |
| OOMKilled | pod status, requests/limits, metrics | Memory demand, leak, incorrect limits |
| Service has no endpoints | selectors, labels, EndpointSlices | Label mismatch, readiness failure, wrong namespace |
| Ingress returns 404/502/503 | ingress/controller logs, service and endpoints | Host/path rule, backend port, controller, readiness |
| DNS lookup fails inside cluster | test pod, CoreDNS pods/logs, DNS config | Service name/namespace, CoreDNS, network policy |
| ConfigMap/Secret change not reflected | pod spec, mounts, rollout status | Environment variables need restart; mount/update behavior |
| PVC stuck Pending / mount fails | PVC/PV events, StorageClass, CSI logs | Capacity, access mode, provisioner, zone |
| Deployment rollout stuck | kubectl rollout status; describe deployment/RS | Bad image, readiness, quota, scheduling |
| Node NotReady | node conditions/events, kubelet/runtime logs | Resource pressure, network, runtime, kubelet |
| NetworkPolicy blocks traffic | policies, pod labels, namespace labels | Ingress/egress rules and DNS allowances |
| RBAC Forbidden | kubectl auth can-i; role/binding inspection | Subject, namespace scope, missing verb/resource |
| HPA not scaling | HPA status/events, metrics API, requests | Metrics unavailable, target config, missing requests |

Reference: [Kubernetes debugging tasks](https://kubernetes.io/docs/tasks/debug/), [Kubernetes YAML types](KUBERNETES-ALL-YAML-TYPES.md).

## 5. Terraform and Infrastructure as Code

| Scenario | First checks | Typical investigation focus |
|---|---|---|
| terraform init fails | exact error, provider/module source, network | Version constraints, credentials, registry/network |
| terraform validate fails | validation output and configuration | HCL syntax, types, required arguments |
| terraform plan wants unexpected replacement | plan diff, lifecycle and immutable fields | Changed argument, provider behavior, state drift |
| Resource exists in cloud but not state | terraform state list, cloud resource ID | Import the existing object; avoid duplicate creation |
| State lock cannot be acquired | lock error, active runs, backend | Confirm no active operation before safe recovery |
| State/backend access fails | backend config, identity and permissions | Bucket/key/region/lock permissions, credentials |
| Provider authentication fails | identity command and provider config | Wrong profile/role, expired credentials, region |
| Dependency ordering is wrong | plan graph, resource references, depends_on only if needed | Express real dependencies in configuration |
| count/for_each address mismatch | state list and plan | Stable keys, moved/import blocks, safe state migration |
| Drift appears in plan | refresh/plan output and cloud console | Manual change vs configuration; reconcile deliberately |
| Sensitive values appear in output | variable/output/provider configuration | Mark sensitive; understand state can still contain secrets |
| Destroy would remove critical resource | saved plan and dependency review | Stop, inspect targeted change; avoid blind apply/destroy |
| Module/provider version changes behavior | lock file and version constraints | Pin/test versions and review upgrade notes |

Official practice: [Terraform tutorials](https://developer.hashicorp.com/terraform/tutorials). Always inspect the plan before applying; use a disposable account/environment for destructive labs.

## 6. AWS, VPC, EC2, IAM, and connectivity

| Scenario | First checks | Typical investigation focus |
|---|---|---|
| Public EC2 cannot be reached from internet | public IP, subnet route table, SG, NACL, OS firewall | Internet Gateway route and inbound/outbound path |
| Private EC2 cannot access internet | private route table, NAT route, NAT health | NAT Gateway and return route; private instances need not have public IPs |
| Two subnets cannot communicate | routes, SGs, NACLs, CIDRs | Route target, overlapping CIDRs, return path |
| DNS resolves but connection times out | dig; nc; flow logs if available | Security groups, NACLs, routes, listener |
| Connection refused | listener and target port | Host reachable but no service listening or wrong port |
| Load balancer target unhealthy | target health reason, listener, health path | SG chain, protocol/port, app response, readiness |
| EC2 cannot assume role/access service | instance profile, role trust and policy | Trust policy, permissions, endpoint/network |
| S3 access denied | caller identity, bucket policy, IAM policy, KMS | Explicit deny, role, resource policy, encryption |
| VPC peering/TGW route missing | both sides' route tables and attachments | Routes in both directions and overlapping CIDRs |
| Interface/gateway endpoint access fails | endpoint policy, route/DNS, SG for interface endpoint | Correct endpoint type and policy |

Official reference: [AWS VPC route tables](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Route_Tables.html). Validate both forward and return paths; do not open all ports to the world as a shortcut.

## 7. GitHub Actions and CI/CD

| Scenario | First checks | Typical investigation focus |
|---|---|---|
| Workflow never starts | Actions tab, workflow file path, event and branch filters | Trigger configuration, workflow disabled |
| YAML/workflow syntax error | workflow validation and run annotations | Indentation, keys, expressions, unsupported fields |
| Checkout or dependency step fails | step logs, action version, network | Permissions, path, package registry, version pinning |
| Permission denied to repository/package | job permissions, token scope, package access | Least-privilege permissions, package/repo settings |
| Secret is empty or unavailable | event type, environment approvals, secret name | Fork PR restrictions, environment scope; never print secrets |
| Cache/artifact not found | cache key, upload/download paths, run IDs | Key mismatch, artifact name/path, retention |
| Docker build/push fails | build logs, registry login and permissions | Tags, credentials, build context, registry permissions |
| Deployment succeeds but app is down | rollout/health check, target status, logs | Deployment command success is not application health |
| Self-hosted runner offline/dirty | runner status and runner logs | Service, labels, workspace cleanup, capacity |
| Concurrency causes cancelled deployment | concurrency groups and run history | Group/cancel policy and release ordering |
| Tests pass locally but fail in CI | runtime versions, env, working directory | Hidden local dependencies, missing env, OS differences |
| Rollback fails | release artifact, previous version, health checks | Keep known-good artifact and documented rollback procedure |

Official reference: [GitHub Actions troubleshooting](https://docs.github.com/en/actions/how-tos/troubleshoot-workflows).

## 8. Jenkins and general delivery pipelines

| Scenario | First checks | Typical investigation focus |
|---|---|---|
| Pipeline fails at checkout | console output, credentials, repository URL | SCM credential and branch configuration |
| Agent/node unavailable | queue, node labels, agent logs | Offline agent, labels, executor capacity |
| Tool works on controller but not agent | agent environment and PATH | Tool installation and environment consistency |
| Credentials binding fails | credential ID/type and scope | Correct credential reference; never echo secrets |
| Pipeline hangs | stage logs, process, timeout | Waiting for approval/input, locked resource, external command |
| Deployment stage passes but release is unhealthy | deployment logs and health checks | Verify real service behavior and rollback path |

## 9. Monitoring, observability, and incident response

| Scenario | First checks | Typical investigation focus |
|---|---|---|
| Alert does not fire | rule expression, labels, evaluation and delivery logs | Threshold, duration, missing series, notification route |
| Too many false alerts | metric history and alert rule | Noisy threshold, missing for duration, bad grouping |
| Dashboard has gaps | scrape/ingestion health, labels, time range | Target down, relabeling, retention, wrong query |
| Logs and metrics disagree | timestamps, labels, sampling and clock sync | Different scope, missing telemetry, time-window mismatch |
| Latency rises but CPU is normal | request latency, dependency timings, saturation | Database, network, queue, connection pool |
| Error rate spikes after release | deploy timeline, traces, logs, change diff | Regression, config change, dependency compatibility |
| Incident has no clear owner/runbook | alert metadata and incident timeline | Ownership, severity, escalation, communication |

## 10. Security, access, and configuration

| Scenario | First checks | Typical investigation focus |
|---|---|---|
| Secret exposed in repository/log | restrict access, revoke/rotate secret, audit use | Treat as incident; removing the visible line alone is insufficient |
| Least-privilege policy blocks a required action | denied action/resource and policy evaluation | Exact permission, resource ARN, condition and explicit deny |
| TLS/certificate failure | certificate chain, expiry, hostname, system time | Trust chain, SAN, renewal, proxy termination |
| App uses wrong environment/config | deployment manifest and effective config source | Config precedence, namespace/environment, stale rollout |
| Clock/time mismatch breaks auth or logs | system time and time sync status | NTP/time synchronization and timezone interpretation |

## 11. End-to-end project failure scenarios

Use one demo application deployed through a pipeline and infrastructure-as-code. Practise diagnosing:

- [ ] A bad application image causes a failed rollout.
- [ ] A readiness probe prevents traffic reaching an unready pod.
- [ ] A CI test failure blocks deployment.
- [ ] A missing IAM permission blocks deployment or cloud access.
- [ ] A route/security-group change breaks connectivity.
- [ ] A wrong Terraform variable causes an unexpected plan.
- [ ] A missing secret/configuration causes application startup failure.
- [ ] Monitoring alerts on increased errors or latency.
- [ ] A rollback restores the last known-good version.
- [ ] A post-incident note records cause, evidence, fix, verification, and prevention.

## 12. Interview answer checklist

For any scenario, be ready to answer:

- What is the impact and scope?
- What changed recently?
- Which command/log/event did you check first, and why?
- What evidence ruled out other causes?
- What was the root cause?
- What is the safest fix and its risk?
- How did you verify recovery?
- What monitoring, automation, test, or runbook would prevent recurrence?

A strong short answer format:

> “I first confirm the impact and scope, then check the relevant logs/events and current configuration. I use the evidence to isolate the root cause, apply the smallest safe fix, verify service health end-to-end, and add a preventive test or alert.”

## 13. Personal readiness tracker

Only mark a category complete when you can explain and demonstrate it:

- [ ] Linux/processes/storage/logs
- [ ] Git/GitHub
- [ ] Docker
- [ ] Kubernetes
- [ ] Terraform/IaC
- [ ] AWS networking/IAM/EC2
- [ ] GitHub Actions
- [ ] Jenkins/CI/CD
- [ ] Monitoring/SRE/incident response
- [ ] Security/secrets/TLS
- [ ] End-to-end project and rollback
- [ ] Mock interview: explain at least five scenarios without notes

## Related repository guides

- [Tool-wise troubleshooting](tool-wise-troubleshooting.md)
- [Expanded DevOps interview scenario bank](EXPANDED-DEVOPS-INTERVIEW-SCENARIO-BANK.md)
- [Official documentation practical labs](OFFICIAL-DOCS-PRACTICAL-TROUBLESHOOTING-LABS.md)
- [GitHub Actions troubleshooting scenarios](GITHUB-ACTIONS-TROUBLESHOOTING-SCENARIOS.md)
- [Interview-ready troubleshooting script](INTERVIEW-READY.md)
- [Tool-wise interview quick sheet](TOOL-WISE-INTERVIEW-QUICK-SHEET.md)
- [DevOps job-preparation roadmap](DEVOPS-JOB-PREPARATION-ROADMAP.md)
