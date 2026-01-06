# Contributing to Open Rate Card

Thank you for your interest in contributing to the Open Rate Card specification! This is an open, community-driven project designed to serve the entire telecommunications industry.

---

## Table of Contents

1. [Code of Conduct](#code-of-conduct)
2. [How to Contribute](#how-to-contribute)
3. [Proposing New Fields](#proposing-new-fields)
4. [Proposing New Card Types](#proposing-new-card-types)
5. [Asking for Clarifications](#asking-for-clarifications)
6. [Reporting Bugs](#reporting-bugs)
7. [Contribution Guidelines](#contribution-guidelines)
8. [Review Process](#review-process)
9. [Backward Compatibility](#backward-compatibility)
10. [Acceptance Criteria](#acceptance-criteria)

---

## Code of Conduct

We are committed to providing a welcoming and inclusive environment for all contributors. Please:

- ✅ Be respectful and professional
- ✅ Welcome newcomers and help them get started
- ✅ Focus on constructive feedback
- ✅ Assume good intentions
- ❌ No harassment, discrimination, or hostile behavior

---

## How to Contribute

We use **GitHub Issues** for all proposals, discussions, and bug reports. All contributions go through community discussion before acceptance.

### Quick Links

- 🐛 [Report a Bug](https://github.com/connexcs/open-rate-card/issues/new?template=bug-report.md)
- 💡 [Propose New Field](https://github.com/connexcs/open-rate-card/issues/new?template=new-field-proposal.md)
- 📋 [Propose New Card Type](https://github.com/connexcs/open-rate-card/issues/new?template=new-card-type.md)
- ❓ [Request Clarification](https://github.com/connexcs/open-rate-card/issues/new?template=clarification.md)

---

## Proposing New Fields

### When to Propose a New Field

- You have a use case not covered by existing fields
- Industry standards or regulations require additional data
- Common patterns across multiple implementations need standardization
- You're adding custom fields internally and want to standardize them

### How to Propose

1. **Open a GitHub Issue** using the [New Field Proposal](https://github.com/connexcs/open-rate-card/issues/new?template=new-field-proposal.md) template
2. **Fill out all sections** of the template:
   - Field name
   - Parent section
   - Data type
   - Required/optional status
   - Description
   - Use case justification
   - Example JSON
   - Backward compatibility analysis

3. **Engage in discussion** - Respond to questions and feedback
4. **Wait for community input** - Minimum 14-day discussion period
5. **Maintainer review** - Final decision on acceptance

### Field Naming Standards

**Use snake_case:**
- ✅ `connection_fee`
- ✅ `max_call_duration`
- ❌ `connectionFee` (camelCase)
- ❌ `MaxCallDuration` (PascalCase)
- ❌ `connection-fee` (kebab-case)

**Be descriptive:**
- ✅ `minimum_call_duration`
- ❌ `min_dur` (abbreviations only if industry-standard)

**Use industry-standard terms:**
- ✅ `asr` (Answer Seizure Ratio - widely understood)
- ✅ `mcc` (Mobile Country Code - industry standard)
- ✅ `stir_shaken_attestation` (established standard)

### Data Type Selection

Follow these guidelines:

**Strings:**
- Text values, enums, identifiers
- Use ISO standards where applicable (country codes, currency codes)

**Numbers:**
- Financial values, rates, percentages
- Use `number` for decimals, `integer` for whole numbers

**Booleans:**
- True/false flags
- Example: `sms_enabled`, `fraud_detection_enabled`

**Arrays:**
- Multiple values of same type
- Example: `["sip", "h323"]`

**Objects:**
- Grouped related fields
- Example: `{ "street": "...", "city": "...", "country": "..." }`

**ISO Standards to Prefer:**
- **ISO 3166-1**: Country codes (`"US"`, `"GB"`)
- **ISO 4217**: Currency codes (`"USD"`, `"EUR"`)
- **ISO 8601**: Dates and times (`"2026-01-03T12:00:00Z"`)
- **E.164**: Phone numbers (`"+12125551234"`)

### Example Field Proposal

```markdown
## Field Name
`fraud_prevention_enabled`

## Parent Section
`cards.{card_name}`

## Data Type
`boolean`

## Required/Optional
Optional

## Description
Enables fraud prevention features for this card including velocity checks, 
spend limits, and suspicious pattern detection.

## Use Case
Many carriers need to indicate whether fraud prevention is active on a rate card.
This helps receiving systems understand what security measures are in place and
whether additional fraud checks are needed on their side.

## Example JSON
```json
{
  "cards": {
    "default": {
      "name": "Default Card",
      "type": "termination",
      "currency": "USD",
      "endpoint": "default",
      "fraud_prevention_enabled": true
    }
  }
}
```

## Backward Compatibility
This is a new optional field. Parsers that don't recognize it will safely ignore it.
No existing fields are modified or removed.
```

---

## Proposing New Card Types

### When to Propose a New Card Type

- Existing card types don't fit your service model
- You're offering a service that requires unique pricing structure
- Industry needs a standardized format for a new service type

### How to Propose

1. **Open a GitHub Issue** using the [New Card Type](https://github.com/connexcs/open-rate-card/issues/new?template=new-card-type.md) template
2. **Provide comprehensive details:**
   - Type name and identifier
   - Direction (inbound/outbound/bidirectional)
   - Traffic type (voice/sms/video/data)
   - Unique fields required
   - Rate structure format
   - Complete example card
   - Use cases

3. **Include real-world examples** showing why existing card types are insufficient
4. **Participate in discussion** - Be prepared to iterate on the design
5. **Wait for community consensus** - Larger changes require broader agreement

### Example Card Type Proposal

```markdown
## Type Name
**Video Conferencing** (`"video_conferencing"`)

## Direction
`"bidirectional"`

## Traffic Type
`"video"`

## Use Case
Cloud video conferencing services need to exchange rate cards for per-participant,
per-minute pricing with different tiers based on video quality (SD/HD/4K) and 
features (recording, transcription, etc.).

Existing card types don't support:
- Per-participant pricing
- Quality tier pricing
- Feature-based pricing

## Unique Fields
- `video_quality`: Array of quality tiers with rates
- `features_pricing`: Object with feature costs
- `participant_tiers`: Array of participant count tiers

## Complete Example
{complete JSON example}

## Migration Path
Existing `termination` cards can't represent this pricing model adequately.
New type is needed rather than extending existing types.
```

---

## Asking for Clarifications

### When to Ask

- Specification wording is ambiguous
- Field behavior is unclear
- Validation rules seem contradictory
- Examples don't match documentation

### How to Ask

1. **Open a GitHub Issue** using the [Clarification Request](https://github.com/connexcs/open-rate-card/issues/new?template=clarification.md) template
2. **Quote the specific text** that's unclear
3. **Explain what you're trying to achieve**
4. **Describe multiple interpretations** if applicable
5. **Suggest clarifying language** if you have ideas

Clarifications that improve documentation can be merged quickly (PATCH version).

---

## Reporting Bugs

### What Qualifies as a Bug

- JSON Schema validation errors on valid documents
- Schema allows invalid documents
- Documentation contradicts schema
- Examples don't validate
- Field descriptions are incorrect

### How to Report

1. **Open a GitHub Issue** using the [Bug Report](https://github.com/connexcs/open-rate-card/issues/new?template=bug-report.md) template
2. **Provide:**
   - Exact JSON that fails/passes incorrectly
   - Schema version being used
   - Validator being used
   - Expected vs actual behavior
   - Steps to reproduce

3. **Include validation output** if available

---

## Contribution Guidelines

### Documentation Requirements

All proposals must include:

1. ✅ **Clear description** of what's being added/changed
2. ✅ **Business justification** - Why is this needed?
3. ✅ **Use case examples** - Who will use this and how?
4. ✅ **JSON example** - Show it in context
5. ✅ **Backward compatibility analysis** - Impact on existing implementations
6. ✅ **Schema changes** - What validation rules are needed?

### Writing Style

- Use clear, concise language
- Define technical terms or link to [GLOSSARY.md](GLOSSARY.md)
- Provide examples for complex concepts
- Consider international audience (avoid regional colloquialisms)

### Testing

Before proposing changes:

1. ✅ Validate your examples against the schema
2. ✅ Test with multiple JSON validators
3. ✅ Ensure backward compatibility
4. ✅ Check for naming conflicts with existing fields

---

## Review Process

### Timeline

1. **Submission** - Issue opened with template
2. **Community Discussion** - Minimum 14 days for feedback
3. **Refinement** - Proposer addresses feedback and questions
4. **Maintainer Review** - Core team evaluates proposal
5. **Decision** - Accepted, rejected, or needs more work
6. **Implementation** - If accepted, added to next release
7. **Documentation** - Specification and examples updated

### Discussion Period

- **Minimum 14 days** for new fields
- **Minimum 30 days** for new card types or breaking changes
- Extensions possible if discussion is active

### Maintainer Review Criteria

Maintainers evaluate:

- ✅ **Need** - Does this solve a real problem?
- ✅ **Scope** - Is it appropriate for this specification?
- ✅ **Compatibility** - Does it maintain backward compatibility?
- ✅ **Quality** - Is it well-designed and documented?
- ✅ **Precedent** - Does it follow existing patterns?
- ✅ **Adoption** - Will the community use it?

### Acceptance Outcomes

**Accepted** ✅
- Change will be included in next appropriate release
- Documentation will be updated
- Schema will be modified
- Announced in changelog

**Rejected** ❌
- Clear explanation of why
- Alternative approaches suggested if applicable
- Can be reconsidered if circumstances change

**Needs Work** ⚠️
- Specific feedback on what needs improvement
- Proposer can revise and resubmit

---

## Backward Compatibility

### Critical Requirement

**All contributions must maintain backward compatibility within the same MAJOR version.**

See [VERSIONING.md](VERSIONING.md) for detailed compatibility rules.

### Compatibility Checklist

When proposing changes, confirm:

- ✅ No existing fields removed
- ✅ No existing fields renamed
- ✅ No field type changes
- ✅ No required fields added (only optional)
- ✅ No enum values removed
- ✅ Validation not made stricter
- ✅ Existing documents remain valid

### Why This Matters

Telecommunications companies deploy software that operates for years. Breaking changes would:

- ❌ Require coordinated upgrades across industries
- ❌ Break integration between systems
- ❌ Create operational disruptions
- ❌ Reduce trust in the specification

### Using Additional Properties

The specification uses `"additionalProperties": true` throughout. This means:

**You can use custom fields immediately** without waiting for formal adoption:

```json
{
  "schema_version": "1.0.0",
  "name": "My Rate Card",
  "cards": {
    "default": {
      "name": "Default",
      "type": "termination",
      "currency": "USD",
      "endpoint": "default",
      "my_custom_internal_field": "some_value"
    }
  }
}
```

This document is **valid** even though `my_custom_internal_field` isn't in the specification.

**When to formalize:**
- Multiple organizations need the same field
- Field represents industry standard
- You want guaranteed long-term stability
- You want field included in validation

---

## Acceptance Criteria

### For New Fields

Must meet ALL criteria:

1. ✅ **Widespread applicability** - Useful to multiple organizations, not just one
2. ✅ **No existing alternative** - Can't be represented with current fields
3. ✅ **Clear definition** - Unambiguous meaning and validation rules
4. ✅ **Proper data type** - Uses appropriate type and format
5. ✅ **Follows naming conventions** - snake_case, descriptive
6. ✅ **Backward compatible** - Doesn't break existing implementations
7. ✅ **Well documented** - Clear description and examples

### For New Card Types

Must meet ALL criteria:

1. ✅ **Distinct use case** - Can't be handled by existing card types
2. ✅ **Industry demand** - Multiple organizations need it
3. ✅ **Complete specification** - All fields defined
4. ✅ **Example rate cards** - Real-world examples provided
5. ✅ **Validation rules** - Schema constraints defined
6. ✅ **Backward compatible** - Doesn't affect existing types
7. ✅ **Documentation** - Usage guide written

---

## Release Cycle

### Version Schedule

- **PATCH (x.x.Z)**: As needed (documentation, bug fixes)
- **MINOR (x.Y.0)**: Quarterly (new optional fields, features)
- **MAJOR (X.0.0)**: Annually or as needed (breaking changes)

### Release Process

1. **Feature freeze** - 2 weeks before release
2. **Testing period** - Validate examples, test implementations
3. **Documentation review** - Ensure all changes documented
4. **Release** - Tagged version, changelog published
5. **Announcement** - Community notification

---

## Communication Channels

- **GitHub Issues** - Proposals, bugs, clarifications
- **GitHub Discussions** - General questions, ideas, feedback
- **Pull Requests** - Documentation fixes, example updates

---

## Getting Help

**Not sure where to start?**

- 📖 Read [SPECIFICATION.md](SPECIFICATION.md) to understand current fields
- 📋 Check [CARD-TYPES.md](CARD-TYPES.md) for card type patterns
- 📚 Review [existing issues](https://github.com/connexcs/open-rate-card/issues) for similar proposals
- 💬 Start a [GitHub Discussion](https://github.com/connexcs/open-rate-card/discussions) to brainstorm ideas

**Questions about the process?**

Open a [Clarification Request](https://github.com/connexcs/open-rate-card/issues/new?template=clarification.md) issue.

---

## Recognition

Contributors who have proposals accepted will be:

- Listed in release notes
- Credited in changelog
- Recognized in project documentation

---

## License

By contributing to this project, you agree that your contributions will be licensed under the same [MIT License](LICENSE) as the project.

---

## Thank You!

Your contributions help make telecommunications rate card exchange more efficient for everyone. We appreciate your time and expertise! 🎉

---

**Last Updated**: January 3, 2026  
**License**: MIT
