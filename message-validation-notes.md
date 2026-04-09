# Message Validation Notes

## Common patterns
- Validation state should be bound to the configuration active at the time evidence is collected.
- Re-evaluating past attestations under a new live config can create retroactive invalidation paths.
- Permissionless finalize / commit steps should not become blockable through later privileged config changes.
