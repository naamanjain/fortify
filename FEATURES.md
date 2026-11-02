# Features

Everything is labelled **implemented**, **implemented and verified**, or **planned**. Nothing in the planned column is presented as a capability, and nothing in the implemented columns is presented as production-ready.

## Implemented and verified

| Area | Feature | How it was verified |
|---|---|---|
| **Analysis** | Reproducible synthetic longitudinal environment — 500 personnel over 180 days | Deterministic regeneration; identical outputs from the same source |
| | Data-quality and validation gates that run before scoring | Behaviour tests; quality counts surfaced in the reviewer header |
| | Temporal features plus personal and contextual baselines | Behaviour tests; shown per case in the person profile |
| | Meaningful-change and persistence policy, duplicate suppression, recovery logic | Behaviour tests over the alert policy; queue admission counts |
| | Ranking model as one signal, with baseline comparison against a simple rule | Evaluation published: rule F1 0.614 vs model 0.322 at the deployed point |
| | Explanation records with computed value, reference, window and admission rule | Behaviour tests; rendered per case with provenance |
| | Explicit "no contributing factors recorded" state | Verified in the browser: the absence is shown, never filled in |
| **Review workflow** | Bounded, prioritised review queue with capacity budgeting | Behaviour tests; live queue read over HTTP with the contract the UI depends on |
| | Case states: acknowledge, review, support planned/completed, close, reopen | 43 hosted-runtime checks including invalid transitions that change nothing |
| | Reason codes recorded with each transition | In-browser workflow: action recorded, visible in history |
| | Concurrency control — a second reviewer cannot silently overwrite the first | Database row-locking test; conflicting expected-state write refused |
| | Support options mapped deterministically and non-punitively | Behaviour tests; policy version carried with the recommendation |
| | Operational feasibility — can support be scheduled now, with adjustment, or constrained | Rendered on the person profile; computed from the demo constraints |
| | Feedback capture with idempotent resubmission | HTTP gate: replayed submission does not duplicate the record |
| | Follow-up scheduling, due/overdue derivation, completion | In-browser: follow-up view renders; scheduling is a supported transition |
| **Access and privacy** | Server-side role × access purpose × unit scope | Forged identity headers grant nothing; scoped accounts see strictly fewer cases |
| | Purpose declaration that never grants reach | Refusal measured over HTTP and in the browser |
| | Out-of-scope individuals answered "not found", identical to nonexistent | HTTP gate asserts the indistinguishable response |
| | Personnel identifiers stripped from the governance view | Behaviour tests; verified in the rendered audit table |
| | Voluntary self-report fields excluded from the analysis path by construction | Exporter refuses to run if such a column appears in a selected output |
| | Identifier redaction in logs | Behaviour test on the log shape |
| **Accountability** | Hash-chained audit trail with chain verification at readiness | Tamper test: altered event → chain invalid → platform reports unhealthy → restored → healthy |
| | Governance view with chain status and access-purpose summary | Rendered to an audit-purpose account in the browser |
| **Hosted pilot runtime** | Real authenticated sessions: server-issued HttpOnly cookie, session restore, sign-out revocation | 17 live-HTTP checks plus 30 in-browser steps |
| | Cross-request forgery protection on every mutation | Missing and invalid tokens both refused; reads unaffected |
| | Sign-in throttling with per-client budget and lockout | Measured on an isolated process: 429 with a retry-after |
| | Graceful shutdown that closes the connection pool | Verified on Linux with the production signal: exit 0, handler logged, connections released |
| | Verified precomputed data bundle with per-file checksums and source commit | Runtime refuses to serve a bundle whose bytes do not match its manifest |
| | Strict data validation — no non-finite values, no ambiguous duplicate columns | Regenerated bundle verified file-by-file |
| **Interfaces** | Attention queue, person profile, personnel directory, follow-ups, reviews, aggregate unit command, trends, governance | All rendered and exercised in a real browser at desktop width |
| | Restricted-view states for accounts that lack a purpose | Measured: no navigation entry offered, direct URL refused, no data rendered |
| | Aggregate-first leadership view | Command account sees unit rows only; search returns units, never people |

## Implemented, but explicitly not production-verified

| Feature | What is missing |
|---|---|
| Deployment procedure for the hosted pilot | Written and reviewed, but **never executed** against the target hosting account |
| Database portability | Verified on MariaDB 10.11.19 and, for the reference implementation, PostgreSQL 16.15. **Oracle MySQL 8 has not been tested** |
| Live identity integration | Session authentication is real and server-side; a departmental single-sign-on provider, provisioning, revocation and multi-factor lifecycle are not |
| Backup and recovery | A scripted backup → mutate → restore → verify drill passed on a disposable database; scheduled, off-site, immutable backups and measured recovery objectives are not in place |
| Phone-width layout | The application shell at narrow viewports is **broken** — measured: the content column collapses to zero width below 520 px. Desktop and tablet widths render correctly |

## Planned, not implemented

| Capability | Status |
|---|---|
| Authorised real-data ingestion under an approved data contract | Planned — requires institutional authorisation before it is appropriate to build |
| Departmental identity provider integration with claim and scope policy | Planned |
| Prospective validation on authorised data against simple baselines | Planned — this is the gate before any pilot |
| Human-factors evaluation for stigma, over-reliance and alert fatigue | Planned |
| Retention, deletion and subject-access handling under a real policy owner | Planned |
| Managed hosting with failover, monitoring and tested recovery | Planned |
| Independent penetration testing with external tooling | Not done — no such tooling was available on the development machine |
| Hardware-backed key protection (secure enclave, HSM, attestation) | Not implemented; nothing in the current audit chain should be read as hardware-anchored |

## Deliberately absent

These are not gaps. They are the boundaries that define the product:

- No diagnosis, screening or clinical assessment of any kind.
- No disciplinary, performance or adverse personnel decision support.
- No autonomous action — no automatic notification to a supervisor, no automatic case closure, no automatic escalation.
- No use of missing voluntary self-reports as a signal of distress.
- No individual-level output to command-purpose accounts.
- No presentation of precomputed analysis as live inference.
