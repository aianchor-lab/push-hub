# ADR-0013: Use the Developer Certificate of Origin Instead of a CLA

* **Status:** Accepted
* **Date:** 2026-11-18

## Context

The CLA was introduced under the BSL so that contributions could be sold under commercial licenses. Under the Apache License 2.0 ([ADR-0001](0001-apache-2-0-license.md)), anyone, including the project owner, may include contributions in proprietary or commercial products, and Section 5 of the license already applies its terms to submitted contributions. The CLA still required a legal review and a Corporate CLA before any external pull request could be accepted, and contributors often hesitate to sign CLAs.

What only a CLA provides:

1. The right to relicense contributions, for example to a more restrictive license.
2. A covenant not to exercise moral rights under Japanese copyright law.

## Decision

* Contributions to push-hub are made under the **Developer Certificate of Origin 1.1**. Each commit carries a `Signed-off-by` line (`git commit -s`), checked in CI.
* Contributions are licensed under the Apache License 2.0, the same as the project.
* `CLA.md` and CLA Assistant are removed. No Corporate CLA is needed; employees sign off their own commits.
* If a separately licensed `ee/` directory is created later, its contribution terms are decided then (a CLA, or no external contributions), as GitLab does.
* External pull requests can be accepted once the DCO check is in CI.

## Alternatives Considered

* **Keep the CLA and have it reviewed before v1.0:** costs money and time, and discourages contributors.
* **Keep the CLA and accept no pull requests at v1.0:** loses contributions at launch.

## Consequences

* **No relicensing of the core without every contributor's consent.** Accepted, since moving away from Apache 2.0 is not planned and is the hard direction anyway.
* **Patents:** covered by Sections 3 and 5 of the Apache License 2.0, not by the DCO.
* **Moral rights (Japan):** there is no covenant not to exercise them. The risk is assessed as low:
  * Article 20(2)(iii) of the Copyright Act excludes changes needed to run a program on a computer, or to run it more effectively, from the right of integrity.
  * The Agency for Cultural Affairs explains that bug fixes, version upgrades, and added functions are not subject to the right of integrity, given the nature of programs.
  * The Supreme Court ruling of 2001-02-13 found infringement for a game because the change altered its audiovisual expression and story, not because program code was modified.
  * Authorship stays visible in the git history.
* This is not legal advice. A one-time confirmation by counsel that Apache 2.0 with the DCO raises no practical moral-rights issue is optional.

## Evidence

* DCO in use:
  * Linux: [`Documentation/process/submitting-patches.rst`](https://github.com/torvalds/linux/blob/master/Documentation/process/submitting-patches.rst) ("Sign your work - the Developer's Certificate of Origin")
  * GitLab: [CONTRIBUTING.md](https://gitlab.com/gitlab-org/gitlab/-/blob/master/CONTRIBUTING.md) applies the DCO to contributions outside `/ee` and a CLA inside it. GitLab switched from a CLA to the DCO on 2017-11-01 ([announcement](https://about.gitlab.com/blog/gitlab-switches-to-dco-license/)).
  * Helm and Prometheus: DCO sign-off described in their `CONTRIBUTING.md`.
  * For comparison, Kubernetes and Grafana use CLAs.
* Trade-offs: [DCO vs CLA (2026)](https://tenthirtyam.org/dispatches/2026/04/08/dco-vs-cla-managing-contribution-agreements-in-open-source/), [Django forum discussion](https://forum.djangoproject.com/t/cla-vs-dco-for-django-contributors/42399)
* Japanese law:
  * Copyright Act Articles 20 and 59, current text from [e-Gov](https://laws.e-gov.go.jp/law/345AC0000000048)
  * Agency for Cultural Affairs, copyright text, "7. 著作者の権利", page 13 ([PDF](https://www.bunka.go.jp/seisaku/chosakuken/seidokaisetsu/pdf/93726501_06.pdf))
  * Supreme Court, 2001-02-13 ([ruling](https://www.courts.go.jp/assets/hanrei/hanrei-pdf-52268.pdf))
