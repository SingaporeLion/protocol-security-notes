# Message Validation Notes

These notes focus on message validation logic in systems where correctness depends on evidence collection, state transitions, and delayed finalization.

## Core principle

Validation evidence should remain bound to the exact assumptions under which it was collected.

If attestations, signatures, confirmations, or approvals are gathered under one configuration, and the system later re-evaluates them under a different live configuration, a retroactive invalidation path may appear.

That class of issue is especially important in:

- cross-chain messaging
- staged verification / commit flows
- attestation-based systems
- quorum-based verification paths
- delayed finalization pipelines

## Common high-signal bug pattern

A system stores evidence using only the message identity, but not the validation context.

Typical examples:

- keyed by `(message_id)` but not by active config version
- keyed by `(header_hash, payload_hash)` but not by quorum config
- keyed by signer identity but not by role snapshot
- keyed by approval count but not by required threshold version

This can create a state where:

1. a message becomes valid under the original rules
2. evidence is accumulated successfully
3. the rules change before finalization
4. the same message becomes unverifiable or blocked

## Questions worth asking during review

### What exactly is stored?
Is the system storing:

- the raw evidence only
- or also the configuration state under which the evidence was collected?

### What is evaluated live?
Does the finalize / commit / execute step:

- validate against a frozen snapshot
- or recompute the active rules at commit time?

### Can privileged roles mutate the validation conditions mid-flight?
If yes, ask whether this can affect already in-flight messages or already collected evidence.

### Is the final step permissionless?
If the final step is permissionless, but a privileged actor can mutate prerequisites before that step, then the system may not be truly censorship-resistant in practice.

## Risk patterns

### Retroactive invalidation
Previously valid evidence becomes insufficient because live config changed.

### Privileged message blocking
A role cannot forge messages, but can block already-progressing messages by changing requirements.

### Staged-finalization fragility
A multi-step flow looks permissionless at the end, but depends on mutable privileged state between steps.

### Liveness loss without theft
No direct asset theft occurs, but valid message delivery becomes impossible or indefinitely blocked.

## Good design properties

A robust design usually does one of the following:

- snapshots the effective config when evidence collection begins
- stores a config hash / version alongside the message evidence
- guarantees that later config changes do not affect already in-flight messages
- clearly separates future-message config changes from already-verified state

## Review mindset

When reviewing message-validation logic, do not ask only:

> Can an attacker forge a message?

Also ask:

> Can a privileged or semi-privileged actor make a legitimate message stop progressing after it has already entered the validation pipeline?

That second question often reveals stronger availability and censorship-resistance issues than obvious signature bypass bugs.
