# Unit Test Plan Excerpt

## Test Scope

Validate that `CUSTMNT` rejects updates for inactive customers while preserving existing update behavior for active customers.

## Test Cases

| Test ID | Rule | Setup | Action | Expected Result |
|---------|------|-------|--------|-----------------|
| UT-01 | BR-01 | `CUSTNO=90001`, `CUSTSTS='I'` | Attempt update through `CUSTMNT` | Update rejected, message `Customer is inactive`, record unchanged. |
| UT-02 | BR-01 | `CUSTNO=90002`, `CUSTSTS='A'` | Attempt update through `CUSTMNT` | Update follows existing success path. |
| UT-03 | OQ-01 | `CUSTNO=90003`, `CUSTSTS='S'` | Attempt update through `CUSTMNT` | Expected behavior TBD until business confirms suspended status rule. |

## Risks And Gaps

- Interactive display behavior may require manual verification if no automated screen driver is available.
- Status `S` remains non-blocking and should not be implemented as inactive without confirmation.
