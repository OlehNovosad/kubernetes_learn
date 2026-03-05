# Security Testing with Snyk

## Overview

[Snyk](https://snyk.io) is a developer-first security tool that can scan your Kubernetes manifests and container images for vulnerabilities, misconfigurations, and compliance issues — helping you secure your deployments before they reach production.

## Testing a Kubernetes Deployment

Snyk can analyze your YAML manifests (e.g. `deployment.yaml`) and report security issues such as:

- Containers running as root
- Missing resource limits
- Privileged containers
- Missing security contexts
- Exposed sensitive environment variables

## Getting Started

### 1. Install Snyk CLI

```bash
npm install -g snyk
```

### 2. Authenticate

```bash
snyk auth
```

### 3. Scan Kubernetes Manifests

```bash
snyk iac test deployment.yaml
```

To scan all manifests in the current directory:

```bash
snyk iac test .
```

### 4. Review the Results

Snyk will output a list of issues grouped by severity (**Critical**, **High**, **Medium**, **Low**) along with remediation guidance.

## Example Output

```
Testing deployment.yaml...

Infrastructure as code issues:
  ✗ Container is running without a high UID [Medium Severity]
  ✗ CPU limits should be set [Low Severity]
  ✗ Memory limits should be set [Low Severity]

-------------------------------------------------------
Test Summary: 3 issues found
```

## Useful Flags

| Flag | Description |
|------|-------------|
| `--severity-threshold=high` | Only report high and critical issues |
| `--report` | Send results to the Snyk dashboard |
| `--json` | Output results as JSON |

## Further Reading

- [Snyk IaC Documentation](https://docs.snyk.io/products/snyk-infrastructure-as-code)
- [Snyk CLI Reference](https://docs.snyk.io/snyk-cli/cli-reference)
