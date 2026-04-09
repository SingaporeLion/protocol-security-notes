# Availability vs Security Notes

These notes focus on the difference between direct exploitability and meaningful protocol failure.

A system does not need to lose funds immediately to still have a serious security problem.

## Core principle

Security review should not focus only on theft, forgery, or direct balance loss.

A protocol can remain economically or operationally unsafe if valid actions, proofs, messages, or claims can no longer complete under intended conditions.

That usually falls into one of these categories:

- availability loss
- liveness failure
- completeness failure
- message delivery failure
- verification/execution mismatch
- censorship-resistance failure

## Useful distinction

### Classic security failure
Examples:
- unauthorized fund transfer
- signature bypass
- forged proof
- privilege escalation

### Availability / liveness failure
Examples:
- valid messages cannot commit
- valid proofs cannot verify
- already-approved actions stop progressing
- protocol roles can block user operations
- expected cross-component flow becomes unexecutable

The second category is often underrated, but in real systems it can still be serious.

## When availability issues matter a lot

Availability or liveness problems become especially important when they affect:

- cross-chain messages
- escrow / burn / lock flows
- bridge delivery paths
- sequenced state transitions
- proving systems
- liquidation / claim / settlement paths
- protocol invariants explicitly promising censorship resistance or completion guarantees

## Good severity questions

### Is the action valid?
If the underlying action is legitimate and still becomes impossible, the issue is stronger.

### Is the broken flow officially supported?
If the failing path is part of a documented, intended workflow, the issue is more meaningful.

### Can the failure be triggered or sustained by a privileged actor?
If yes, this may move from “operational inconvenience” toward “censorship / blocking risk”.

### Does the failure strand value or block downstream state?
If assets, claims, or protocol progress depend on the failed step, the issue is stronger.

### Is the problem recoverable?
A temporary delay is different from indefinite blocking or a system state that requires admin intervention.

## Common examples

### Message delivery failure
A message is valid and partially verified, but can no longer commit.

### Proof completeness failure
A valid execution can be proven but the resulting proof cannot be verified.

### Privileged blocking
No invalid state is forged, but a role can stop legitimate state transitions.

### Configuration-dependent liveness break
A valid action only becomes impossible because live config changed after the action had already entered the pipeline.

## How to write this class of finding well

A strong report should make these points explicit:

1. the action is valid
2. the system officially supports the action
3. the failing behavior is produced by protocol logic, not by misuse
4. the result is not just inconvenience, but broken progression of legitimate state
5. the impact is practical and reproducible

## Review mindset

When a bug does not steal funds, do not stop there.

Ask instead:

> Does this let the protocol reject, stall, or block a valid action that should succeed under the intended design?

If the answer is yes, the bug may still be meaningful — especially in systems that claim permissionlessness, completeness, or censorship resistance.
