# Configuration Race Patterns

These notes focus on bugs where valid system behavior depends on timing between staged state changes and later privileged or public actions.

## Core idea

A configuration race exists when a system allows a meaningful state transition to depend on:

- a staged intermediate state
- a later public or permissionless call
- and mutable privileged configuration in between

In that structure, the intended state transition may still be technically valid, but the timing can be abused to force an unexpected result.

## Common pattern

A flow often looks like this:

1. a legitimate actor stages a transition
2. the system enters a pending or in-flight state
3. another actor triggers a finalization step
4. live configuration is re-read during finalization
5. the result differs from what the original actor expected when staging the transition

This can happen in:

- staged receiver / owner handovers
- delayed commit / finalize flows
- config-dependent execution paths
- approval systems with mutable thresholds
- systems where default config is resolved at execution time instead of staged time

## Important distinction

Not every configuration race is automatically a high-severity exploit.

A good review must distinguish between:

### Expected privileged reconfiguration
A role changes future behavior in a way the protocol intentionally allows.

### Retroactive effect on already in-flight state
A role changes configuration in a way that affects operations already staged or already partially validated.

The second class is usually much more security-relevant.

## High-signal questions

### Does the system re-read live config during commit/finalize?
If yes, ask whether this can change the outcome of already-staged operations.

### Is the pending state bound to a config snapshot?
If no, a later config mutation may retroactively alter success conditions.

### Can an untrusted caller finalize a staged transition?
If yes, the question becomes:
- are they only accelerating an already-authorized transition,
- or are they forcing the system into a state that the legitimate operator did not intend to reach yet?

### Does timing create a harmful intermediate state?
A timing issue becomes more serious if it can produce a broken or under-configured state such as:

- receiver changed before roles are ready
- config active before dependencies are initialized
- commit path blocked after evidence was already collected
- fees, claims, or rotations no longer operational

## Common impact types

### Premature control transition
A staged handover can be finalized earlier than intended.

### Message blocking
In-flight messages become uncommittable after config mutation.

### Operational dead zone
The system enters a temporarily unmanageable state because configuration changes outpace setup.

### Liveness loss
The system does not lose funds directly, but valid actions stop progressing.

## Good mitigation patterns

Robust systems usually:

- bind pending transitions to a config/version snapshot
- prevent public calls from finalizing sensitive staged transitions
- require explicit authorization for final acceptance steps
- separate initialization from privileged handover logic
- ensure config changes apply only to future operations, not already in-flight ones

## Review mindset

When reviewing a staged transition, always ask:

> What assumptions does the first step make about the second step?

Then ask:

> Can any public or privileged actor invalidate those assumptions before the final step happens?

That is where many strong availability, governance, and state-transition findings come from.
