# Contributing to rune-infra

Thank you for your interest in contributing to the rune-infra project!

## Getting Started

1. Fork the repository
2. Clone your fork: `git clone https://github.com/YOUR-USERNAME/rune-infra.git`
3. Create a feature branch: `git checkout -b feat/my-feature`
4. Install dependencies: `pip install -r requirements.txt`

## Development Workflow

1. **Write tests** (or identify manual test steps)
2. **Implement your change** 
3. **Test locally** before pushing
4. **Submit a pull request** with a clear description

## Code Standards

- All YAML files must have SPDX license headers
- Use `ansible-lint` for Ansible syntax: `ansible-lint roles/`
- Use `yamllint` for YAML validation: `yamllint .`
- Follow the existing role structure and naming conventions

## Roles and Playbooks

When adding new roles:
- Create `roles/<name>/{tasks,defaults,handlers,templates}` directories
- Include `tasks/main.yml` with clear task names
- Document variables in `defaults/main.yml`
- Add `handlers/main.yml` if needed
- Ensure idempotency (running twice produces same result)

## Documentation

- Update `README.md` if adding public features
- Add role documentation to `docs/VARIABLES.md`
- Include examples in the relevant doc files

## Testing

Before submitting:
```bash
# Lint checks
ansible-lint roles/
yamllint .

# Syntax check
ansible-playbook --syntax-check playbooks/deploy_runners.yml

# (Optional) Test against test environment
ansible-playbook -i inventory/proxmox.ini playbooks/deploy_runners.yml
```

## Pull Request Process

1. Update relevant documentation
2. Add SPDX headers to new files
3. Reference the issue with `Closes #NNN` in PR body
4. Ensure all CI checks pass
5. Request review from maintainers
6. Squash and merge once approved

## Questions?

Open an issue or discussion on GitHub. We're here to help!
