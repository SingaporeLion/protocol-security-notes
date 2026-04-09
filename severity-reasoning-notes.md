# Severity Reasoning Notes

These notes are meant for contest-style security research where correct severity selection matters almost as much as the finding itself.
A strong bug can still lose value if it is framed with the wrong severity.

## Core principle

Severity should be based on demonstrated impact, not on how interesting the bug feels.

Good severity reasoning usually answers three questions clearly:

1. What breaks?
2. Under what conditions?
3. Why does that impact level fit better than the adjacent levels?

## General mindset

### High
Use High only when impact is strong and clearly demonstrated.

Typical signs:
- direct asset theft
- forged invalid state with major consequence
- strong privilege takeover
- severe censorship/blocking with clearly major protocol consequence
- proof soundness break in proving systems

### Medium
Medium often fits bugs where the system still breaks in a meaningful way, but without direct catastrophic loss.

Typical signs:
- valid actions become blocked
- supported flows become unavailable
- protocol liveness breaks under realistic conditions
- already-in-flight operations become invalidated
- valid proofs or messages fail to progress
- privileged actors can interfere in ways that violate stated invariants

### Low / QA
Use Low or QA when:
- the issue is mostly informational
- the impact is weak or speculative
- strong assumptions are needed
- the effect is noisy but not practically harmful
- the finding is more about style, maintainability, or weak ergonomics than real protocol breakage

## What often causes downgrades

### Overclaiming
The report says “critical censorship” but only proves a narrow delay or recoverable inconvenience.

### Unclear preconditions
A judge may agree the bug exists, but disagree that it is broadly meaningful.

### Missing impact bridge
The root cause is correct, but the report never proves why the bug matters in practice.

### Governance vs exploit confusion
A finding may show that a privileged actor can do something undesirable, but the judge may view it as intended authority unless the report proves retroactive or unexpected harm.

## Strong severity framing patterns

### For Medium
A good Medium framing often looks like:

- the action is valid
- the system intends to support it
- protocol logic prevents it from completing
- this causes real availability, liveness, or delivery failure
- impact is meaningful even without direct theft

### For High
A good High framing usually needs:

- strong practical consequence
- limited speculation
- a direct bridge from bug to major damage
- and ideally a reproduction or very clear exploit path

## Good report hygiene

A strong report should separate:

### Confirmed behavior
What was actually reproduced or observed.

### Likely root cause
What the code suggests is causing the reproduced behavior.

### Impact
What the broken behavior means in practice.

### Severity reasoning
Why this should be Medium instead of Low, or High instead of Medium.

That separation makes reports easier to judge and harder to dismiss.

## Personal checklist before submitting

- Is the root cause concrete?
- Are the preconditions explicit?
- Is the impact real, not implied?
- Is the severity justified against the actual demonstrated behavior?
- Could a judge say this is only governance / ops / intended authority?
- Did I separate confirmed behavior from likely root cause?
- Is there a clean reproduction path?
- Am I arguing the strongest honest version of the bug, not the most dramatic one?

## Final rule

A slightly conservative but well-defended severity usually performs better than an aggressive severity with weak justification.

Judges forgive restraint more often than overclaiming.
