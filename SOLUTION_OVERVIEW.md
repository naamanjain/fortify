# Solution Overview

## The idea in one line

Turn operational data the organisation already holds into a **small, explainable, scoped queue of people worth a timely welfare look**, and carry each case through support, feedback and follow-up under a human decision that is recorded so it can be audited.

## Step by step

### 1. Authorised operational information

Duty schedule, rest opportunity, deployment and unit context — the signals an organisation is already entitled to hold for welfare-support purposes. Personnel identity does not travel through the analysis path further than it needs to; the analysis world is described in pseudonymous person-day terms.

### 2. Validation and preparation

Completeness, plausibility and quality checks run **before** anything is scored. Missing values stay missing and are reported as missing rather than filled with a guess. A quality gate that finds nothing wrong says so; a gate that finds something raises the count into the reviewer's header, because a signal computed on bad data is not a signal.

### 3. Personal and contextual baselines

Each person is compared with **their own recent normal**, with unit-level context alongside. This is what stops a demanding tempo that is ordinary for a role from reading as a concern, and what stops a quiet period from being read as recovery when it is unusual for that individual.

### 4. Welfare signal and persistence policy

A change must be **material and persist** to be raised. One difficult day does not open a case. Duplicate suppression stops the same person occupying the queue repeatedly, and recovery logic retires items whose pattern resolved on its own.

A ranking model contributes here — as one input. It is not the decision-maker, and the project's own evaluation found that at the deployed operating point a simple operational rule outperformed it, which is why the model was demoted to a ranking signal inside a bounded queue rather than promoted into an authority.

### 5. Explanation and contributing evidence

Every raised case carries the recorded factors behind it, each traceable to a computed value, the reference it was compared against, the window it was measured over, and the admission rule that let it through. Where no evidence was recorded for a case, the interface says **"no contributing factors recorded"** rather than generating a plausible reason. Reviewers are allowed to know when they are being told nothing.

### 6. Review queue with a capacity budget

Cases are prioritised for a human look and filtered to what the signed-in reviewer is authorised to see — by role, by access purpose, and by unit scope. The queue is **budgeted**: items that would not realistically be read are suppressed, and the suppression is visible rather than hidden. Burden is treated as a design constraint, not an afterthought.

### 7. Human review

The reviewer acknowledges, reviews and decides, recording a **reason code** with the transition. Invalid state moves are refused rather than papered over, and a concurrent second reviewer cannot silently overwrite the first. The system recommends; a person owns the outcome.

### 8. Support and follow-up

Agreed support is recorded against the case, together with whether it is operationally schedulable now, schedulable with adjustment, or constrained by current duties. A follow-up can be scheduled with a date; due and overdue items return to the queue and stay there until a human closes them. Reopening a closed case is a supported transition, not an anomaly.

### 9. Feedback and auditable history

Each action appends to a **hash-chained** record — who acted, when, in which role, for what declared purpose. Editing a stored event breaks the chain, and the platform then reports itself unhealthy rather than continuing to serve as though nothing happened. The governance view shows the trail and its verification status to an audit-purpose account, with personnel identifiers stripped.

## Access model

Access is decided on the server, from the stored account, on every request, along three axes:

| Axis | Question it answers |
|---|---|
| **Role** | What kind of reviewer is this — welfare officer, command leadership, auditor, administrator? |
| **Access purpose** | Why are they asking — welfare support, aggregate operations, audit, infrastructure administration? |
| **Unit scope** | Which units' people may they see at all? |

A reviewer may *declare* a purpose; a declaration never *grants* reach. An account that does not hold the purpose the request needs is refused, and a person outside the account's unit scope is answered as "not found" — the same response as for someone who does not exist, so the interface cannot be used to map who serves where.

## Deliberate boundaries

The system does not diagnose medical or psychiatric conditions, does not make or recommend disciplinary or adverse personnel decisions, does not act autonomously, does not treat a missing voluntary self-report as evidence of distress, and does not present precomputed analysis as live inference. Those are construction rules, not disclaimers.

## What this means for a reviewer's day

They open a scoped queue of cases that have already survived a persistence policy and a capacity budget. Each one tells them what changed, against what baseline, over what window, and how current the underlying data is. They acknowledge, look at the person's pattern, decide whether support is warranted, record what they arranged, schedule the follow-up, and come back to it. Nothing about that sequence requires them to trust a number they cannot interrogate — and nothing about it can happen without leaving a record that can be checked afterwards.
