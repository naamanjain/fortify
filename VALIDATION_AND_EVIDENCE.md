# Validation and Evidence

Every figure on this page was measured on this project, on this machine, with the stated environment. Where something was **not** tested, it is listed as not tested rather than omitted.

## 1. What the evaluation is actually predicting

The demonstration environment is synthetic: a reproducible longitudinal world of **500 personnel over 180 days**, producing **90,000 person-day decisions**. The model's target is *whether an observed future wellness report occurs within a 7-day horizon* in that synthetic world — not "distress", not a clinical outcome, and not anything measured on real people.

From the 90,000 person-days, **26,085** had a usable labelled prediction date; **63,915** were excluded because no future report was observed within the horizon. Chronological split: **15,662 train / 5,325 validation / 5,098 test**. Chronological, not random, so the evaluation cannot peek at the future.

## 2. Discrimination metrics — synthetic prototype only

| Metric | Value |
|---|---|
| Validation ROC AUC | **0.74574** |
| Test ROC AUC | **0.76340** |
| Test average precision | **0.57909** |

These are prototype evaluation metrics on generated data. They do **not** establish clinical, occupational or operational effectiveness, and they are not evidence about real personnel.

## 3. The comparison that shaped the architecture

Precision at fixed review capacity — how often the top *k* % of person-days a strategy raises are ones that actually need attention:

| Strategy | Top 1 % | Top 2 % | Top 5 % | Top 10 % |
|---|---:|---:|---:|---:|
| Simple operational rule | **0.9412** | **0.8824** | **0.7961** | **0.7529** |
| Unfitted temporal score | 0.8235 | 0.7745 | 0.7333 | 0.7020 |
| Logistic regression | 0.7255 | 0.7157 | 0.7059 | 0.6451 |
| L2 logistic regression | 0.7059 | 0.6667 | 0.6824 | 0.6745 |

At the deployed operating point, the trained model's **F1 was 0.322** against the simple rule's **0.614**. Loosening the model's threshold raised its F1 to about **0.633**, but required flagging roughly **54 % of person-days** — a review burden no welfare function could carry.

**The decision taken from this:** the model remains one ranking signal inside a bounded, human-owned review queue. It is not demonstrated to be consistently superior to a simple rule, and the system is not built as though it were. The operating threshold is selected on the validation window closest to deployment under a precision floor, and both candidate thresholds are recorded rather than quietly chosen.

## 4. Behavioural verification

| Gate | Environment | Result |
|---|---|---|
| Analysis-platform behaviour suite | Python 3.13, isolated scratch database | **450 passed, 4 skipped**, exit code 0, 111 s. The demonstration store was byte-identical after the run (SHA-256 verified before and after) |
| Hosted-runtime unit checks | Node.js v24.18.0 | **14 passed** — includes parity guards that parse the transition graph and the role/purpose matrix out of the reference implementation and fail if the two drift, plus fixed case-identifier vectors |
| Database integration checks | **MariaDB 10.11.19** (disposable container) | **11 passed** — migrations apply and re-apply idempotently; InnoDB, foreign keys and uniqueness constraints hold; provisioning refuses invalid role/purpose pairs; sessions and audit rows persist; the queue is scope-filtered; a full workflow with history and hash chain completes; single-writer concurrency; throttling; tamper detection; a backup → mutate → restore → verify drill |
| Live HTTP runtime checks | real service process on loopback, real cookies | **17 passed** — readiness layering, 401 for anonymous callers, forged identity headers granting nothing, cookie attributes, uniform login failure message, purpose-declaration refusal, missing and invalid forgery tokens, the queue read contract, an authorised workflow persisting into a new session, invalid transitions changing nothing, cross-scope reads and writes answered identically to unknown records, scoped counts strictly smaller than all-units counts, auditor versus welfare refusals, aggregate search returning units only, sign-out revocation, per-client throttling with a retry hint, malformed body, oversized body refused at 413, a tampered audit row forcing the readiness endpoint to 503 and healing back to 200, and startup refusing a checksum-mismatched bundle and a missing database host |
| Graceful-shutdown check | **node:20-slim v20.20.2** in a Linux container, stopped with the signal the production host sends | **1 passed** — exit code 0 (not a kill after the grace period), shutdown handler logged the triggering signal, and the connections the process opened were gone from the database server's session list |
| Frontend checks | TypeScript build and unit suite | **29 passed**, type check clean, production build clean |
| Live browser verification | headless Chrome driving the application at **https://fortify.urbantechx.in** | **37 of 37 steps passed** across welfare reviewer, commander and auditor journeys; reviewer workflow state survived a hard reload and a new sign-in; audit chain reported Verified; responsive layout measured at 390, 414, 768 and 1440 px |
| Reference-implementation PostgreSQL path | **PostgreSQL 16.15**, throwaway container | **3 passed** — versioned migration runner creates the schema, the partial unique index enforces feedback idempotency, a real transition writes its audit row, two concurrent writers serialise on the row lock |

## 5. What the in-browser verification actually covered

Not a mock: the production frontend build, served by the pilot runtime itself, driven through real sign-in and real workflow actions.

| Screen / behaviour | What was measured |
|---|---|
| Unauthenticated load | Sign-in screen rendered; **zero** case data in the document |
| Wrong password | One uniform message; no indication whether the account exists |
| Successful sign-in | Identity shown comes from the server's session answer, including role, purpose and unit scope |
| Session storage | No identity or token in browser storage; the session cookie is unreadable to script |
| Return to requested route | A protected route typed while signed out is honoured after signing in |
| Queue and directory | 20 case cards rendered; 500 personnel rows; evidence affordance present on every case |
| **Workflow through the interface** | Clicking a review action changed the visible state; the change survived a full page reload; the action appeared in the visible history; **a brand-new sign-in read the same persisted state** |
| Restricted purpose | An aggregate-only account was offered 3 of 8 navigation entries; a direct URL to the welfare queue produced a restricted message with **zero** case cards; the audit view refused a welfare account |
| Sign-out | Protected routes returned the sign-in screen with no data |
| Responsive layout | Hosted layout measured at 390, 414, 768 and 1440 px for welfare reviewer and aggregate leader; no measured layout failures |
| Console and network | No uncaught errors; the only logged failures were the deliberate 401/403 refusals the run was testing for |

## 6. Data provenance and integrity

The hosted runtime serves a compact, **precomputed synthetic bundle** rather than re-deriving analysis on demand.

| Property | Value |
|---|---|
| Files | 10, totalling **10,711,215 bytes** |
| Contents | 183 admitted cases, 500 personnel, 250 explanation records, per-unit summaries and trends, person timelines, dashboard aggregates |
| As-of date | 2026-06-29 |
| Provenance | The manifest records the source commit of the code that generated it |
| Integrity | Per-file SHA-256 checksums, re-verified independently by the runtime at startup; a mismatch is a startup failure, not a degraded page |
| Determinism | Re-exporting from the same inputs reproduced 9 of 10 files **byte-identically**; the tenth differed only in a descriptive sentence about where one view is served from |
| Strictness | Every file re-parsed with a parser that **rejects** non-finite values: no `NaN`, no `Infinity`. No ambiguous duplicate column suffixes from the analysis joins |
| Privacy | Explicit column allow-lists; the exporter refuses to run if a voluntary wellness or self-report column appears in a selected output |

## 7. Security properties that were tested, not asserted

| Property | How it was demonstrated |
|---|---|
| Identity comes from the server, not the client | Forged role headers were sent to the live service and granted nothing |
| A purpose declaration grants nothing | A reviewer declaring a purpose the account does not hold was refused, over HTTP and in the browser |
| Scope is enforced where it counts | A unit-scoped account saw strictly fewer cases than an all-units account, and out-of-scope people resolved to the same answer as nonexistent ones |
| Forgery protection on mutations | Missing and invalid tokens were both refused; reads were unaffected |
| Sessions end when they should | Sign-out revoked server-side; expired and revoked sessions were refused |
| Brute force is bounded | Per-client sign-in budget produced a 429 with a retry hint, measured on an isolated process so the test could not lock itself out |
| Input is hostile by default | Malformed bodies produced client errors; an oversized body was refused at 413 by the parser, not buffered |
| The audit record can be challenged | A stored event was deliberately altered; the readiness endpoint reported unhealthy and the governance view reported the chain invalid with a reason; restoring the value returned it to healthy |
| Startup fails loudly | A checksum-mismatched bundle and a missing database configuration each stopped the process with a specific error rather than starting a partial service |
| Shutdown is clean | The production signal produced exit code 0 with the handler logged and the database connections released |

## 8. Not tested or not yet established

Stated so that no reader infers coverage that does not exist.

- **Oracle MySQL 8** — never started. The hosted pilot and its integration evidence use MariaDB 10.11.19 (plus PostgreSQL 16.15 for the Python reference implementation only).
- **Restorable live-host backup** — the hosting deployment is running, but a database export/restore artifact from the cPanel account was not obtained. The backup → mutate → restore → verify drill was performed only against a disposable MariaDB database.
- **Independent penetration testing** — no external security tooling (vulnerability scanner, SQL-map style tester, network scanner, dedicated audit frameworks) was available on the development machine, so none ran.
- **Dependency vulnerability advisories** — no advisory database was consulted for either runtime, so no claim is made about upstream CVEs.
- **Intermittent HTTP 503 cause (F-20)** — two 503 responses were observed during an earlier hosted browser run, clustered around Passenger restart markers. They were not reproduced in the subsequent 37/37 run or controlled restart; the cause remains unknown and the finding stays open.
- **Accessibility conformance** — reviewed in earlier work and not re-certified by this release; the hosted sign-in and restricted states were exercised in the browser but not against an automated accessibility pass.
- **Real-world validity of any kind** — no authorised data, no institutional approval, no prospective study, no human-factors evaluation.
- **Graceful shutdown on the development platform** — measured to be impossible to exercise there because the platform delivers no interceptable signal to a spawned service process; verified on Linux instead, which is where the production host runs.
