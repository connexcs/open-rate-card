# Interconnect Made Easy Specification

Version: 1.0.0  
Last Updated: January 3, 2026

This document provides a complete field-by-field reference for the Interconnect Made Easy specification. All fields are **optional** unless explicitly marked as **required**.

## Table of Contents

1. [Document Structure](#document-structure)
2. [Root-Level Metadata](#root-level-metadata)
3. [Legal Section](#legal-section)
4. [Endpoints Section](#endpoints-section)
5. [Cards Section](#cards-section)
6. [Customer Section](#customer-section)
7. [Metadata Section](#metadata-section)
8. [Data Types](#data-types)
9. [Glossary](#glossary)

---

## Document Structure

The Interconnect Made Easy is a JSON document with the following top-level structure:

```json
{
  "name": "...",
  "schema_version": "...",
  "version": "...",
  "date": "...",
  "cards": {...},
  // ... additional sections
}
```

## Root-Level Metadata

Core information identifying the rate card and its provider.

### Required Fields

| Field | Type | Description | Example | Validation |
|-------|------|-------------|---------|------------|
| `name` | string | The name of the rate card or provider. | `"ABC Telecom Rate Card"` | - |
| `schema_version` | string | The version of the Interconnect Made Easy specification being used. Must follow semantic versioning (MAJOR.MINOR.PATCH). | `"1.0.0"` | Pattern `^[0-9]+\.[0-9]+\.[0-9]+$` |
| `version` | string | The version of this specific rate card document (not the schema). | `"1.0"`, `"2.1.3"` | Pattern `^[0-9]+\.[0-9]+(\.[0-9]+)?$` |
| `date` | string | Date this rate card was created or published. Format: `YYYY-MM-DD` (ISO 8601 date). | `"2026-01-03"` | - |
| `cards` | object | Object containing one or more rate card definitions. See [Cards Section](#cards-section). | See example below | - |

**`cards` Example:**
```json
{
  "cards": {
    "default": {...},
    "premium": {...}
  }
}
```

### Optional Fields

| Field | Type | Description | Example | Validation |
|-------|------|-------------|---------|------------|
| `description` | string | A brief description of the rate card or its purpose. | `"ABC Telecom provides competitive international calling rates."` | - |
| `author` | string | The author or organization that created this rate card. | `"ABC Telecom"` | - |
| `website` | string (URI) | Website URL of the provider. | `"https://www.abctelecom.com"` | - |
| `email` | string (email) | General contact email address. | `"rates@abctelecom.com"` | Valid email format |
| `technical` | string (email) | Technical support contact email. | `"support@abctelecom.com"` | - |
| `noc` | string (email) | Network Operations Center (NOC) contact email. | `"noc@abctelecom.com"` | - |
| `billing` | string (email) | Billing department contact email. | `"billing@abctelecom.com"` | - |
| `phone` | string (E.164) | Contact phone number in E.164 format. | `"+1-555-123-4567"` | Pattern `^\+?[1-9]\d{1,14}$` |
| `effective_date` | string (datetime) | Date and time when this rate card becomes effective. Format: `YYYY-MM-DDTHH:mm:ss(.sss)Z` | `"2024-06-01T00:00:00Z"` | - |
| `expiry_date` | string (datetime) | Date and time when this rate card expires. Format: `YYYY-MM-DDTHH:mm:ss(.sss)Z` | `"2024-12-31T23:59:59Z"` | - |
| `timezone` | string | IANA timezone identifier for interpreting dates/times. | `"UTC"`, `"America/New_York"` | - |

---

## Legal Section

Legal terms and conditions associated with the rate card.

#### `legal` (object)

All fields within the legal section are optional.

| Field | Type | Description | Example | Validation |
|-------|------|-------------|---------|------------|
| `terms_url` | string (URI) | URL to the full terms and conditions document. | `"https://www.abctelecom.com/terms"` | - |
| `privacy_url` | string (URI) | URL to the privacy policy. | `"https://www.abctelecom.com/privacy"` | - |
| `jurisdiction` | string (ISO 3166-1 alpha-2) | Legal jurisdiction using two-letter country code. | `"US"`, `"GB"`, `"DE"` | Pattern `^[A-Z]{2}$` |
| `dispute_resolution` | string (enum) | Method for resolving disputes. Allowed values: `"arbitration"`, `"litigation"`, `"mediation"` | `"arbitration"` | - |
| `liability_cap` | number | Maximum liability amount in USD. | `10000` | Minimum `0` |
| `indemnification` | boolean | Whether indemnification is required. | `true` | - |

**Example:**
```json
{
  "legal": {
    "terms_url": "https://www.abctelecom.com/terms",
    "privacy_url": "https://www.abctelecom.com/privacy",
    "jurisdiction": "US",
    "dispute_resolution": "arbitration",
    "liability_cap": 10000,
    "indemnification": true
  }
}
```

---

## Endpoints Section

Named SIP/VoIP endpoints for routing traffic. Each endpoint represents a connection point where calls can be sent or received.

#### `endpoints` (object)

An object where keys are endpoint names (e.g., `"default"`, `"backup"`) and values are endpoint configuration objects.

**Example:**
```json
{
  "endpoints": {
    "default": {
      "protocol": "sip",
      "transport": "udp",
      "host": "sip.abctelecom.com",
      "port": 5060,
      "username": "user",
      "password": "pass"
    },
    "backup": {
      "protocol": "sip",
      "transport": "tcp",
      "host": "sip-backup.abctelecom.com",
      "port": 5060,
      "priority": 10
    }
  }
}
```

### Endpoint Object Fields

| Field | Type | Description | Example | Validation |
|-------|------|-------------|---------|------------|
| `protocol` | string (enum) | Signaling protocol. Allowed values: `"sip"`, `"h323"`, `"iax2"`, `"webrtc"` | `"sip"` | - |
| `transport` | string (enum) | Transport protocol. Allowed values: `"udp"`, `"tcp"`, `"tls"`, `"ws"`, `"wss"` | `"udp"` | - |
| `host` | string | Hostname or IP address. | `"sip.abctelecom.com"` | - |
| `port` | integer | Port number. | `5060` | Range `1-65535` |
| `username` | string | Authentication username. | `"user123"` | - |
| `password` | string | Authentication password. Note: Consider security implications of storing passwords in plain text. | `"secretpass"` | - |
| `priority` | integer | Endpoint priority for failover routing. Lower numbers indicate higher priority. | `0` (highest), `10` (lower) | Minimum `0` |

---

## Cards Section

The core of the rate card specification. Contains pricing, routing, and quality information.

#### `cards` (object, **required**)

An object where keys are card names (e.g., `"default"`, `"premium"`, `"messaging"`) and values are card configuration objects.

Each card type may have different fields based on its `type`. See [CARD-TYPES.md](CARD-TYPES.md) for detailed documentation of termination, origination, and messaging card types.

### Common Card Fields

These fields are common across all card types.

| Field | Type | Required | Description | Example | Validation |
|-------|------|----------|-------------|---------|------------|
| `name` | string | **Yes** | Name of this rate card. | `"Default Rate Card"` | - |
| `type` | string (enum) | **Yes** | Type of rate card. Allowed values: `"termination"`, `"origination"`, `"messaging"`, `"wholesale"`, `"retail"`, `"did"`, `"toll_free"` | `"termination"` | - |
| `currency` | string (ISO 4217) | **Yes** | Currency code for all rates in this card. | `"USD"`, `"EUR"`, `"GBP"` | Pattern `^[A-Z]{3}$` |
| `endpoint` | string | **Yes** | Reference to an endpoint name defined in the `endpoints` section. | `"default"` | - |
| `description` | string | No | Description of this rate card. | `"This is the default rate card for standard destinations."` | - |
| `direction` | string (enum) | No | Traffic direction. Allowed values: `"outbound"`, `"inbound"`, `"bidirectional"` | `"outbound"` | - |
| `traffic_type` | string (enum) | No | Type of traffic this card handles. Allowed values: `"voice"`, `"sms"`, `"mms"`, `"fax"`, `"data"`, `"video"` | `"voice"` | - |
| `service_type` | string (enum) | No | Service classification. Allowed values: `"wholesale"`, `"retail"`, `"did"`, `"toll_free"`, `"premium"` | `"wholesale"` | - |
| `tech_prefix` | string | No | Technical prefix to be prepended for routing. | `"555"` | - |

### Rate Configuration

#### `fields` (array of objects)

Defines the columns in the `rates` array. Each object must have a `name` property.

```json
{
  "fields": [
    { "name": "prefix" },
    { "name": "name" },
    { "name": "rate" },
    { "name": "connection_fee" }
  ]
}
```

#### `rate` (object)

Default rate calculation configuration.

| Field | Type | Description | Example | Validation |
|-------|------|-------------|---------|------------|
| `precision` | integer | Number of decimal places for rates. | `4` | Range `0-10` |
| `rounding` | string (enum) | Rounding method for rate calculations. Allowed values: `"up"`, `"down"`, `"nearest"`, `"half_up"`, `"half_down"` | `"up"` | - |
| `default_pulse` | integer | Default billing pulse in seconds. | `60` | Minimum `1` |
| `default_initial` | integer | Default initial interval in seconds. | `60` | Minimum `1` |
| `connection` | number | Default connection fee. | `0`, `0.05` | Minimum `0` |

#### `charge` (object)

Charge calculation settings.

| Field | Type | Description | Example | Validation |
|-------|------|-------------|---------|------------|
| `precision` | integer | Number of decimal places for final charges. | `4` | Range `0-10` |
| `rounding` | string (enum) | Rounding method for charge calculations. Allowed values: `"up"`, `"down"`, `"nearest"`, `"half_up"`, `"half_down"` | `"up"` | - |

#### `rates` (array of arrays)

2D array containing rate data. Each inner array represents one rate entry and must match the columns defined in `fields`.

```json
{
  "fields": [
    { "name": "prefix" },
    { "name": "name" },
    { "name": "rate" }
  ],
  "rates": [
    ["441", "UK Landline", 0.012],
    ["442", "UK Mobile", 0.025]
  ]
}
```

### Quality Criteria

#### `criteria` (array of objects)

Array of quality requirements and criteria.

| Field | Type | Description | Example | Validation |
|-------|------|-------------|---------|------------|
| `acd` | integer | Average Call Duration requirement in seconds. | `60` | Minimum `0` |
| `asr` | integer | Answer Seizure Ratio requirement as percentage. | `90` | Range `0-100` |
| `pre_assert_id` | string (enum) | P-Asserted-Identity header requirement. Allowed values: `"required"`, `"optional"`, `"none"` | `"required"` | - |
| `stir_shaken_attestation` | string (enum) | Required STIR/SHAKEN attestation level. Allowed values: `"A"`, `"B"`, `"C"` | `"A"` | - |
| `cli_required` | boolean | Whether Calling Line Identification is required. | `true` | - |

**Example:**
```json
{
  "criteria": [
    { "acd": 60 },
    { "asr": 90 },
    { "stir_shaken_attestation": "A" },
    { "cli_required": true }
  ]
}
```

### Performance SLA

#### `performance` (object)

Service Level Agreement performance metrics.

| Field | Type | Description | Example | Validation |
|-------|------|-------------|---------|------------|
| `ner` | number | Network Effectiveness Ratio as percentage. | `100` | Range `0-100` |
| `uptime` | number | Service uptime percentage. | `99.9` | Range `0-100` |
| `cps` | integer | Calls Per Second capacity. | `10` | Minimum `0` |
| `channels` | integer | Number of available channels. | `100` | Minimum `0` |
| `concurrent_calls` | integer | Maximum concurrent calls. | `100` | Minimum `0` |
| `pdd` | integer | Post Dial Delay in seconds. | `3` | Minimum `0` |
| `latency` | integer | Network latency in milliseconds. | `150` | Minimum `0` |
| `jitter` | integer | Jitter in milliseconds. | `30` | Minimum `0` |
| `packet_loss` | number | Acceptable packet loss percentage. | `1` | Range `0-100` |
| `mos_min` | number | Minimum Mean Opinion Score. | `3.5` | Range `1.0-5.0` |
| `sla_response_time_minutes` | integer | SLA response time in minutes for support issues. | `240` (4 hours) | Minimum `0` |

### Codecs

##### `codecs` (array)
**Description:** Supported audio codecs. Can be simple strings or detailed objects.

**Simple format:**
```json
{
  "codecs": ["PCMU", "PCMA", "G729"]
}
```

**Complex format:**
```json
{
  "codecs": [
    "PCMU",
    {
      "name": "OPUS",
      "min_bitrate": 16000,
      "max_bitrate": 64000,
      "channels": 2,
      "ptime": 20
    }
  ]
}
```

#### Codec Object Fields

| Field | Type | Required | Description | Example |
|-------|------|----------|-------------|--------|
| `name` | string | **Yes** | Codec name. | `"OPUS"` |
| `min_bitrate` | integer | No | Minimum bitrate in bps. | `16000` |
| `max_bitrate` | integer | No | Maximum bitrate in bps. | `64000` |
| `channels` | integer | No | Number of audio channels. | `1` (mono), `2` (stereo) |
| `ptime` | integer | No | Packet time in milliseconds. | `20` |

### Number Format

#### `number_format` (object)

Number format requirements for source and destination.

##### `number_format.src` / `number_format.dst`

Source number (CLI/ANI) and destination number format requirements share the same structure:

| Field | Type | Description |
|-------|------|-------------|
| `e164` | boolean | Accept E.164 format. |
| `national` | boolean | Accept national format. |
| `international` | boolean | Accept international format. |

**Example:**
```json
{
  "number_format": {
    "src": {
      "e164": true,
      "national": false,
      "international": true
    },
    "dst": {
      "e164": true,
      "national": false,
      "international": true
    }
  }
}
```

---

## Customer Section

Information about the customer or account associated with this rate card.

#### `customer` (object)

| Field | Type | Description | Example |
|-------|------|-------------|--------|
| `name` | string | Customer name or company name. | `"XYZ Corp"` |
| `account_id` | string | Unique account identifier. | `"CUST-12345"` |
| `contact_name` | string | Primary contact person name. | `"John Doe"` |
| `contact_email` | string (email) | Primary contact email. | `"john.doe@xyzcorp.com"` |
| `contact_phone` | string (E.164) | Primary contact phone number. | `"+1-555-987-6543"` |
| `billing_email` | string (email) | Billing contact email. | `"billing@xyzcorp.com"` |
| `technical_email` | string (email) | Technical contact email. | `"tech@xyzcorp.com"` |
| `address` | object | Physical address. See address fields below. | - |
| `tax_id` | string | Tax identification number. | `"12-3456789"` |
| `customer_type` | string (enum) | Type of customer. Allowed values: `"carrier"`, `"enterprise"`, `"reseller"`, `"retail"`, `"other"` | `"carrier"` |

##### `customer.address` fields

| Field | Type | Description |
|-------|------|-------------|
| `street` | string | Street address. |
| `city` | string | City. |
| `state` | string | State or province. |
| `postal_code` | string | Postal/ZIP code. |
| `country` | string (ISO 3166-1 alpha-2) | Country code. |

**Example:**
```json
{
  "customer": {
    "name": "XYZ Corp",
    "account_id": "CUST-12345",
    "contact_name": "John Doe",
    "contact_email": "john.doe@xyzcorp.com",
    "address": {
      "street": "123 Main Street",
      "city": "New York",
      "state": "NY",
      "postal_code": "10001",
      "country": "US"
    },
    "customer_type": "carrier"
  }
}
```

---

## Metadata Section

File metadata and tracking information.

#### `metadata` (object)

| Field | Type | Description | Example |
|-------|------|-------------|--------|
| `created_at` | string (datetime) | Timestamp when this document was created. | `"2024-06-01T00:00:00Z"` |
| `updated_at` | string (datetime) | Timestamp when this document was last updated. | `"2024-06-01T00:00:00Z"` |
| `created_by` | string (email) | Email of the person who created this document. | `"admin@abctelecom.com"` |
| `checksum` | string or null | Checksum for document integrity verification (e.g., SHA-256 hash). | `null` or `"a1b2c3d4e5f6..."` |
| `signature` | string or null | Cryptographic signature for document authenticity. | `null` or `"base64-encoded-signature"` |
| `notes` | string | Additional notes or comments about this rate card. | `"Initial rate card release"` |

**Example:**
```json
{
  "metadata": {
    "created_at": "2024-06-01T00:00:00Z",
    "updated_at": "2024-06-01T00:00:00Z",
    "created_by": "admin@abctelecom.com",
    "checksum": null,
    "signature": null,
    "notes": "Initial rate card release"
  }
}
```

---

## Data Types

### String Formats

- **email**: Valid email address (pattern: `^[^\s@]+@[^\s@]+\.[^\s@]+$`)
- **uri**: Valid URI/URL format
- **date**: ISO 8601 date format (`YYYY-MM-DD`)
- **datetime**: ISO 8601 datetime format (`YYYY-MM-DDTHH:mm:ss(.sss)Z`)
- **E.164**: International phone number format (pattern: `^\+?[1-9]\d{1,14}$`)
- **ISO 3166-1 alpha-2**: Two-letter country code (pattern: `^[A-Z]{2}$`)
- **ISO 4217**: Three-letter currency code (pattern: `^[A-Z]{3}$`)

### Enum Values

Enumerated values are case-sensitive and must match exactly as specified.

---

## Glossary

Brief definitions of key telecommunications terms. For comprehensive definitions, see [GLOSSARY.md](GLOSSARY.md).

- **ACD**: Average Call Duration - typical length of calls in seconds
- **ASR**: Answer Seizure Ratio - percentage of calls that are successfully connected
- **NER**: Network Effectiveness Ratio - measure of routing success rate
- **PDD**: Post Dial Delay - time from last digit dialed until ring tone
- **MOS**: Mean Opinion Score - voice quality rating from 1.0 to 5.0
- **CPS**: Calls Per Second - capacity measure for simultaneous call setup
- **STIR/SHAKEN**: Standards for caller authentication (RFC 8224/8225)
- **LNP**: Local Number Portability - ability to keep phone numbers when switching carriers
- **CNAM**: Caller Name - database lookup for caller identification
- **E.164**: ITU-T international public telecommunication numbering plan
- **CLI/ANI**: Calling Line Identification / Automatic Number Identification
- **DID/DDI**: Direct Inward Dialing - phone numbers for direct calling

---

## Required vs Optional Fields

| Field Name       | Required / Optional |
| ---------------- | ------------------- |
| `name`           | Required            |
| `schema_version` | Required            |
| `version`        | Required            |
| `date`           | Required            |
| `cards`          | Required            |
| `description`    | Optional            |
| `author`         | Optional            |
| `website`        | Optional            |
| `email`          | Optional            |
| `technical`      | Optional            |
| `noc`            | Optional            |
| `billing`        | Optional            |
| `phone`          | Optional            |
| `effective_date` | Optional            |
| `expiry_date`    | Optional            |
| `timezone`       | Optional            |
| `legal`          | Optional            |
| `endpoints`      | Optional            |
| `customer`       | Optional            |
| `metadata`       | Optional            |

---

### Cards Object (per card)

| Field Name      | Required / Optional |
| --------------- | ------------------- |
| `name`          | Required            |
| `type`          | Required            |
| `currency`      | Required            |
| `endpoint`      | Required            |
| `description`   | Optional            |
| `direction`     | Optional            |
| `traffic_type`  | Optional            |
| `service_type`  | Optional            |
| `tech_prefix`   | Optional            |
| `fields`        | Optional            |
| `rates`         | Optional            |
| `rate_card`     | Optional            |
| `rate`          | Optional            |
| `charge`        | Optional            |
| `criteria`      | Optional            |
| `performance`   | Optional            |
| `codecs`        | Optional            |
| `number_format` | Optional            |

---

### Endpoint Object

| Field Name  | Required / Optional |
| ----------- | ------------------- |
| `protocol`  | Required            |
| `transport` | Required            |
| `host`      | Required            |
| `port`      | Required            |
| `username`  | Optional            |
| `password`  | Optional            |
| `priority`  | Optional            |

---

### Metadata Object

| Field Name   | Required / Optional |
| ------------ | ------------------- |
| `created_at` | Optional            |
| `updated_at` | Optional            |
| `created_by` | Optional            |
| `checksum`   | Optional            |
| `signature`  | Optional            |
| `notes`      | Optional            |

---

## Additional Properties

The Interconnect Made Easy specification uses `"additionalProperties": true` throughout. This means:

✅ **Parsers must ignore unknown fields** they don't recognize  
✅ **Custom fields can be added** at any level without breaking validation  
✅ **Extensions are encouraged** to meet specific industry needs  
⚠️ **Field names should avoid conflicts** with future specification additions

See [VERSIONING.md](VERSIONING.md) for guidelines on extending the specification while maintaining compatibility.

---

## See Also

- [CARD-TYPES.md](CARD-TYPES.md) - Detailed documentation of card types
- [GLOSSARY.md](GLOSSARY.md) - Complete telecommunications terminology reference
- [VERSIONING.md](VERSIONING.md) - Schema evolution and compatibility
- [CONTRIBUTING.md](CONTRIBUTING.md) - How to propose new fields

---

**Specification Version**: 1.0.0  
**Last Updated**: January 3, 2026  
**License**: MIT
