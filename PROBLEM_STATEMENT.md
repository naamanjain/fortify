# Problem Statement

## The situation

Uniformed personnel work under demanding schedules, irregular duties, deployments, limited recovery time, transfers, and separation from family or support networks. Those conditions are measurable — the organisation records them for operational reasons long before anyone thinks about welfare.

Welfare concerns, meanwhile, tend to surface **late and socially**: when a supervisor notices something, when a colleague raises it, when a person finally makes a voluntary self-report, or when an incident has already happened. By then, the point at which proportionate, low-cost support would have helped has often passed.

The information needed to notice earlier usually exists. It is just **distributed across systems that were never designed to look at welfare**, and nobody is accountable for joining it up in a way a welfare officer could act on.

## The four gaps FORTIFY addresses

**1. Latency.** Detection depends on someone raising something. Operational data could indicate a sustained change in a person's pattern weeks before a referral would, but no one owns that reading, and doing it by hand across systems is not feasible at scale.

**2. Decontextualisation.** Naive monitoring compares a person to an average. In uniformed service the average is meaningless: a demanding tempo that is normal for that role and that unit is not a welfare signal, and a quiet period during a low-tempo window is not recovery. Without personal and contextual baselines, the output is either obvious or wrong.

**3. Unexplained alerts.** A ranked list of names with a score attached is not actionable for a welfare reviewer. The reviewer needs to know *what specifically changed, measured against what, over what window, and why the policy considered that worth raising* — and needs to be told plainly when no evidence was recorded, rather than being handed a plausible-sounding narrative.

**4. Missing follow-through.** Even a good alert fails if nothing tracks the outcome. Was support offered? Was it schedulable given the operational picture? Was it delivered? Did someone agree to check back in two weeks, and did that check actually happen? Most signal tools stop at the prediction; the welfare value is entirely in what happens afterwards.

## Why the obvious alternative is unacceptable

The same operational data can power indiscriminate surveillance, and that is the failure mode this project treats as the real risk — not a hypothetical one.

| Harm | How it arises | What FORTIFY does about it structurally |
|---|---|---|
| **Stigma** | A person is flagged, and the flag becomes a label that follows them | Signals are scoped to welfare-support roles; leadership sees aggregates, not individuals; nothing about a case is visible to a reviewer without authorisation |
| **Punitive use** | A welfare signal is repurposed for discipline or performance management | The support mapping is deterministic and non-punitive by construction; adverse personnel decisions are out of scope, and the audit trail records the declared purpose of every access |
| **Alert fatigue** | Everything that deviates is raised, so nothing gets read | A change must be material **and** persist; duplicates are suppressed; recovery logic closes items that resolved themselves; the queue is budgeted to what can actually be reviewed |
| **Over-reliance** | A score is treated as a verdict | The model is one ranking input, not a decision-maker; every case carries its contributing factors so a human can disagree with the ranking on evidence |
| **Coerced disclosure** | Voluntary wellness answers become an input to being "watched" | Raw voluntary self-report responses are excluded from the analysis path by construction, and a **missing** self-report is never treated as evidence of distress |
| **Function creep** | A tool built for support quietly becomes a monitoring tool | Access requires role, purpose and unit scope on every request; purpose is a declaration of why the reviewer is asking, never a grant of extra reach |

## The evaluation problem nobody solves

Welfare-support systems are usually evaluated as classification problems: precision, recall, AUC. That framing hides the two things that decide whether such a system is usable.

The first is **burden**. A detector that finds more cases is not better if it requires flagging half the workforce; the review capacity is finite and belongs in the objective function. FORTIFY treats capacity as an explicit constraint and records what it suppressed as a consequence.

The second is **whether the learned model earns its place at all**. On a reproducible synthetic world, a simple operational rule beat the trained model at the deployed operating point (F1 0.614 against 0.322), and loosening the model's threshold to close the gap pushed its flag rate to roughly 54 % of person-days — operationally meaningless. Most projects would have re-tuned and reported the better number. The honest conclusion is that the model is not demonstrated to be consistently superior to a rule, and the architecture was changed to reflect that.

## What would make this a real problem-solver

Not more modelling. Institutional work: an authorised data contract with defined provenance and freshness; a real identity provider with provisioning and revocation; an agreed scope policy for transfers, multi-unit and acting roles; a privacy and security assessment; prospective validation on authorised data with stop criteria agreed in advance; and a human-factors study of whether reviewers actually give better, timelier support because of it — measured for stigma, over-reliance and fatigue, not just accuracy. That path is set out in [LIMITATIONS_AND_FUTURE_SCOPE.md](LIMITATIONS_AND_FUTURE_SCOPE.md).
