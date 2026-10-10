# Official Documentation: Practical Troubleshooting Labs

Use this with tool-wise-troubleshooting.md, INTERVIEW-READY.md, and TOOL-WISE-INTERVIEW-QUICK-SHEET.md. Practise each scenario in a safe lab and explain it as symptom → evidence → root cause → fix → verification.

## Lab 1 — Terraform state and import
1. Run terraform fmt -check, terraform validate, and terraform plan before applying.
2. Inspect state with terraform state list and terraform state show <address>.
3. For a pre-existing resource, write matching resource configuration, then use the documented import workflow for that resource address and real provider ID.
4. Run plan and reconcile differences deliberately; do not blindly apply an import-generated diff.
5. Practise remote backend and state locking in a disposable environment. If a lock exists, identify the owner before considering unlock.
6. Create controlled drift in a lab, run plan, and explain the proposed reconciliation.

Interview answer: Terraform state maps configuration addresses to real objects. Remote state and locking help teams coordinate; imports connect existing objects to addresses, but configuration still needs to match the real resource.

Reference: https://developer.hashicorp.com/terraform/tutorials/state

## Lab 2 — Kubernetes workload debugging
Run these commands against a test namespace:
~~~bash
kubectl get pods -A
kubectl describe pod <pod> -n <namespace>
kubectl logs <pod> -n <namespace> --all-containers
kubectl logs <pod> -n <namespace> --previous
kubectl get events -n <namespace> --sort-by=.metadata.creationTimestamp
kubectl get svc,endpoints -n <namespace>
kubectl rollout status deployment/<deployment> -n <namespace>
~~~

Scenarios:
- Pending: inspect events, requests, node capacity, selectors, taints/tolerations and PVC status.
- CrashLoopBackOff: inspect current and previous logs, command/args, config, dependencies and probes.
- ImagePullBackOff: verify image name/tag, registry reachability and pull-secret configuration.
- Service has no endpoints: compare Service selectors with Pod labels and readiness.
- PVC pending: inspect StorageClass, access mode, capacity and events.
- 403 Forbidden: verify the active identity and RBAC Role/RoleBinding or ClusterRoleBinding.

Do not delete Pods before reading events and logs. Verify the fix by checking readiness, endpoints and rollout status.

Reference: https://kubernetes.io/docs/tasks/debug/

## Lab 3 — GitHub Actions failed workflow
Inspect the failed run summary and the first failing step before editing YAML.

Scenarios:
- Workflow never starts: check file location under .github/workflows/, YAML syntax, event name, branch/path filters and workflow permissions.
- Job never schedules: verify runs-on, runner availability and labels.
- Checkout or API call gets 403: inspect repository/workflow permissions and least-privilege GITHUB_TOKEN settings.
- Secret is empty: verify secret scope/name and event restrictions; never print secrets to logs.
- Artifact missing: check upload/download names, paths, job dependencies and retention.
- Dependency cache misses: check cache key and restore-key strategy; cache is an optimization, not a source of truth.
- Test fails only in CI: compare runtime versions, environment variables, working directory, OS assumptions and service dependencies.

Verification: rerun the relevant workflow, confirm intended and downstream jobs pass, and document the root cause.

Reference: https://docs.github.com/en/actions/how-tos/troubleshoot-workflows

## Lab 4 — AWS VPC route-table diagnosis
For each subnet, record subnet CIDR, associated route table, longest-prefix matching route, target, and security controls.

Scenarios:
1. Public subnet cannot reach internet: check route-table association, 0.0.0.0/0 to an attached Internet Gateway, public IPv4/EIP, security group egress/ingress, NACL rules and OS firewall.
2. Private subnet cannot reach public IPv4 destinations: check default route to a NAT Gateway, NAT Gateway placement in a public subnet, public route to IGW, NAT state and NACL return traffic.
3. Instance-to-instance connection fails: check local VPC route, peering/TGW routes if applicable, SG source rules, NACLs and listening process.
4. DNS fails: inspect VPC DNS support/hostnames, resolver configuration and application DNS settings.
5. One CIDR works but another does not: inspect more-specific routes and longest-prefix selection.

Remember:
- A route table controls where subnet traffic is directed; it does not by itself grant permission.
- Security groups are stateful; network ACLs are stateless.
- A public route alone does not give an instance a public IP.
- NAT Gateway enables outbound-initiated connectivity for private IPv4 workloads; it does not allow unsolicited inbound internet connections to the private instance.

Reference: https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Route_Tables.html

## Lab report template
Copy this block for every issue:
- Scenario / expected behaviour:
- Observed symptom and exact error:
- Commands or logs checked:
- Root cause (evidence-based):
- Fix and why it works:
- Verification result:
- Prevention / monitoring:
- 30-second interview explanation:

## Official links
- Terraform tutorials: https://developer.hashicorp.com/terraform/tutorials
- Kubernetes debugging: https://kubernetes.io/docs/tasks/debug/
- GitHub Actions troubleshooting: https://docs.github.com/en/actions/how-tos/troubleshoot-workflows
- AWS VPC route tables: https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Route_Tables.html
