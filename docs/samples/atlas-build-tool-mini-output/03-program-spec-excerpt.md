# Program Spec Excerpt: CUSTMNT Status Validation

## Scope

Enhance existing RPGLE program `CUSTMNT` so inactive customers cannot be updated.

## Traceability

| Business Rule | Program Step | Implementation Note |
|---------------|--------------|---------------------|
| BR-01 | Step 3.2 | Check `CUSTSTS` before update logic. |

## Main Logic Excerpt

1. Read requested customer record from `CUSTMAST`.
2. If record is not found, return existing not-found handling.
3. Validate customer status.
   1. If `CUSTSTS = 'I'`, set message text to `Customer is inactive`.
   2. Set return indicator or response flag to reject update.
   3. Skip update to `CUSTMAST`.
4. For non-inactive customers, continue existing update flow.

## Data Contract Excerpt

| Field | Source | Usage |
|-------|--------|-------|
| `CUSTNO` | `CUSTMAST` | Key for customer lookup. |
| `CUSTSTS` | `CUSTMAST` | Status validation for BR-01. |

## Open Questions

| ID | Question | Build handling |
|----|----------|----------------|
| OQ-01 | Should status `S` also be rejected? | Non-blocking. Do not implement until confirmed. |
