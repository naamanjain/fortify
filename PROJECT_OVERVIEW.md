# Project Overview

## What FORTIFY is

FORTIFY — **Force Operational Resilience & Timely Intervention Framework** — is a research and demonstration prototype for **welfare decision support inside a uniformed-service organisation**. It takes operational-pattern information the organisation is already entitled to use for welfare purposes, compares each person with their own recent normal, raises a case only when a change is both material and persistent, and hands the reviewer the recorded reasons behind the signal along with support options that are actually schedulable.

It is deliberately **not** a diagnosis engine, a performance-monitoring tool or an automated decision system. Its unit of output is not a verdict but a *reviewable case with evidence*, and its unit of completion is a human decision recorded against that case.

## Who it is for

| Audience | What they get |
|---|---|
| Welfare officers and duty counsel | A prioritised, scoped queue of people worth a timely look, each with the factors behind it and a support workflow that carries through to follow-up |
| Command leadership | Aggregate, unit-level operational pressure — counts and trends — without individual welfare records |
| Security and audit functions | A tamper-evident trail of who accessed or changed what, in which role and for what purpose, plus a chain-verification status the platform reports about itself |
| Policy and governance owners | A concrete, inspectable artefact for the questions they actually have to answer: what data is in scope, who may see an individual, what the system is forbidden to do, and what evidence would justify a pilot |

## The shape of the work

Two things were built, and they are described here conceptually because the implementation is not published in this repository.

1. **An analysis and workflow platform** — the data-quality gates, temporal features, personal and contextual baselines, the ranking signal, the meaningful-change and persistence policy, explanation provenance, support feasibility, case workflow and the hash-chained audit trail.
2. **A hosted pilot runtime** — a small, separately built service that serves the *verified precomputed* analysis output to reviewers over an authenticated session, stores reviewer actions in a relational database, and refuses to start if the data it is serving does not match its own checksums. It performs no model inference at request time; it is a delivery and record-keeping surface for analysis that was done elsewhere, reproducibly.

The split matters for honesty: it means the numbers a reviewer sees are traceable to a specific generated dataset and commit, not re-derived on the fly by an opaque service.

## Current status

| Dimension | Status |
|---|---|
| Implementation | Complete enough to run end to end and to be verified end to end |
| Data | Synthetic only — a reproducible 500-personnel, 180-day demonstration environment |
| Verification | 450 backend behaviour tests passed (4 skipped); 42 pilot API tests passed (1 skipped), 29 frontend tests passed, and 37/37 hosted browser steps passed across all three demo roles; details in [VALIDATION_AND_EVIDENCE.md](VALIDATION_AND_EVIDENCE.md) |
| Deployment | **Deployed** to the Razorhost account at https://fortify.urbantechx.in using Node.js 20.20.2 and MariaDB 10.11.19; the live application, role journeys and workflow persistence were verified |
| Real-world validation | **None.** No authorised data, no institutional approval, no prospective study |
| Open-source release | **No.** This repository is presentation material; see [RIGHTS_NOTICE.md](RIGHTS_NOTICE.md) |

## What makes it worth evaluating

The interesting part of FORTIFY is not that it trained a model. It is that the project **published where the model lost** and restructured the system around that result: a simple operational rule outperformed the learned signal at the deployed operating point (F1 0.614 against 0.322), and the honest response was to demote the model to one ranking input inside a bounded, human-owned queue with explicit review-capacity budgeting, rather than to tune the threshold until the number looked better. Everything else in the design — persistence policy, provenance-carrying explanations, purpose-limited access, tamper-evident audit, the voluntary-self-report firewall — follows from the same stance: a welfare tool is judged by whether a human can trust and act on what it says, and by what it refuses to do.

## Reading order

Start with [PROBLEM_STATEMENT.md](PROBLEM_STATEMENT.md) for the gap, then [SOLUTION_OVERVIEW.md](SOLUTION_OVERVIEW.md) for the approach, [INNOVATION.md](INNOVATION.md) for what is different, [VALIDATION_AND_EVIDENCE.md](VALIDATION_AND_EVIDENCE.md) for what was actually measured, and [LIMITATIONS_AND_FUTURE_SCOPE.md](LIMITATIONS_AND_FUTURE_SCOPE.md) for what was not. [DEMONSTRATION_GUIDE.md](DEMONSTRATION_GUIDE.md) walks the screens.
