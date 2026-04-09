## High-signal review patterns
If I only had limited time in a new codebase, these are the patterns I would prioritize first.

1. **Config mutation before finalize**  
   A privileged configuration change can alter the outcome of an already in-flight action before it reaches commit or execution.

2. **Staged handover + public acceptance**  
   A sensitive transition is staged by an authorized actor, but can later be finalized by an unexpected external caller.

3. **Stored evidence vs live config**  
   Validation evidence is collected under one set of assumptions, but later evaluated against a different live configuration.

4. **Permissionless commit with mutable privileged prerequisites**  
   A path appears permissionless, but its success still depends on mutable privileged state.

5. **Execution / verification mismatch**  
   A valid execution path succeeds, but later fails because proving or verification assumptions diverge.

6. **Valid action becomes uncompletable**  
   The system accepts the action into the pipeline, but protocol logic later prevents it from completing.
