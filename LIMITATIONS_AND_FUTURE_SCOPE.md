# Limitations and Future Scope

A welfare decision-support tool earns trust by being precise about what it cannot do. This page is the honest list.

## Fundamental limits

**Synthetic data only.** Everything shown here was produced in a reproducible generated world of 500 personnel over 180 days. No real personnel, departmental or wellness records are involved, and none of the figures describe real people.

**No real-world predictive validation.** The metrics in [VALIDATION_AND_EVIDENCE.md](VALIDATION_AND_EVIDENCE.md) are prototype evaluation numbers on generated data. They do not establish clinical, occupational or operational effectiveness, and they say nothing about whether real welfare outcomes improve.

**The prediction target is narrow and synthetic.** The model predicts whether an *observed future wellness report* occurs within a 7-day horizon in the generated world. That is not distress, not risk, and not a welfare need — it is a reporting event in a simulation.

**Explanations are associative, not causal.** A contributing factor describes an operational pattern associated with a higher signal. It does not explain why a person feels as they do, and must not be read as doing so.

**The learned model is not demonstrated to beat a simple rule.** At the deployed operating point a straightforward operational rule scored F1 0.614 against the model's 0.322, and precision at every review-capacity level favoured the rule. The system is designed so this does not matter much — the model is one ranking input, not an authority — but it is a real limit on what the modelling contributes today.

## Engineering and operational limits

| Area | Current state |
|---|---|
| **Deployment** | **Not deployed.** No live instance has been operated. The hosting procedure is written and reviewed but unexecuted, so nothing is verified about the target platform's runtime versions, application root, TLS, disk limits or process manager behaviour |
| **Database portability** | Verified on MariaDB 10.11.19 and, for the reference implementation, PostgreSQL 16.15. **Oracle MySQL 8 has never been tested** — the schema avoids vendor-specific syntax, but that is a design argument, not evidence |
| **Authentication** | The hosted pilot path uses real server-side sessions. The local demonstration mode still trusts caller-supplied role and purpose headers, which is acceptable only for a local synthetic demo and is stated as such in the interface |
| **Identity integration** | No departmental identity provider is connected. Token validation was exercised with locally generated keys only; provisioning, revocation, multi-factor and session lifecycle under a real provider are unverified |
| **Sign-in throttling** | The budget is counted **per source IP**. Reviewers sharing one office egress or VPN exit can lock each other out — this was observed, not theorised, during verification. Keying the lockout on client and account is the durable fix |
| **Phone-width layout** | **Broken.** Measured at 414 px: the navigation column takes the full width and the content column collapses to zero width, so nothing is readable. Tablet (900 px) and desktop (1440 px) render correctly. No mobile screenshot is published because there is no working mobile view to photograph |
| **Accessibility** | Reviewed in earlier work, not re-certified by this release. The new sign-in and restricted states were exercised in a browser but not against an automated accessibility pass |
| **Dependency vulnerabilities** | No advisory database was consulted for either runtime, so no claim is made about upstream CVEs |
| **Independent security testing** | No external security tooling was available on the development machine; no scanner, fuzzer or penetration framework ran. The security properties in the evidence page are ones this project's own tests demonstrate |
| **Backups and recovery** | A scripted backup → mutate → restore → verify drill passed against a disposable database. Scheduled, off-site, immutable backups, retention policy and measured recovery objectives are not in place |
| **Hardware-backed key protection** | None. There is no secure enclave, hardware security module or remote attestation, and the audit chain's anchoring should not be read as hardware-protected |
| **Data freshness in the demonstration set** | The synthetic dataset's most recent case day sits well before its as-of date, so several cases carry an explicit "older data day" chip. That is honest labelling of a generated dataset, not a production staleness profile |

## What would have to happen before any real pilot

These are not engineering tasks; they are preconditions, and most of them are institutional rather than technical.

1. **A sponsor and a policy owner** who define purpose, decision rights, access rules, escalation, retention, prohibited use and — explicitly — that adverse personnel use is forbidden.
2. **An authorised data contract**: which sources may be read, by whom, with what provenance and freshness expectations, and how failures are handled.
3. **Real identity integration** with an authorised provider, including provisioning, revocation, and a claim policy the institution has agreed.
4. **Scope semantics agreed by the institution** — transfers, multi-unit service and acting roles are policy questions that no test suite can answer.
5. **A privacy and security assessment** covering access boundaries, key management, logging, retention, incident response and independent testing.
6. **Prospective validation on authorised data**, compared against simple baselines at realistic review capacity, measuring alert burden and context stability, with **stop criteria agreed before the pilot starts**.
7. **A human-factors evaluation**: does the interface actually help people give timely, proportionate support — measured for stigma, over-reliance, punitive interpretation and alert fatigue, not just accuracy.

## Future technical scope

Once those exist, the technical direction that follows naturally:

- **Capacity-aware prioritisation** as an explicit optimisation against the reviewer budget, rather than a threshold plus suppression.
- **Baseline quality measurement** — how well personal and contextual baselines separate ordinary operational tempo from genuine drift, evaluated on authorised data.
- **Retention and subject-access handling** under a real policy: what is kept, for how long, who may see the audit trail, and how a person exercises access.
- **Managed hosting** with tested failover and recovery objectives, replacing a scripted drill with an operated capability.
- **Narrow, consented pilots** with a defined evaluation plan and a defined way to turn it off.
- **Explainability hardening** — making the "evidence not recorded" state rarer by improving provenance capture, never by generating reasons.

## What is explicitly out of scope, permanently

Not roadmap items. Boundaries.

- Diagnosis or screening of any medical or psychiatric condition.
- Disciplinary, performance or other adverse personnel decisions.
- Autonomous action of any kind — no automatic escalation, notification or closure.
- Treating a missing voluntary self-report as evidence of distress.
- Individual-level visibility for command-purpose accounts.
- Indiscriminate monitoring of personnel.

If a future version of this project crosses any of those lines, it is no longer the system described here.
