# Generated RPGLE Excerpt

The code below is a fictional excerpt showing the shape of a controlled change block. It is not a complete member.

```rpgle
      * ATLAS-M4 SAMPLE CHANGE BLOCK START
      * BR-01: Inactive customers must not be updated through CUSTMNT.
     C                   IF        CUSTSTS = 'I'
     C                   EVAL      MSGTXT = 'Customer is inactive'
     C                   EVAL      REJECTUPD = *ON
     C                   GOTO      SKIPUPD
     C                   ENDIF
      * ATLAS-M4 SAMPLE CHANGE BLOCK END

      * Existing update logic remains below this point.
     C                   IF        NOT REJECTUPD
     C                   UPDATE    CUSTREC
     C                   ENDIF

     C     SKIPUPD       TAG
```

## Review Notes

- The excerpt preserves fixed-format style because the sample represents an existing RPGLE program.
- The change is intentionally limited to BR-01.
- A real generated member should be run through compile precheck and code review before compile.
