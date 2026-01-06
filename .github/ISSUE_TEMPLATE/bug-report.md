---
name: Bug Report
about: Report a bug in the schema or documentation
title: '[BUG] '
labels: ['bug']
assignees: ''
---

## Bug Description

**Brief description of the bug:**



## Bug Type

- [ ] Schema validation error (valid document fails validation)
- [ ] Schema validation pass (invalid document passes validation)
- [ ] Documentation contradicts schema
- [ ] Example doesn't validate
- [ ] Field description is incorrect
- [ ] Broken link or reference
- [ ] Other: _______________

## Environment

**Schema version:** (e.g., 1.0.0)

**Validator used:**
- [ ] ajv-cli
- [ ] Online validator (specify): _______________
- [ ] Custom implementation
- [ ] Other: _______________

**Validator version:**

**Operating System:**

## Expected Behavior

**What should happen?**



## Actual Behavior

**What actually happens?**



## JSON Example

**Provide the JSON that demonstrates the bug:**

```json
{
  "name": "Example Rate Card",
  "schema_version": "1.0.0",
  "version": "1.0",
  "date": "2026-01-03",
  "cards": {
    "default": {
      "name": "Default",
      "type": "termination",
      "currency": "USD",
      "endpoint": "default"
    }
  }
}
```

## Validation Output

**Include the actual validation error or unexpected success message:**

```
[Paste validation output here]
```

## Steps to Reproduce

1. 
2. 
3. 

## Schema Section

**If schema bug, which section of the schema is affected?**

**File:** `schema/open-rate-card.schema.json`

**Section/Path:** (e.g., `properties.cards.additionalProperties.properties.rate`)

**Current schema snippet:**

```json
{
  "paste": "relevant schema section"
}
```

## Proposed Fix

**If you have a suggested fix:**



**Corrected schema snippet:**

```json
{
  "proposed": "fix here"
}
```

## Impact

**Severity:**

- [ ] Critical - Prevents valid documents from being used
- [ ] High - Common use case affected
- [ ] Medium - Edge case or workaround available
- [ ] Low - Minor issue or documentation only

**Who is affected?**

- [ ] All users
- [ ] Specific card type: _______________
- [ ] Specific use case: _______________

## Workaround

**Is there a workaround available?**

- [ ] Yes - Describe: _______________
- [ ] No

## Related Issues

**Are there related issues?**

- #issue_number

## Additional Context

**Any other information:**



**Screenshots:** (if applicable)

---

## Checklist

Before submitting:

- [ ] Verified this is a bug (not expected behavior)
- [ ] Checked against latest schema version
- [ ] Searched existing issues for duplicates
- [ ] Included complete JSON example
- [ ] Included validation output
- [ ] Specified environment details
- [ ] Described expected vs actual behavior
