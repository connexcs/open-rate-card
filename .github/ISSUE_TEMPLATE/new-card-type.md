---
name: New Card Type
about: Propose a new card type for the specification
title: '[CARD TYPE] '
labels: ['enhancement', 'card-type']
assignees: ''
---

## Card Type Name

**Type identifier:** `"your_card_type_name"` (will be used in `"type"` field)

**Display name:** (e.g., "Video Conferencing Services")

## Card Classification

**Direction:**
- [ ] `"outbound"` - Outbound traffic
- [ ] `"inbound"` - Inbound traffic
- [ ] `"bidirectional"` - Both directions

**Traffic Type:**
- [ ] `"voice"` - Voice calls
- [ ] `"sms"` - SMS messages
- [ ] `"mms"` - MMS messages
- [ ] `"fax"` - Fax transmissions
- [ ] `"data"` - Data services
- [ ] `"video"` - Video calls/conferencing
- [ ] Other: _______________

**Service Type:**
- [ ] `"wholesale"` - Carrier-to-carrier
- [ ] `"retail"` - Direct to customers
- [ ] `"did"` - Direct Inward Dialing
- [ ] `"toll_free"` - Toll-free services
- [ ] `"premium"` - Premium rate services
- [ ] Other: _______________

## Business Need

**Why is this new card type needed?**

Explain the business use case:



**What service model does this represent?**



**How is this different from existing card types?**

- **Termination cards:** 
- **Origination cards:** 
- **Messaging cards:** 

## Industry Demand

**Who needs this card type?**

- [ ] Multiple carriers/providers (estimate: _____ organizations)
- [ ] Specific industry segment: _______________
- [ ] New service offering not covered by existing types
- [ ] Regulatory requirement

**Evidence of demand:**



## Unique Fields

List fields that are specific to this card type (not in common card fields):

### Field 1
- **Name:** `field_name`
- **Type:** 
- **Required/Optional:** 
- **Description:** 

### Field 2
- **Name:** `field_name`
- **Type:** 
- **Required/Optional:** 
- **Description:** 

*(Add more as needed)*

## Rate Structure

**How are rates structured for this card type?**

Describe the pricing model:



**Fields needed in `fields` array:**

```json
{
  "fields": [
    { "name": "field1" },
    { "name": "field2" },
    { "name": "field3" }
  ]
}
```

**Sample rates array:**

```json
{
  "rates": [
    ["value1", "value2", "value3"],
    ["value1", "value2", "value3"]
  ]
}
```

## Complete Example

Provide a complete, valid rate card document with this card type:

```json
{
  "name": "Provider Rate Card",
  "schema_version": "1.0.0",
  "version": "1.0",
  "date": "2026-01-03",
  "endpoints": {
    "default": {
      "protocol": "sip",
      "transport": "udp",
      "host": "example.com",
      "port": 5060
    }
  },
  "cards": {
    "your_card_type": {
      "name": "Your Card Name",
      "type": "your_card_type_name",
      "currency": "USD",
      "endpoint": "default",
      
      // Add all unique fields here
      
      "fields": [
        // Define fields
      ],
      "rates": [
        // Sample rates
      ]
    }
  }
}
```

## Use Cases

### Use Case 1

**Scenario:**

**How this card type solves it:**

### Use Case 2

**Scenario:**

**How this card type solves it:**

*(Add more use cases as needed)*

## Migration Path

**Can existing card types be migrated to this new type?**

- [ ] Yes - Describe migration: _______________
- [ ] No - This is entirely new
- [ ] Partial - Some fields overlap: _______________

**Would existing card types need deprecation?**

- [ ] No impact on existing types
- [ ] Replaces: _______________
- [ ] Complements existing types

## Backward Compatibility

**Impact on specification:**

- [ ] ✅ Fully compatible - Just adds new card type option
- [ ] ⚠️ Requires schema changes - Describe: _______________
- [ ] ❌ Breaking changes needed - Explain: _______________

**Will this affect parsers that don't recognize the new type?**

- [ ] No - They'll ignore cards of this type
- [ ] Yes - Explain impact: _______________

## Validation Schema

**Special validation rules for this card type:**



**Required fields beyond the standard 4 (name, type, currency, endpoint):**

1. 
2. 
3. 

**Constraints and dependencies:**



## Related Standards

Does this card type align with industry standards?

- [ ] Based on standard: _______________
- [ ] Follows practice from: _______________
- [ ] No related standards (new service model)

## Real-World Examples

**Are organizations currently representing this in custom formats?**

- [ ] Yes - Describe current approaches: _______________
- [ ] No - This is a new service type

**If yes, how many organizations?**

## Documentation Needs

What documentation should accompany this card type?

- [ ] Add section to [CARD-TYPES.md](../../CARD-TYPES.md)
- [ ] Update [SPECIFICATION.md](../../SPECIFICATION.md)
- [ ] Add examples to [example.json](../../example.json)
- [ ] Update [schema/open-rate-card.schema.json](../../schema/open-rate-card.schema.json)
- [ ] Add terminology to [GLOSSARY.md](../../GLOSSARY.md)

## Implementation

**Are you currently implementing this?**

- [ ] Yes, using custom format
- [ ] Planning to implement
- [ ] Awaiting standardization

**Timeline needs:**

## Additional Context

Any other information that would help evaluate this proposal:



---

## Checklist

Before submitting, ensure:

- [ ] Card type name is descriptive and follows conventions
- [ ] Complete example provided and validates
- [ ] Use cases clearly demonstrate need
- [ ] Unique fields documented
- [ ] Rate structure explained
- [ ] Backward compatibility addressed
- [ ] Multiple organizations would benefit
- [ ] Related to [CONTRIBUTING.md](../CONTRIBUTING.md) guidelines
