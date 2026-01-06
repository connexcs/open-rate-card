---
name: New Field Proposal
about: Propose a new field to be added to the specification
title: '[FIELD] '
labels: ['enhancement', 'new-field']
assignees: ''
---

## Field Name

**Proposed field name:** `your_field_name_here`

## Parent Section

Where should this field be added?

- [ ] Root level (document metadata)
- [ ] `legal` section
- [ ] `endpoints` section
- [ ] `endpoints.{endpoint_name}` (within an endpoint)
- [ ] `cards` section
- [ ] `cards.{card_name}` (within a card)
- [ ] `customer` section
- [ ] `metadata` section
- [ ] Other (specify): _______________

**Full path:** (e.g., `cards.default.fraud_prevention`)

## Data Type

What type of data does this field contain?

- [ ] `string` (text)
- [ ] `number` (decimal)
- [ ] `integer` (whole number)
- [ ] `boolean` (true/false)
- [ ] `array` (list of values)
- [ ] `object` (nested structure)
- [ ] `enum` (specific allowed values)

**If enum, list allowed values:**
- 
- 
- 

**Additional type details:** (format, pattern, min/max, etc.)

## Required or Optional

- [ ] **Required** - Must be present in all documents
- [ ] **Optional** - Can be omitted

**If required, explain why it must be mandatory:**

## Description

Provide a clear, concise description of what this field represents:



## Business Justification

**Why is this field needed?**

Explain the business use case:



**Who will use this field?**

- [ ] VoIP carriers
- [ ] Wholesale providers
- [ ] System integrators
- [ ] Billing platforms
- [ ] Routing systems
- [ ] Other: _______________

## Use Case Example

Describe a specific scenario where this field is essential:



## Example JSON

Provide a complete example showing the field in context:

```json
{
  "schema_version": "1.0.0",
  "name": "Example Rate Card",
  "version": "1.0",
  "date": "2026-01-03",
  "cards": {
    "default": {
      "name": "Default Card",
      "type": "termination",
      "currency": "USD",
      "endpoint": "default",
      "your_field_name_here": "example_value"
    }
  }
}
```

## Validation Rules

What validation rules should apply?

**Pattern/Format:**

**Min/Max values:**

**Default value:**

**Dependencies:** (requires other fields to be present?)

## Backward Compatibility

**Impact on existing implementations:**

- [ ] ✅ No impact - This is a new optional field
- [ ] ⚠️ Minor impact - Explain: _______________
- [ ] ❌ Breaking change - Explain why it's necessary: _______________

**Will existing documents remain valid?**

- [ ] Yes, all existing documents remain valid
- [ ] No, existing documents need updates - Explain: _______________

## Alternative Approaches

Have you considered alternatives?

- **Can existing fields be used instead?** If not, why?
  
- **Could this be represented differently?**
  

## Related Standards

Does this field align with any industry standards?

- [ ] ISO standard: _______________
- [ ] ITU-T recommendation: _______________
- [ ] RFC: _______________
- [ ] Industry standard: _______________
- [ ] No related standards

## Implementation

**Are you currently using this field?**

- [ ] Yes, we're using it as a custom field
- [ ] No, but we plan to use it
- [ ] Multiple organizations need this

**If yes, how many documents currently use it?**

## Additional Context

Any other information that would help evaluate this proposal:



---

## Checklist

Before submitting, ensure:

- [ ] Field name follows `snake_case` convention
- [ ] Field name is descriptive (no abbreviations unless industry-standard)
- [ ] Use case clearly explained
- [ ] Example JSON is valid
- [ ] Backward compatibility addressed
- [ ] Related to [CONTRIBUTING.md](../CONTRIBUTING.md) guidelines
