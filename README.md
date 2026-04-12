# rune-infra: Ansible Collection for GitHub Actions Runners on Proxmox

[![Apache License 2.0](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](LICENSE)
[![Ansible Collection](https://img.shields.io/badge/ansible--collection-rune.infra-blue.svg)](galaxy.yml)

An Ansible collection to deploy and validate GitHub Actions self-hosted runners on Proxmox LXC containers, with automated UAT merge gates.

## Overview

**rune-infra** automates the deployment of full-featured GitHub Actions runners on Proxmox LXC containers, providing:

- **Modular roles** for container setup, build tools, Docker, language runtimes, and runner registration
- **Dynamic scaling** — deploy 1-N runners with a single variable
- **Automated validation** — post-deployment health checks verify all tooling
- **CI/CD merge gate** — pull requests cannot merge until UAT deployment and product tests pass
- **Cleanup automation** — ephemeral runners are automatically deregistered and removed after validation

## Architecture

```
GitHub PR → Workflow Trigger → Deploy Runners → Validate → Run Tests → Status Gate
                                    ↓
                            Proxmox LXC Containers
                            (Debian 12 minimal)
                                    ↓
                    [Docker] [Git] [Python] [Node.js]
                    [GitHub Actions Runner Agent]
```

## Quick Start

### Prerequisites

- Ansible 2.12+
- Python 3.8+
- Proxmox host with LXC support
- GitHub PAT with runner registration scopes
- SSH access to Proxmox node

### Installation

1. **Install the collection:**
   ```bash
   ansible-galaxy collection install git+https://github.com/lpasquali/rune-infra.git
   ```

2. **Configure inventory:**
   ```bash
   cp inventory/proxmox.ini inventory/proxmox.ini.local
   # Edit inventory/proxmox.ini.local with your Proxmox details
   ```

3. **Set environment variables or use vault:**
   ```bash
   export ANSIBLE_VAULT_PASSWORD_FILE=~/.vault_pass
   # Or provide secrets via command line: -e @secrets.yml
   ```

### Deploy Runners

```bash
# Deploy 2 runners
ansible-playbook playbooks/deploy_runners.yml \
  -i inventory/proxmox.ini \
  -e "runner_count=2" \
  -e "github_runner_github_token=${GITHUB_TOKEN}"
```

### Validate Deployment

```bash
# Validate runners are online and healthy
ansible-playbook playbooks/validate_uat.yml \
  -i inventory/proxmox.ini \
  -e "github_runner_github_token=${GITHUB_TOKEN}"
```

### Cleanup

```bash
# Deregister and remove runners
ansible-playbook playbooks/cleanup.yml \
  -i inventory/proxmox.ini \
  -e "github_runner_github_token=${GITHUB_TOKEN}"
```

## Collection Roles

| Role | Purpose |
|------|---------|
| `container_base` | Base LXC setup: hostname, users, SSH, sudo |
| `build_tools` | Build toolchain: gcc, make, git, curl, jq |
| `docker_runtime` | Docker CE installation and daemon configuration |
| `runtime_env` | Python 3, Node.js, language runtimes |
| `github_runner` | GitHub Actions runner binary, registration, systemd |
| `runner_validation` | Post-deployment health checks |

## Playbooks

| Playbook | Purpose |
|----------|---------|
| `deploy_runners.yml` | Full orchestration: create containers, install tooling, register runners |
| `validate_uat.yml` | Verify runners are online, check GitHub registration |
| `cleanup.yml` | Deregister runners and remove containers |

## Configuration

### Required Variables (Secrets)

Store these in GitHub Actions secrets or Ansible vault:
- `GITHUB_RUNNER_TOKEN` — GitHub PAT with `admin:org_hook` and `repo` scopes
- `PROXMOX_API_HOST` — Proxmox API endpoint (e.g., `proxmox.example.com`)
- `PROXMOX_API_TOKEN_ID` — Token ID (e.g., `ansible@pam!runner-token`)
- `PROXMOX_API_TOKEN_SECRET` — Token secret UUID

### Key Variables

**Global** (`inventory/group_vars/all.yml`):
- `runner_count` — Number of runners to deploy (default: 1)
- `lxc_container_name_prefix` — Container name prefix (default: `gh-runner`)
- `github_runner_labels` — Custom labels for runners

**Proxmox** (`inventory/group_vars/proxmox_hosts.yml`):
- `proxmox_node` — Proxmox node name (default: `pve`)
- `lxc_memory_mb` — RAM per container (default: 2048)
- `lxc_cpus` — vCPUs per container (default: 2)
- `lxc_disk_gb` — Disk size per container (default: 20)

**Runner-specific** (role defaults):
- `github_runner_version` — Runner binary version
- `github_runner_ephemeral` — Auto-deregister after each job (default: true)

## GitHub Actions Integration

### Merge Gate Workflow

The UAT merge gate automatically:
1. **Deploys** runners on pull request
2. **Validates** runners come online
3. **Runs tests** on deployed runners
4. **Cleans up** infrastructure (always)
5. **Sets status** — pass/fail determines merge eligibility

See `.github/workflows/uat-deploy.yml` and `.github/workflows/uat-validation.yml`.

## Documentation

- [Deployment Guide](docs/DEPLOYMENT.md)
- [UAT Gate Explanation](docs/UAT_GATE.md)
- [All Configurable Variables](docs/VARIABLES.md)
- [Troubleshooting](docs/TROUBLESHOOTING.md)

## Examples

See the `examples/` directory for sample playbooks:
- `5-runner-cluster.yml` — Deploy 5 runners with custom labels
- `with-custom-tools.yml` — Add additional tools to runners
- `air-gapped-deployment.yml` — Deploy without external internet

## License

[Apache License 2.0](LICENSE) — Copyright 2026 RUNE Contributors

## Contributing

Contributions are welcome! Please:
1. Fork the repository
2. Create a feature branch
3. Add tests and documentation
4. Submit a pull request

See [CONTRIBUTING.md](CONTRIBUTING.md) for details.
