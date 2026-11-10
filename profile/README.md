<p align="center">
  <picture>
    <source media="(prefers-color-scheme: dark)" srcset="assets/logo-dark.svg">
    <img src="assets/logo-light.svg" alt="FreeSecDev" width="440">
  </picture>
</p>

<p align="center">
  <strong>Linux compliance you can audit, remediate and prove, with every change under your control.</strong>
</p>

<p align="center">
  <img alt="Status: pre-alpha" src="https://img.shields.io/badge/status-pre--alpha-orange">
  <img alt="Licence: Apache-2.0" src="https://img.shields.io/badge/licence-Apache--2.0-blue">
  <a href="https://github.com/FreeSecDev/ansible-linux-compliance/actions/workflows/ci.yml?query=branch%3Amain"><img alt="CI" src="https://github.com/FreeSecDev/ansible-linux-compliance/actions/workflows/ci.yml/badge.svg?branch=main"></a>
</p>

FreeSecDev builds open-source tooling that measures Linux hosts against public security baselines and brings them
into line. [OpenSCAP](https://www.open-scap.org/) and [ComplianceAsCode](https://github.com/ComplianceAsCode/content)
do the measuring. The remediation is our own: written in Ansible, reviewed, tested against the software that enforces
each setting, and fully reversible.

> **Pre-alpha.** There is no release yet. Operating systems and benchmarks will be listed as supported only once they
> have been tested end to end.

## Projects

| Repository | Description |
|---|---|
| [**ansible-linux-compliance**](https://github.com/FreeSecDev/ansible-linux-compliance) | The Ansible collection `freesecdev.linux_compliance`: audit, remediation, verification and reporting, with an offline dashboard and an evidence bundle per run |
| [**linux-compliance-runner**](https://github.com/FreeSecDev/linux-compliance-runner) | The hardened, signed toolbox image that the collection's CI runs in |

## Principles

**Audit first.** An audit never changes a host. Remediation is opt-in and can be scoped down to a single rule.
Disruptive controls must be named explicitly.

**Our own remediation.** We use the scanner to measure, never to change a host. Every fix is Ansible that we wrote
and reviewed. A rule we have not implemented is reported as such, and never patched with third-party scripts.

**Verified where it counts.** A setting counts as fixed only when the program that enforces it, such as sshd, PAM,
auditd or the kernel, actually uses the required value. When that cannot be shown, the change is refused, with a
reason, before anything on the host is touched.

**Reversible.** Every change is recorded, and can be rolled back to the exact original state.

**Operator control.** Operators choose what is remediated, and will be able to set their own values. A value weaker
than the baseline is reported as a deviation, never as compliant.

**Evidence, not claims.** A scan result is evidence. It is never presented as ISO 27001, PCI DSS, NIST or NIS2
compliance. We do not invent coverage, and we do not redistribute restricted benchmark content.

## Engineering

- Strictly typed Python, and no shell execution anywhere in the remediation path. External programs run with fixed
  arguments, resolved paths and timeouts.
- Dependencies and CI actions are pinned exactly. Container images are signed, come with an SBOM, and are consumed by
  digest. Cloud access uses short-lived OIDC credentials only.
- Significant decisions are recorded as [architecture decision records](https://github.com/FreeSecDev/ansible-linux-compliance/tree/main/docs/adr)
  before they are implemented.
- Every change is independently reviewed and passes the full CI gate in a merge queue before it reaches `main`.

[Contributing](https://github.com/FreeSecDev/ansible-linux-compliance/blob/main/CONTRIBUTING.md) ·
[Contributor rules](https://github.com/FreeSecDev/ansible-linux-compliance/blob/main/AGENTS.md) ·
[Documentation](https://github.com/FreeSecDev/ansible-linux-compliance/tree/main/docs) ·
[Code of conduct](https://github.com/FreeSecDev/ansible-linux-compliance/blob/main/CODE_OF_CONDUCT.md)

## Security

Report vulnerabilities privately through
[GitHub security advisories](https://github.com/FreeSecDev/ansible-linux-compliance/security/advisories/new), not in a
public issue. See the [security policy](https://github.com/FreeSecDev/ansible-linux-compliance/blob/main/SECURITY.md).
