# SQL/CL Test Scaffold Excerpt

## Artifact 1: Environment Setup CL

```cl
PGM
  CRTLIB LIB(ATLASTEST) TEXT('Atlas M4 sample test library')
ENDPGM
```

## Artifact 3: Test Data Setup SQL

```sql
INSERT INTO ATLASTEST/CUSTMAST (CUSTNO, CUSTNAM, CUSTSTS)
VALUES (90001, 'SAMPLE INACTIVE CUSTOMER', 'I');

INSERT INTO ATLASTEST/CUSTMAST (CUSTNO, CUSTNAM, CUSTSTS)
VALUES (90002, 'SAMPLE ACTIVE CUSTOMER', 'A');
```

## Artifact 4: Execution CL

```cl
PGM
  CALL PGM(ATLASTEST/CUSTMNT) PARM('90001')
  CALL PGM(ATLASTEST/CUSTMNT) PARM('90002')
ENDPGM
```

## Artifact 5: Verification SQL

```sql
SELECT
  CASE
    WHEN CUSTSTS = 'I' THEN 'PASS'
    ELSE 'FAIL'
  END AS UT_01_STATUS
FROM ATLASTEST/CUSTMAST
WHERE CUSTNO = 90001;
```

## Artifact 6: Cleanup SQL

```sql
DELETE FROM ATLASTEST/CUSTMAST
WHERE CUSTNO IN (90001, 90002, 90003);
```

## Safety Notes

- `ATLASTEST` is a fictional sample library.
- Do not run sample scripts against production libraries.
- Real scaffolds should be reviewed and adapted to the target IBM i test environment.
