# Security Policy

We take the security of push-hub seriously. Thank you for helping us keep the project and its users safe.

## Supported Versions

push-hub is currently in a pre-release stage. Security fixes are applied only to the latest code on the `main` branch.

| Version | Supported |
| ------- | --------- |
| `main` (latest) | :white_check_mark: |

A version support table will be published here after the first stable release.

## Reporting a Vulnerability

**Please do not report security vulnerabilities through public GitHub Issues, Pull Requests, or Discussions.**

Instead, please report them by email to:

**aianchor.lab@gmail.com**

Please use a subject line such as `[SECURITY] <short summary>`.

To help us triage and resolve the issue quickly, please include as much of the following as possible:

- Affected component, version, or commit hash
- Environment details (OS, deployment method, configuration, etc.)
- Type of vulnerability (e.g. authentication bypass, injection, information disclosure)
- Step-by-step instructions to reproduce the issue
- Proof-of-concept or exploit code, if available
- Expected impact and how an attacker might exploit the issue
- Whether and how you would like to be credited

## Response Process

push-hub is maintained by a small team, and security reports are handled on a best-effort basis. After you submit a report, we aim to follow these target timelines:

| Step | Target |
| ---- | ------ |
| Acknowledgement of your report | Within 7 days |
| Initial assessment and severity evaluation | Within 14 days |
| Fix or mitigation released | Within 90 days, depending on severity and complexity |

These timelines are targets, not guarantees. We will keep you informed of our progress and may ask for additional information during the investigation. If you have not received a response within 7 days, please send a follow-up email.

## Disclosure Policy

We follow a **coordinated disclosure** process:

1. We confirm the vulnerability and determine the affected versions.
2. We prepare a fix and release it as soon as possible.
3. We publish a [GitHub Security Advisory](https://docs.github.com/en/code-security/security-advisories) describing the issue and the fix.

We kindly ask that you do not disclose the vulnerability publicly until a fix has been released or 90 days have passed since your report, whichever comes first. If you believe an earlier disclosure is necessary, please discuss it with us first.

## Scope

### In Scope

- Source code in this repository
- Official Docker images and configuration files provided by this project

### Out of Scope

- Vulnerabilities in third-party dependencies (please report them to the upstream project; you are welcome to let us know as well)
- Issues caused by user misconfiguration or insecure deployment environments
- Denial of Service (DoS) attacks or load testing
- Social engineering or physical attacks
- Reports from automated scanners without a demonstrated, exploitable impact

## Safe Harbor

We will not pursue legal action against anyone who conducts security research in good faith and in accordance with this policy. This includes:

- Making a good-faith effort to avoid privacy violations, data destruction, and service disruption
- Only interacting with accounts and data you own or have explicit permission to access
- Reporting the vulnerability promptly and not exploiting it beyond what is necessary to demonstrate the issue

## Acknowledgements

We appreciate the efforts of security researchers. With your permission, we will credit you in the corresponding security advisory.
