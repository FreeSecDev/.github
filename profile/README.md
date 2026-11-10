<p align="center">
  <picture>
    <source media="(prefers-color-scheme: dark)" srcset="assets/logo-dark.svg">
    <img src="assets/logo-light.svg" alt="FreeSecDev" width="440">
  </picture>
</p>

<p align="center">
  Open-source tooling to audit, harden, verify and report on Linux hosts against open security baselines.<br>
  The scanning is done by <a href="https://www.open-scap.org/">OpenSCAP</a> with
  <a href="https://github.com/ComplianceAsCode/content">ComplianceAsCode</a> content.
  The remediation is our own Ansible.
</p>

<p align="center">
  <img alt="Status: pre-alpha" src="https://img.shields.io/badge/status-pre--alpha-orange">
  <img alt="Licence: Apache-2.0" src="https://img.shields.io/badge/licence-Apache--2.0-blue">
  <img alt="Remediation: own Ansible only" src="https://img.shields.io/badge/remediation-own%20Ansible%20only-0F766E">
  <img alt="Audit mode: changes nothing" src="https://img.shields.io/badge/audit%20mode-changes%20nothing-0F766E">
</p>

> **Pre-alpha.** Nothing here is ready for production hosts yet. No release has been published, and no operating
> system or benchmark is supported until it has been tested and published as supported.

## Repositories

| Repository | What it is | Status |
|---|---|---|
| [**ansible-linux-compliance**](https://github.com/FreeSecDev/ansible-linux-compliance) | The Ansible collection `freesecdev.linux_compliance`, in development. It is built to audit a host with OpenSCAP, remediate with native Ansible, verify the result, and report it in an offline HTML dashboard with an evidence bundle | [![ci](https://github.com/FreeSecDev/ansible-linux-compliance/actions/workflows/ci.yml/badge.svg?branch=main)](https://github.com/FreeSecDev/ansible-linux-compliance/actions/workflows/ci.yml?query=branch%3Amain) [![last commit](https://img.shields.io/github/last-commit/FreeSecDev/ansible-linux-compliance/main)](https://github.com/FreeSecDev/ansible-linux-compliance/commits/main) |
| [**linux-compliance-runner**](https://github.com/FreeSecDev/linux-compliance-runner) | A minimal, Alpine-based OCI toolbox image for the collection's CI. It is built rootless, scanned, given an SBOM, signed with cosign, and consumed by digest only | [![ci](https://github.com/FreeSecDev/linux-compliance-runner/actions/workflows/ci.yml/badge.svg?branch=main)](https://github.com/FreeSecDev/linux-compliance-runner/actions/workflows/ci.yml?query=branch%3Amain) [![last commit](https://img.shields.io/github/last-commit/FreeSecDev/linux-compliance-runner/main)](https://github.com/FreeSecDev/linux-compliance-runner/commits/main) |

## What the code is held to

Where a rule can be checked, CI checks it, with lint rules, repository guards and tests. The full
text is in [AGENTS.md](https://github.com/FreeSecDev/ansible-linux-compliance/blob/main/AGENTS.md) and the
[architecture decision records](https://github.com/FreeSecDev/ansible-linux-compliance/tree/main/docs/adr).

### Remediation

- **Audit changes nothing.** Remediation needs an explicit opt-in. It can be turned on or off per area, topic and
  rule, and disruptive controls must be allow-listed one by one
  ([ADR 0011](https://github.com/FreeSecDev/ansible-linux-compliance/blob/main/docs/adr/0011-control-toggles-and-cleanup.md)).
- **Only our own Ansible remediates.** The scanner's remediation is never run, read or shipped. That covers
  ComplianceAsCode playbooks and Bash fixes, XCCDF `fix` elements and `oscap --remediate`. A rule without a native
  remediation stays scan-only and is reported as `not_implemented`
  ([ADR 0025](https://github.com/FreeSecDev/ansible-linux-compliance/blob/main/docs/adr/0025-own-ansible-remediation-only.md),
  [ADR 0026](https://github.com/FreeSecDev/ansible-linux-compliance/blob/main/docs/adr/0026-no-content-scripts-on-hosts.md)).
- **Native modules only.** No `shell`, `command`, `raw` or `script` module anywhere, including in tests. What
  Ansible lacks becomes a typed Python module
  ([ADR 0003](https://github.com/FreeSecDev/ansible-linux-compliance/blob/main/docs/adr/0003-native-module-remediation.md)).
- **The reader is the oracle.** A setting counts as `applied` or `compliant` only when the program that reads it,
  such as sshd, libpwquality, linux-pam, auditd or the kernel, ends up with the profile's value. Anything the role
  cannot prove is refused with a reason, before it changes the host.
- **Every change can be undone.** Each change is recorded in a ledger, so cleanup restores the original state byte
  for byte.
- **External programs run under strict control.** They run only from Python, with an explicit argument list, a
  resolved absolute path, validated inputs and a timeout
  ([ADR 0004](https://github.com/FreeSecDev/ansible-linux-compliance/blob/main/docs/adr/0004-audited-process-execution.md)).

### Honest results

- We never invent rule coverage, and we never swap one benchmark for another silently.
- A host scan never claims ISO 27001, PCI DSS, NIST or NIS2 compliance. Mappings are evidence, not certification.
- We never redistribute restricted benchmark content, such as CIS documents or CIS-CAT content.
- Nothing is reported as passed or successful unless it actually ran.

### Code and supply chain

- Python is strictly typed: `mypy --strict` and Pyright strict, plus Ruff, Pylint and Bandit.
- Every module, role and plugin is referenced by its fully qualified name. Every YAML file starts with `---`.
- No inline code comments. Names, docstrings and the documentation in `docs/` carry the meaning.
- GitHub Actions are pinned to full commit SHAs, container images to digests, and tools to exact versions.
- Cloud access is OIDC-only, with no long-lived keys. Credentials are never printed, committed or logged.
- Lint rules, tests and security findings are never suppressed without a documented justification.

### How changes land

- Every change is tracked as an issue, starts on a branch named `<type>/FREES-<n>-<slug>`, and uses Conventional
  Commits with a `Refs:` footer.
- Significant decisions are written down as an ADR before they are implemented. Documentation changes in the same
  pull request as the code it describes.
- An independent reviewer reviews every pull request: one who did not write the change. The verdict is posted on
  the pull request.
- Changes merge through a merge queue as squash commits, and only when the full CI gate is green.

## Useful links

| | |
|---|---|
| Contributing | [CONTRIBUTING.md](https://github.com/FreeSecDev/ansible-linux-compliance/blob/main/CONTRIBUTING.md) |
| Rules for contributors and AI agents | [AGENTS.md](https://github.com/FreeSecDev/ansible-linux-compliance/blob/main/AGENTS.md) |
| Architecture decisions | [docs/adr](https://github.com/FreeSecDev/ansible-linux-compliance/tree/main/docs/adr) |
| Documentation | [docs](https://github.com/FreeSecDev/ansible-linux-compliance/tree/main/docs) |
| Toolbox image build | [linux-compliance-runner docs](https://github.com/FreeSecDev/linux-compliance-runner/tree/main/docs) |
| Code of conduct | [CODE_OF_CONDUCT.md](https://github.com/FreeSecDev/ansible-linux-compliance/blob/main/CODE_OF_CONDUCT.md) |
| Upstream content | [ComplianceAsCode](https://github.com/ComplianceAsCode/content) · [OpenSCAP](https://www.open-scap.org/) |

## Security

Please report vulnerabilities privately through
[GitHub private vulnerability reporting](https://github.com/FreeSecDev/ansible-linux-compliance/security/advisories/new).
Do not open a public issue. Never include secrets, real host evidence or scan output from systems you do not own. See
the [security policy](https://github.com/FreeSecDev/ansible-linux-compliance/blob/main/SECURITY.md).

<sub>All FreeSecDev code is licensed under the Apache License 2.0.</sub>
