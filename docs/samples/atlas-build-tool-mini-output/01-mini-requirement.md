# Mini Requirement: Customer Status Validation

## Change ID

`SAMPLE-M4-001`

## Business Goal

Prevent inactive customers from being updated through the customer maintenance program.

## Current Behavior

Program `CUSTMNT` updates records in physical file `CUSTMAST`. The input flow does not consistently reject records where customer status is inactive.

## Requested Future Behavior

When the selected customer has `CUSTSTS = 'I'`, `CUSTMNT` must reject the update, display message `Customer is inactive`, and leave the existing `CUSTMAST` record unchanged.

## Business Rules

| ID | Rule |
|----|------|
| BR-01 | Inactive customers must not be updated through `CUSTMNT`. |

## Known Objects

| Object | Type | Notes |
|--------|------|-------|
| `CUSTMNT` | RPGLE program | Existing interactive maintenance program. |
| `CUSTMAST` | Physical file | Contains customer master records. |

## Open Questions

| ID | Question | Blocking |
|----|----------|----------|
| OQ-01 | Confirm whether suspended status `S` should follow the same rule as inactive status `I`. | No |
