# GitHub Actions Troubleshooting Scenarios

For every issue record symptom, evidence, root cause, fix, verification and prevention. Never expose secret values.

## 1. Workflow does not start
- Confirm file is under `.github/workflows/` and ends in .yml/.yaml.
- Check `on` event, branch/path filters, YAML indentation, and pull request context.
- Verify using a safe commit matching the trigger.

## 2. YAML syntax or key error
Common causes: indentation, malformed expressions, misspelled `runs-on` (not `run-on`), or shell/YAML confusion.

Minimal workflow:
```yaml
name: CI
on:
  push:
    branches: [main]
  pull_request:
jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Show message
        run: echo "CI started"
```

## 3. Permission denied
Check workflow/job `permissions:`, minimum token scopes, fork restrictions, cloud OIDC trust conditions, and environment approvals. Grant only required permissions.

## 4. Secret/environment variable is empty
Check exact secret name, repository/org/environment scope, selected environment and fork restrictions. Never echo a secret; use a safe boolean check or inspect redacted errors.

## 5. Dependencies/tests fail
Check runtime version, lock file, package manager, working directory, cache key, registry connectivity and first failing command. Reproduce locally if practical. Cache must not be required for correctness.

## 6. Docker build fails in CI
Check build context, Dockerfile path, Linux case-sensitive paths, .dockerignore, architecture, base image and registry limits. Never bake credentials into image layers. Verify with a clean build and smoke test.

## 7. Artifact missing in later job
Jobs usually use separate runner environments. Upload output as an artifact in the producing job and download the matching artifact in the dependent job. Check the output path and retention policy.

## 8. Deploy command succeeds but app is unhealthy
Check release/version, service/container/pod state, startup logs, health endpoint, port, DNS, security rules, config and migrations. Run a user-path smoke test; follow documented rollback if health checks fail.

## 9. Runner queued/unavailable
Check `runs-on` labels, hosted runner availability/quota, self-hosted runner online status, runner logs, network/proxy, disk and concurrency groups.

## 10. Action update breaks workflow
Review release notes, input changes, runtime requirements and deprecations. Pin an approved immutable commit SHA where required by security policy; test updates before rollout.

## Hands-on checklist
- [ ] Trigger workflow on push and pull_request.
- [ ] In a disposable branch, use `run-on` then fix the validation error.
- [ ] Make a test fail and locate the first failing step.
- [ ] Pass an artifact between jobs.
- [ ] Use least-privilege workflow permissions.
- [ ] Build a Docker image and smoke-test it.
- [ ] Add a health check and document rollback.
- [ ] Explain why secrets must not be printed or committed.

Official: https://docs.github.com/en/actions/how-tos/troubleshoot-workflows
