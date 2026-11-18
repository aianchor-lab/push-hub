# ADR-0002: Build a Working Product Before Promoting It

* **Status:** Accepted
* **Date:** 2026-11-13 (merged into `main` on 2026-11-17 in #42)

## Context

The original plan was to validate demand first with a landing page, a survey, and a pre-order button, and to start development only after that validation. With revenue no longer the near-term goal ([ADR-0001](0001-apache-2-0-license.md)), a wrong guess costs only development time. A project with no code also gets a weak reception on Show HN and similar communities, and a first impression is hard to repeat.

## Decision

* Build the product first, and promote it once it works.
* Drop the survey and the demand-validation wording. GitHub stars and issues remain the only signal before release.

## Alternatives Considered

* **Validate demand first:** it was meant to test willingness to pay, which is no longer the question.

## Consequences

* Feedback comes from people who tried the product, which is more useful than survey answers.
* Interest before release is measured only by stars and issues, which do not identify buyers.
* Issues #13, #17, #19, #20, and #21 were closed on 2026-11-17 because they assumed the old plan.

## Evidence

* Pull requests #41 and #42
