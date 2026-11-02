# Innovation and Differentiation

Each claim below is stated as **what is usually done → what FORTIFY does → why it matters**, and is tied to something that was actually built and measured. Nothing here is a roadmap item dressed as a feature.

---

## 1. It published the result where the model lost, and restructured around it

**Usually done** — a threshold is retuned until the learned model beats the baseline, and the tuned number is reported.

**What FORTIFY does** — at the deployed operating point, a simple operational rule scored **F1 0.614** against the trained model's **0.322**. Loosening the model's threshold raised it to roughly **0.633**, but required flagging about **54 % of person-days**. Both numbers were kept, the comparison was made against the rule rather than against nothing, and the conclusion drawn was that the model is *not demonstrated to be consistently superior to a simple rule*. The architecture was changed accordingly: the model is one ranking signal inside a bounded, human-owned queue, not a decision-maker.

**Why it matters** — this is the decision that determines whether the tool is trustworthy. A welfare system built on a suppressed baseline comparison inherits a false premise. Publishing the uncomfortable result and demoting the model is the stronger engineering claim, and it is the reason the queue has a persistence policy and a capacity budget instead of a score cut-off.

## 2. Review capacity as a first-class constraint

**Usually done** — rank everyone, let the organisation cope with whatever falls out.

**What FORTIFY does** — the queue is budgeted. Items that would not realistically be read are suppressed, and the suppression is recorded and visible rather than hidden in a longer list.

**Why it matters** — alert burden is the mechanism by which these systems fail in practice. A tool that cannot say "we are showing you 182 of 90,000 person-days because that is what can be reviewed" is not a decision-support tool; it is a noise generator with a UI.

## 3. A persistence policy instead of a threshold

**Usually done** — raise anything that crosses a line today.

**What FORTIFY does** — a change must be **material and persist** before it becomes a case. Duplicate suppression stops the same person re-entering the queue every day; recovery logic retires items whose pattern resolved on its own; closure and reopening are explicit states.

**Why it matters** — welfare-relevant change is a trajectory, not a datapoint. One bad week during an exercise is not a concern; a slow drift over a month is exactly the thing that would otherwise go unnoticed.

## 4. Explanations carry provenance — or admit absence

**Usually done** — a top-k feature list, or a generated sentence that sounds like a reason.

**What FORTIFY does** — each displayed factor traces to a computed value, the reference it was compared against, the time window it was measured over, and the admission rule that let it through, along with the policy version that produced it. Where no evidence was recorded, the interface says so.

**Why it matters** — a reviewer must be able to *disagree* with a signal on evidence. That is only possible if the evidence is real. An explanation that can be fabricated is worse than none, because it manufactures trust in a specific direction.

## 5. Personal baselines before population norms

**Usually done** — compare an individual to a cohort average.

**What FORTIFY does** — compare each person to their own recent normal, with unit-level operational context alongside, so a demanding tempo that is ordinary for that role does not read as a concern.

**Why it matters** — in uniformed service the population average is an artefact of the roster structure, not a healthy baseline. Without this, the queue fills with people who have always worked that way.

## 6. Three-dimensional, server-side access with a purpose declaration that grants nothing

**Usually done** — role-based access, often decided by the client, with "purpose of use" recorded in a policy document rather than enforced in code.

**What FORTIFY does** — role × access purpose × unit scope, resolved on the server from the stored account on every request. A reviewer may declare a purpose; a declaration never grants reach. Out-of-scope individuals are answered "not found" — identical to the response for someone who does not exist, so the interface cannot be walked to map who serves where.

**Why it matters** — this is the difference between a privacy claim and a privacy mechanism. It was measured, not asserted: forged identity headers grant nothing, a unit-scoped account sees strictly fewer cases than an all-units account, an auditor is refused welfare detail, and an aggregate-only account is refused individual records.

## 7. The voluntary self-report firewall

**Usually done** — collect wellness questionnaires and feed them into the model, because they are informative.

**What FORTIFY does** — raw voluntary wellness and self-report fields are excluded from the analysis path by construction, with an explicit refusal if such a column appears in an output being selected. A **missing** self-report is never treated as evidence of distress.

**Why it matters** — this is the single design decision that keeps the tool from punishing people who decline to disclose. Without it, "voluntary" support data becomes an implicit obligation, and the honest answer to a questionnaire becomes risky.

## 8. A tamper-evident trail that can make the platform report itself unhealthy

**Usually done** — an append-only log that is trusted because nobody said otherwise.

**What FORTIFY does** — audit events are hash-chained, the chain is verified as part of the platform's own readiness check, and a broken chain changes the system's reported state. A governance view exposes the trail and its verification status to an audit-purpose account.

**Why it matters** — accountability for a welfare tool is only real if the record of what was done can be independently challenged. This was tested by deliberately altering a stored event and observing the platform report itself unhealthy, then restore and report healthy again.

## 9. Separating analysis from delivery, honestly

**Usually done** — a service that computes whatever it shows, at request time.

**What FORTIFY does** — the hosted pilot serves a **verified precomputed** dataset whose manifest records its source commit and per-file checksums, and the process refuses to start if the bytes do not match. It performs no model inference at request time, and does not imply that it does.

**Why it matters** — the numbers a reviewer acts on become traceable to a specific, reproducible dataset. It also removes a whole class of silent failure: a partially uploaded or truncated dataset is a startup error, not a page full of plausible-looking nothing.

## 10. Verification that ran against real processes, not just functions

**Usually done** — unit tests over in-memory objects, and a claim that the system "works".

**What FORTIFY does** — the hosted runtime was started as a real service and driven over HTTP with real cookies; the production frontend build was served by that service and driven in a real browser; the database checks ran against a real MariaDB server; and the graceful-shutdown path was verified on Linux with the signal the production host actually sends, after measuring that the equivalent check is impossible on the development platform.

**Why it matters** — the gap between "the functions pass" and "the deployed thing works" is where pilots die. Where something could not be verified, the project says so instead of generalising from what was.

---

## What is *not* claimed

No real-world predictive validity. No clinical or occupational effectiveness. No institutional adoption, endorsement, deployment or procurement status. No autonomous decision-making. No claim that a welfare concern was prevented or a life improved. The evaluation environment is synthetic and reproducible, and that is the honest ceiling of what this prototype can currently assert.
