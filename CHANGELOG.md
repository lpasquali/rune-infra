# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [1.0.0] - 2026-04-12

### Added

- Initial release of rune-infra collection
- Six modular roles:
  - `container_base` - Base LXC container setup
  - `build_tools` - Build toolchain installation
  - `docker_runtime` - Docker CE and Compose
  - `runtime_env` - Python 3, Node.js, language runtimes
  - `github_runner` - GitHub Actions runner installation and registration
  - `runner_validation` - Post-deployment health checks
- Three orchestrating playbooks:
  - `deploy_runners.yml` - Full deployment automation
  - `validate_uat.yml` - Runner validation
  - `cleanup.yml` - Teardown and deregistration
- GitHub Actions CI/CD integration:
  - `uat-deploy.yml` - Automated deployment workflow
  - `uat-validation.yml` - Validation and testing workflow
- Full documentation and examples
- Apache 2.0 license
