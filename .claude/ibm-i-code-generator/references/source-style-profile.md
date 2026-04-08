# Maintained Source Style Profile

When generating fixed-format RPGLE for an existing codebase, the generated source should read
like a maintained member — not a technically correct draft that looks alien to the shop.

This reference defines what to extract from a reference source member and apply to generated code.

---

## What to Extract from Reference Source

When a reference source member is provided (e.g., `CUR41.rpgle`), extract these style elements:

### Header / Banner Style
- Program header comment block (box style, content pattern, line length)
- Modification history format (date, author, description layout)
- Copyright or shop-standard notice if present

### Structural Comments
- Section separator style (e.g., `C*===`, `C*---`, `C*****`)
- Subroutine header comment pattern (box vs inline, what fields are listed)
- File declaration comment pattern (purpose annotation style)
- Data structure purpose comments

### Naming Conventions
- Constant naming pattern (e.g., `C_MAXRSP`, `#MAXRSP`, `MAXRSP`)
- Work field naming pattern (e.g., `wkField`, `W_FIELD`, `WKFIELD`)
- Data structure member naming (e.g., `DS_CUST`, `CUSTDS`, `dsCust`)
- Key list naming (e.g., `KYORDR`, `KY_ORDR`, `KORDR`)
- Loop counter naming (e.g., `X`, `IDX`, `I`, `wkIdx`)
- Response-building field naming (e.g., `RSPTEXT`, `RSP_TXT`, `wkRsp`)

### Inline Descriptions
- Whether D-spec fields carry inline comments (column 40+ descriptions)
- Whether constants have purpose annotations
- Whether key fields have access-pattern annotations
- Standard description vocabulary (e.g., "Work field for...", "Loop counter", "Response buffer")

### Error Handling Idiom
- MONMSG scope pattern (global vs per-command)
- Error indicator usage pattern
- Return code variable naming
- Error subroutine naming (e.g., `SR_ERROR`, `*PSSR`, `SRPERR`)

---

## How to Apply in Generated Code

When generating fixed-format RPGLE with a reference source available:

1. **Match the header** — use the same box style, field layout, and modification history format
2. **Match section separators** — use the same comment line style between major code regions
3. **Match naming** — adopt the reference's naming conventions for new constants, work fields,
   data structures, key lists, and loop counters
4. **Match inline descriptions** — if the reference uses D-spec inline comments, add them to
   generated declarations in the same style
5. **Match error handling** — if the reference uses a specific MONMSG/error pattern, follow it

When generating without a reference source:
- Use clean, neutral fixed-format conventions
- Do not invent a shop-specific style
- Add a `TODO` comment suggesting the developer align the style with existing members

---

## What NOT to Extract

- Business logic (belongs in the Program Spec)
- File names or field names (belong in the spec's Data Contract / File Usage)
- Return codes or error categories (belong in the spec's Error Handling)
- Interface parameters (belong in the spec's Interface Contract)

Style is about **how the code looks**. Structure is about **what the code does**.
This profile covers style only.
