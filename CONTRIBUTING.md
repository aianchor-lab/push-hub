# Contributing to push-hub

Thank you for your interest in contributing to push-hub!

Issues and pull requests are reviewed on a best-effort basis. We do not guarantee a reply, a fix, or a response time, and there is no support SLA.

## License

push-hub is **Open Source** software licensed under the [Apache License 2.0](LICENSE).

Copyright 2026 Atsushi Inoue (https://github.com/aianchor-lab)

Contributions are licensed under the Apache License 2.0, the same license as the project.

## Developer Certificate of Origin (DCO)

push-hub uses the [Developer Certificate of Origin 1.1](https://developercertificate.org/) instead of a Contributor License Agreement. By adding a `Signed-off-by` line to a commit, you certify that you wrote the contribution or otherwise have the right to submit it under the project's license. You keep the copyright in your contributions.

Sign off each commit with `-s`:

```sh
git commit -s -m "Fix retry delay calculation"
```

This adds a line with the name and email from your git configuration:

```
Signed-off-by: Your Name <you@example.com>
```

* **Every commit in a pull request needs a sign-off**, including small changes such as typo fixes. Pull requests with unsigned commits cannot be merged.
* To sign off commits you have already made, run `git rebase --signoff main` and force-push the branch.
* If you contribute as part of your job, make sure your employer allows you to contribute under the Apache License 2.0. No separate company agreement is needed.

## Reporting Bugs and Requesting Features

- Search existing [issues](https://github.com/aianchor-lab/push-hub/issues) before opening a new one.
- When reporting a bug, include steps to reproduce it, the expected and actual behavior, and your environment (OS, deployment method, version or commit hash).
- Please do not paste code you want included in the project into issues. Submit it as a pull request with a signed-off commit. Code posted in issues will not be incorporated into the project.

**Do not report security vulnerabilities through public issues.** Please follow [SECURITY.md](SECURITY.md) instead.

## Pull Requests

1. Fork the repository and create a branch from `main`.
2. Keep each pull request focused on a single change.
3. Describe what the change does and why it is needed. Link related issues where applicable.
4. Sign off every commit as described above.
