# Versioning and Compatibility

This document describes the versioning strategy, backward compatibility principles, and migration guidelines for the Open Rate Card specification.

---

## Table of Contents

1. [Versioning Strategy](#versioning-strategy)
2. [Semantic Versioning](#semantic-versioning)
3. [Backward Compatibility](#backward-compatibility)
4. [Schema Version vs. Document Version](#schema-version-vs-document-version)
5. [Deprecation Policy](#deprecation-policy)
6. [Handling Unknown Fields](#handling-unknown-fields)
7. [Version Negotiation](#version-negotiation)
8. [Migration Guides](#migration-guides)
9. [Breaking Changes](#breaking-changes)

---

## Versioning Strategy

The Open Rate Card specification uses **semantic versioning** to communicate the nature and impact of changes.

### Version Numbers

Version numbers follow the format: **MAJOR.MINOR.PATCH**

**Example:** `1.2.3`
- `1` = Major version
- `2` = Minor version  
- `3` = Patch version

---

## Semantic Versioning

### MAJOR Version (X.0.0)

**When to increment:** Breaking changes that require parser updates

**Breaking changes include:**
- Removing existing fields
- Changing field types (string → number)
- Changing enum values or removing allowed values
- Making optional fields required
- Changing required field validation (stricter patterns)
- Restructuring document hierarchy

**Example Changes:**
```
1.0.0 → 2.0.0
```

**Impact:** Systems using version 1.x must update their parsers to handle version 2.0.0

**Mitigation:** Provide migration guide, maintain support for previous MAJOR version for minimum 1 year

### MINOR Version (x.Y.0)

**When to increment:** New features that maintain backward compatibility

**Compatible changes include:**
- Adding new optional fields
- Adding new card types
- Adding new enum values to existing fields
- Adding new sections to the document
- Expanding validation (less strict patterns)

**Example Changes:**
```
1.0.0 → 1.1.0
1.1.0 → 1.2.0
```

**Impact:** Systems using version 1.0.0 can read version 1.1.0 documents (may ignore new fields)

**Guarantee:** All 1.x.x versions are backward compatible with 1.0.0 parsers

### PATCH Version (x.x.Z)

**When to increment:** Documentation updates, clarifications, bug fixes in schema that don't affect structure

**Non-functional changes include:**
- Documentation corrections
- Example updates
- Clarifying descriptions
- Fixing typos
- Schema formatting improvements
- Comment additions

**Example Changes:**
```
1.0.0 → 1.0.1
1.1.0 → 1.1.1
```

**Impact:** No parser changes required, purely informational

---

## Backward Compatibility

### Core Principles

1. **Parsers ignore unknown fields** - When encountering fields they don't recognize, parsers must skip them gracefully
2. **Additional properties allowed** - All objects use `"additionalProperties": true` in schema
3. **Optional fields remain optional** - Fields can't become required in MINOR versions
4. **No field removal** - Fields are never removed, only deprecated
5. **Enum expansion only** - New enum values can be added, existing ones never removed

### Compatibility Matrix

| Parser Version | Document Version | Compatible? | Notes |
|---------------|------------------|-------------|-------|
| 1.0.0 | 1.0.0 | ✅ Yes | Exact match |
| 1.0.0 | 1.1.0 | ✅ Yes | Parser ignores new 1.1.0 fields |
| 1.0.0 | 1.2.0 | ✅ Yes | Parser ignores new 1.2.0 fields |
| 1.0.0 | 2.0.0 | ❌ No | Major version incompatibility |
| 1.1.0 | 1.0.0 | ✅ Yes | New parser handles old document |
| 1.1.0 | 1.1.0 | ✅ Yes | Exact match |
| 1.1.0 | 2.0.0 | ❌ No | Major version incompatibility |
| 2.0.0 | 1.x.x | ⚠️ Maybe | Depends on parser implementation |

### Forward Compatibility

Documents should be **forward compatible** within the same MAJOR version:

```json
{
  "schema_version": "1.0.0",
  "name": "My Rate Card",
  "new_field_added_in_1_1": "value"
}
```

A parser built for 1.0.0 must:
- ✅ Successfully parse this document
- ✅ Ignore `new_field_added_in_1_1`
- ✅ Extract all 1.0.0 fields correctly
- ⚠️ Optionally log warning about unknown field

---

## Schema Version vs. Document Version

### `schema_version` (Required)

**Purpose:** Indicates which version of the Open Rate Card specification the document conforms to

**Format:** Semantic versioning (MAJOR.MINOR.PATCH)

**Example:** `"schema_version": "1.0.0"`

**Usage:** Parsers check this to determine compatibility and which validation rules to apply

**Requirement:** Must match a published specification version

### `version` (Required)

**Purpose:** Version of this specific rate card document (your internal versioning)

**Format:** Flexible (typically MAJOR.MINOR or MAJOR.MINOR.PATCH)

**Example:** `"version": "3.5"` or `"version": "2024.01"`

**Usage:** Track rate card updates, revisions, editions

**Requirement:** No strict format, but should be incrementing

### Example

```json
{
  "name": "ABC Telecom Rate Card",
  "schema_version": "1.0.0",
  "version": "2024.06",
  "date": "2024-06-01"
}
```

- Document uses specification 1.0.0
- This is the June 2024 edition of ABC's rate card
- ABC might publish `"version": "2024.07"` next month with updated rates (still using `"schema_version": "1.0.0"`)

---

## Deprecation Policy

### Deprecation Process

When a field needs to be phased out:

1. **Announce deprecation** in specification changelog
2. **Minimum 1 year notice period** before removal
3. **Mark field as deprecated** in documentation
4. **Provide migration path** to alternative field/structure
5. **Only remove in MAJOR version** increment

### Deprecated Field Documentation

```markdown
#### `legacy_field` (string, **DEPRECATED**)
**Deprecated in:** 1.5.0  
**Removal planned:** 2.0.0  
**Alternative:** Use `new_field` instead  
**Migration:** `new_field = legacy_field.toUpperCase()`
```

### Example Deprecation Timeline

- **2024-06-01 (v1.5.0)**: Field `old_codec_format` marked deprecated
- **2024-06-01 to 2025-06-01**: Support both `old_codec_format` and `codecs`
- **2025-06-01 (v2.0.0)**: Remove `old_codec_format`, only `codecs` supported

---

## Handling Unknown Fields

### Parser Requirements

Parsers **must implement** the following behavior:

#### 1. Ignore Unknown Fields

```python
# Good: Ignore unknown fields
def parse_rate_card(data):
    known_fields = ['name', 'version', 'schema_version', 'date', 'cards']
    parsed = {}
    for field in known_fields:
        if field in data:
            parsed[field] = data[field]
    # Unknown fields are ignored
    return parsed
```

#### 2. Optional Warnings

```python
# Better: Log warnings for unknown fields
def parse_rate_card(data):
    known_fields = ['name', 'version', 'schema_version', 'date', 'cards']
    parsed = {}
    for field, value in data.items():
        if field in known_fields:
            parsed[field] = value
        else:
            logger.warning(f"Unknown field '{field}' (might be from newer schema version)")
    return parsed
```

#### 3. Strict Mode (Optional)

For testing/validation purposes, parsers may offer a strict mode:

```python
# Optional: Strict mode for validation
def parse_rate_card(data, strict=False):
    known_fields = ['name', 'version', 'schema_version', 'date', 'cards']
    if strict:
        unknown = set(data.keys()) - set(known_fields)
        if unknown:
            raise ValueError(f"Unknown fields in strict mode: {unknown}")
    # ... rest of parsing
```

### Generator Requirements

Rate card generators **should:**

1. Include `schema_version` field indicating spec compliance
2. Only use fields documented in that schema version (or document extensions)
3. Follow validation rules for the target schema version

### Why This Matters

```json
{
  "schema_version": "1.0.0",
  "name": "Rate Card",
  "custom_internal_tracking_id": "ABC-123",
  "cards": {...}
}
```

A 1.0.0 parser encountering `custom_internal_tracking_id`:
- ✅ **Must not fail** validation
- ✅ **Must parse** successfully
- ⚠️ **May log** unknown field warning
- ✅ **Must extract** all 1.0.0 standard fields

This allows organizations to add custom fields for internal use without breaking interoperability.

---

## Version Negotiation

### Between Systems

When exchanging rate cards between systems:

1. **Sender includes** `schema_version` in document
2. **Receiver checks** if it supports that version
3. **If compatible**: Process document
4. **If incompatible**: Return error with supported versions

### Negotiation Example

```json
{
  "error": "Unsupported schema version",
  "received": "2.0.0",
  "supported": ["1.0.0", "1.1.0", "1.2.0"],
  "message": "Please provide rate card in version 1.x.x format"
}
```

### API Version Headers

APIs exchanging rate cards may use headers:

```http
POST /rate-cards HTTP/1.1
Content-Type: application/json
X-Rate-Card-Schema-Version: 1.0.0

{rate card document}
```

---

## Migration Guides

### Migrating from 1.0.0 to 1.1.0 (Example)

When new optional fields are added:

**Changes in 1.1.0:**
- Added `fraud_prevention` section to cards
- Added `restrictions` section to cards
- Added `cli_handling` section to cards

**Migration Steps:**

1. **No action required** - All 1.0.0 documents remain valid
2. **Optional**: Add new sections to leverage new features
3. **Parser update**: Update to 1.1.0 parser to access new fields

**Before (1.0.0):**
```json
{
  "schema_version": "1.0.0",
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

**After (1.1.0):**
```json
{
  "schema_version": "1.1.0",
  "cards": {
    "default": {
      "name": "Default",
      "type": "termination",
      "currency": "USD",
      "endpoint": "default",
      "fraud_prevention": {
        "enabled": true,
        "max_call_duration": 7200
      }
    }
  }
}
```

**Compatibility:** 1.0.0 parsers will successfully parse 1.1.0 documents (ignoring new fields)

---

## Breaking Changes

### What Constitutes a Breaking Change?

**Breaking (requires MAJOR version bump):**
- ❌ Removing fields
- ❌ Renaming fields
- ❌ Changing field types
- ❌ Making optional fields required
- ❌ Removing enum values
- ❌ Stricter validation patterns
- ❌ Restructuring document hierarchy

**Not Breaking (allowed in MINOR version):**
- ✅ Adding new optional fields
- ✅ Adding new enum values
- ✅ Adding new sections
- ✅ Relaxing validation (less strict)
- ✅ Adding new card types
- ✅ Deprecating fields (with notice)

### Example: Breaking Change

```json
// Version 1.0.0
{
  "rate": {
    "rounding": "up"  // String enum
  }
}

// Version 2.0.0 (BREAKING)
{
  "rate": {
    "rounding": 1  // Changed to integer enum (0=up, 1=down)
  }
}
```

This requires MAJOR version bump (1.0.0 → 2.0.0) because:
- Field type changed (string → integer)
- 1.0.0 parsers expecting string will fail

### Example: Compatible Change

```json
// Version 1.0.0
{
  "rate": {
    "rounding": "up"  // Enum: up, down, nearest
  }
}

// Version 1.1.0 (Compatible)
{
  "rate": {
    "rounding": "half_up"  // New enum value added
  }
}
```

This is MINOR version (1.0.0 → 1.1.0) because:
- New enum value added
- Existing values still valid
- 1.0.0 parsers might not recognize `"half_up"` but can still validate documents with `"up"`, `"down"`, or `"nearest"`

---

## Release Cycle

### Planned Release Schedule

- **PATCH versions**: As needed (documentation fixes)
- **MINOR versions**: Quarterly (new features, additions)
- **MAJOR versions**: Annually or as required (breaking changes)

### Version Support

- **Current MAJOR version**: Full support
- **Previous MAJOR version**: Security updates and critical fixes for 1 year
- **Older versions**: End of life, upgrade recommended

---

## Best Practices

### For Specification Maintainers

1. ✅ Follow semantic versioning strictly
2. ✅ Document all changes in CHANGELOG.md
3. ✅ Provide migration guides for MAJOR versions
4. ✅ Announce deprecations 1 year in advance
5. ✅ Maintain backward compatibility within MAJOR versions

### For Implementers

1. ✅ Always include `schema_version` in documents
2. ✅ Implement forward-compatible parsers (ignore unknown fields)
3. ✅ Check schema version before parsing
4. ✅ Log warnings for unknown fields (don't fail)
5. ✅ Test with documents from newer MINOR versions
6. ✅ Plan for MAJOR version migrations

### For Rate Card Publishers

1. ✅ Use latest MINOR version when possible
2. ✅ Include `schema_version` in all documents
3. ✅ Validate against schema before publishing
4. ✅ Document custom fields in comments or external documentation
5. ✅ Update when new features meet your needs

---

## Changelog Example

```markdown
# Changelog

## [1.1.0] - 2024-09-01
### Added
- New `fraud_prevention` section in cards
- New `restrictions` section in cards
- New `cli_handling` section in cards

### Changed
- Expanded `codecs` to support object format with bitrate

### Deprecated
- None

## [1.0.1] - 2024-07-15
### Fixed
- Corrected examples in documentation
- Fixed typos in field descriptions

## [1.0.0] - 2024-06-01
### Added
- Initial release
- Support for termination, origination, and messaging cards
- Complete field reference
- JSON Schema validation
```

---

## See Also

- [SPECIFICATION.md](SPECIFICATION.md) - Complete field reference
- [CONTRIBUTING.md](CONTRIBUTING.md) - How to propose changes
- [README.md](README.md) - Project overview

---

**Document Version**: 1.0.0  
**Last Updated**: January 3, 2026  
**License**: MIT
