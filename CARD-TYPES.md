# Card Types Reference

This document provides detailed information about the three primary card types supported in the Open Rate Card specification:

1. [Termination Cards](#termination-cards) - Outbound voice traffic
2. [Origination Cards](#origination-cards) - Inbound DID/DDI services  
3. [Messaging Cards](#messaging-cards) - SMS/MMS services

---

## Card Type Overview

Each card in the `cards` section must specify its `type`, which determines the applicable fields and rate structure. All card types share common required fields:

**Required fields (all card types):**
- `name` - Card name
- `type` - Card type identifier
- `currency` - ISO 4217 currency code
- `endpoint` - Reference to endpoint name

---

## Termination Cards

**Type:** `"termination"`  
**Direction:** `"outbound"`  
**Traffic Type:** `"voice"`  
**Use Case:** Wholesale voice termination, routing outbound calls to destinations

### Complete Field Structure

```json
{
  "cards": {
    "default": {
      "name": "Default Rate Card",
      "description": "Standard termination rates",
      "type": "termination",
      "direction": "outbound",
      "traffic_type": "voice",
      "service_type": "wholesale",
      "currency": "USD",
      "endpoint": "default",
      "tech_prefix": "555",
      "fields": [...],
      "rate": {...},
      "charge": {...},
      "rates": [...],
      "criteria": [...],
      "performance": {...},
      "codecs": [...],
      "number_format": {...}
    }
  }
}
```

### Fields Array

The `fields` array defines the columns in your `rates` array. Common fields include:

```json
{
  "fields": [
    { "name": "prefix" },
    { "name": "name" },
    { "name": "rate" },
    { "name": "connection_fee" },
    { "name": "initial_interval" },
    { "name": "billing_interval" },
    { "name": "effective_date" },
    { "name": "country_code" },
    { "name": "region" },
    { "name": "destination_type" },
    { "name": "mcc" },
    { "name": "mnc" },
    { "name": "carrier" }
  ]
}
```

**Important:** The order and count of fields must match the columns in your `rates` array.

### Rate Configuration

```json
{
  "rate": {
    "precision": 4,
    "rounding": "up",
    "default_pulse": 60,
    "default_initial": 60,
    "connection": 0
  }
}
```

- **precision**: Decimal places for rate values (typically 4-6)
- **rounding**: `"up"`, `"down"`, `"nearest"`, `"half_up"`, `"half_down"`
- **default_pulse**: Billing increment in seconds (e.g., 60 = bill per minute)
- **default_initial**: Minimum call duration charged in seconds
- **connection**: Default connection/setup fee

### Charge Configuration

```json
{
  "charge": {
    "precision": 4,
    "rounding": "up"
  }
}
```

Controls final charge calculations after applying rates.

### Rates Array

The `rates` array is a 2D array where each inner array represents one rate entry:

```json
{
  "rates": [
    ["441", "United Kingdom Landline", 0.012, 0, 60, 60, "2024-06-01", "44", "Europe", "landline", null, null, null],
    ["442", "United Kingdom Mobile", 0.025, 0, 60, 60, "2024-06-01", "44", "Europe", "mobile", "234", "10", "O2"],
    ["447", "United Kingdom Mobile", 0.028, 0, 60, 60, "2024-06-01", "44", "Europe", "mobile", "234", "15", "Vodafone"],
    ["1212", "USA New York", 0.008, 0.01, 6, 6, "2024-06-01", "1", "North America", "landline", null, null, null]
  ]
}
```

**Each row matches the `fields` array:**
- Column 0: prefix (`"441"`)
- Column 1: name (`"United Kingdom Landline"`)
- Column 2: rate (`0.012`)
- Column 3: connection_fee (`0`)
- Column 4: initial_interval (`60`)
- Column 5: billing_interval (`60`)
- Column 6: effective_date (`"2024-06-01"`)
- Column 7: country_code (`"44"`)
- Column 8: region (`"Europe"`)
- Column 9: destination_type (`"landline"`)
- Column 10: mcc (`null`)
- Column 11: mnc (`null`)
- Column 12: carrier (`null`)

### Quality Criteria

```json
{
  "criteria": [
    { "acd": 60 },
    { "asr": 90 },
    { "pre_assert_id": "required" },
    { "stir_shaken_attestation": "A" },
    { "cli_required": true }
  ]
}
```

- **acd**: Average Call Duration in seconds (quality indicator)
- **asr**: Answer Seizure Ratio percentage (target: >50%)
- **pre_assert_id**: P-Asserted-Identity requirement (`"required"`, `"optional"`, `"none"`)
- **stir_shaken_attestation**: Required attestation level (`"A"`, `"B"`, `"C"`)
- **cli_required**: Whether CLI must be present

### Performance SLA

```json
{
  "performance": {
    "ner": 100,
    "uptime": 99.9,
    "cps": 10,
    "channels": 100,
    "concurrent_calls": 100,
    "pdd": 3,
    "latency": 150,
    "jitter": 30,
    "packet_loss": 1,
    "mos_min": 3.5,
    "sla_response_time_minutes": 240
  }
}
```

- **ner**: Network Effectiveness Ratio percentage
- **uptime**: Service availability percentage
- **cps**: Calls Per Second capacity
- **channels**: Total available channels
- **concurrent_calls**: Maximum simultaneous calls
- **pdd**: Post Dial Delay in seconds (target: <3s)
- **latency**: Network latency in milliseconds
- **jitter**: Jitter in milliseconds (target: <30ms)
- **packet_loss**: Acceptable packet loss percentage (target: <1%)
- **mos_min**: Minimum Mean Opinion Score (1.0-5.0, target: >4.0)
- **sla_response_time_minutes**: Support response time

### Codecs

Codecs can be specified as simple strings or detailed objects:

```json
{
  "codecs": [
    "PCMU",
    "PCMA",
    "G729",
    "G722",
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

**Common codecs:**
- **PCMU/PCMA (G.711)**: High quality, 64 kbps, no licensing
- **G729**: Compressed, 8 kbps, requires license
- **G722**: Wideband, 64 kbps
- **OPUS**: Adaptive bitrate, modern codec
- **iLBC**: Low bitrate, good for packet loss
- **AMR/AMR-WB**: Mobile codecs

### Number Format

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

Specifies accepted number formats for source (CLI) and destination numbers.

### Complete Minimal Example

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
      "host": "sip.provider.com",
      "port": 5060
    }
  },
  "cards": {
    "default": {
      "name": "Termination Rates",
      "type": "termination",
      "currency": "USD",
      "endpoint": "default",
      "fields": [
        { "name": "prefix" },
        { "name": "destination" },
        { "name": "rate" }
      ],
      "rates": [
        ["1", "USA", 0.005],
        ["44", "UK", 0.010],
        ["49", "Germany", 0.012]
      ]
    }
  }
}
```

---

## Origination Cards

**Type:** `"origination"`  
**Direction:** `"inbound"`  
**Traffic Type:** `"voice"`  
**Use Case:** DID/DDI numbers, toll-free numbers, inbound voice services

### Origination-Specific Fields

```json
{
  "cards": {
    "origination": {
      "name": "DID Services",
      "type": "origination",
      "direction": "inbound",
      "traffic_type": "voice",
      "service_type": "did",
      "currency": "USD",
      "endpoint": "default",
      "did_pricing": {
        "setup_fee": 5.00,
        "monthly_fee": 2.50,
        "per_minute_rate": 0.015,
        "channels_included": 2,
        "additional_channel_fee": 1.00
      },
      "number_types": ["local", "national", "toll_free", "mobile"],
      "porting": {
        "supported": true,
        "fee": 10.00,
        "lead_time_days": 7
      },
      "sms_enabled": true,
      "fax_enabled": true,
      "e911_supported": true,
      "cnam_lookup": true
    }
  }
}
```

### DID Pricing Structure

```json
{
  "did_pricing": {
    "setup_fee": 5.00,
    "monthly_fee": 2.50,
    "per_minute_rate": 0.015,
    "channels_included": 2,
    "additional_channel_fee": 1.00
  }
}
```

- **setup_fee**: One-time activation fee
- **monthly_fee**: Recurring monthly charge per DID
- **per_minute_rate**: Inbound call rate per minute
- **channels_included**: Channels included in monthly fee
- **additional_channel_fee**: Cost per additional channel

### Number Types

```json
{
  "number_types": ["local", "national", "toll_free", "mobile"]
}
```

Types of numbers available:
- **local**: Local geographic numbers
- **national**: National numbers
- **toll_free**: 800/888 numbers
- **mobile**: Mobile/wireless numbers

### Porting Configuration

```json
{
  "porting": {
    "supported": true,
    "fee": 10.00,
    "lead_time_days": 7
  }
}
```

- **supported**: Whether number porting is available
- **fee**: Cost to port an existing number
- **lead_time_days**: Days required to complete port

### Additional Services

- **sms_enabled**: SMS capability on DIDs
- **fax_enabled**: Fax/T.38 support
- **e911_supported**: Emergency services (US)
- **cnam_lookup**: Caller name lookup

### Complete Origination Example

```json
{
  "name": "DID Provider Rate Card",
  "schema_version": "1.0.0",
  "version": "1.0",
  "date": "2026-01-03",
  "endpoints": {
    "default": {
      "protocol": "sip",
      "transport": "udp",
      "host": "sip.didprovider.com",
      "port": 5060
    }
  },
  "cards": {
    "origination": {
      "name": "US DID Services",
      "type": "origination",
      "currency": "USD",
      "endpoint": "default",
      "did_pricing": {
        "setup_fee": 0,
        "monthly_fee": 1.50,
        "per_minute_rate": 0.01,
        "channels_included": 1,
        "additional_channel_fee": 0.50
      },
      "number_types": ["local", "toll_free"],
      "sms_enabled": true,
      "e911_supported": true
    }
  }
}
```

---

## Messaging Cards

**Type:** `"messaging"`  
**Direction:** `"outbound"` or `"inbound"`  
**Traffic Type:** `"sms"` or `"mms"`  
**Use Case:** SMS and MMS message termination

### Messaging-Specific Fields

```json
{
  "cards": {
    "messaging": {
      "name": "SMS Termination",
      "type": "messaging",
      "direction": "outbound",
      "traffic_type": "sms",
      "currency": "USD",
      "endpoint": "default",
      "message_types": ["sms", "mms"],
      "max_segments": 10,
      "encoding": "gsm7",
      "dlr_enabled": true,
      "fields": [
        { "name": "country_code" },
        { "name": "destination" },
        { "name": "rate" }
      ],
      "rates": [
        ["1", "United States", 0.0050],
        ["44", "United Kingdom", 0.0075],
        ["49", "Germany", 0.0080]
      ]
    }
  }
}
```

### Message Types

```json
{
  "message_types": ["sms", "mms"]
}
```

- **sms**: Standard SMS text messages
- **mms**: Multimedia messages with images/video

### Message Configuration

```json
{
  "max_segments": 10,
  "encoding": "gsm7",
  "dlr_enabled": true
}
```

- **max_segments**: Maximum message segments per SMS (160 chars per segment in GSM7)
- **encoding**: Character encoding
  - `"gsm7"`: Standard GSM 7-bit (160 chars)
  - `"ucs2"`: Unicode (70 chars)
  - `"utf8"`: UTF-8 encoding
- **dlr_enabled**: Delivery receipt support

### Simplified Rate Structure

Messaging cards typically use a simplified 3-column rate structure:

```json
{
  "fields": [
    { "name": "country_code" },
    { "name": "destination" },
    { "name": "rate" }
  ],
  "rates": [
    ["1", "United States", 0.0050],
    ["44", "United Kingdom", 0.0075],
    ["49", "Germany", 0.0080],
    ["86", "China", 0.0120]
  ]
}
```

Each rate applies per message segment.

### Complete Messaging Example

```json
{
  "name": "SMS Provider Rate Card",
  "schema_version": "1.0.0",
  "version": "1.0",
  "date": "2026-01-03",
  "endpoints": {
    "default": {
      "protocol": "sip",
      "host": "sms.provider.com",
      "port": 5060
    }
  },
  "cards": {
    "messaging": {
      "name": "Global SMS",
      "type": "messaging",
      "currency": "USD",
      "endpoint": "default",
      "message_types": ["sms"],
      "max_segments": 5,
      "encoding": "gsm7",
      "dlr_enabled": true,
      "fields": [
        { "name": "country_code" },
        { "name": "destination" },
        { "name": "rate" }
      ],
      "rates": [
        ["1", "USA/Canada", 0.0045],
        ["44", "United Kingdom", 0.0070],
        ["91", "India", 0.0060]
      ]
    }
  }
}
```

---

## Multi-Card Documents

A single rate card document can contain multiple cards of different types:

```json
{
  "name": "Complete Service Offering",
  "schema_version": "1.0.0",
  "version": "1.0",
  "date": "2026-01-03",
  "endpoints": {
    "voice": {
      "protocol": "sip",
      "host": "voice.provider.com",
      "port": 5060
    },
    "sms": {
      "protocol": "sip",
      "host": "sms.provider.com",
      "port": 5060
    }
  },
  "cards": {
    "termination": {
      "name": "Voice Termination",
      "type": "termination",
      "currency": "USD",
      "endpoint": "voice",
      "fields": [
        { "name": "prefix" },
        { "name": "rate" }
      ],
      "rates": [
        ["1", 0.005],
        ["44", 0.010]
      ]
    },
    "origination": {
      "name": "DID Services",
      "type": "origination",
      "currency": "USD",
      "endpoint": "voice",
      "did_pricing": {
        "monthly_fee": 2.00,
        "per_minute_rate": 0.01
      }
    },
    "sms": {
      "name": "SMS Termination",
      "type": "messaging",
      "currency": "USD",
      "endpoint": "sms",
      "fields": [
        { "name": "country_code" },
        { "name": "rate" }
      ],
      "rates": [
        ["1", 0.0050],
        ["44", 0.0075]
      ]
    }
  }
}
```

---

## Field-to-Rates Alignment Validation

**Critical Rule:** The number of columns in each `rates` array entry **must match** the number of items in the `fields` array.

### ✅ Valid Example

```json
{
  "fields": [
    { "name": "prefix" },
    { "name": "name" },
    { "name": "rate" }
  ],
  "rates": [
    ["1", "USA", 0.005],
    ["44", "UK", 0.010]
  ]
}
```
3 fields, 3 columns per rate ✅

### ❌ Invalid Example

```json
{
  "fields": [
    { "name": "prefix" },
    { "name": "name" },
    { "name": "rate" }
  ],
  "rates": [
    ["1", "USA"],        // Only 2 columns - INVALID
    ["44", "UK", 0.010]  // 3 columns - Valid
  ]
}
```

This will fail validation because the first rate entry has only 2 columns but `fields` defines 3.

---

## Best Practices

### 1. Field Naming
Use descriptive field names that clearly indicate the data type:
- ✅ `prefix`, `destination`, `rate`, `country_code`
- ❌ `col1`, `data`, `value`

### 2. Rate Precision
Match precision to your billing requirements:
- **Wholesale**: 4-6 decimal places
- **Retail**: 2-4 decimal places

### 3. Null Values
Use `null` for optional columns when data is not available:
```json
["441", "UK", 0.012, null, null, null]
```

### 4. Date Formats
Use ISO 8601 for date fields:
- Date only: `"2026-01-03"`
- DateTime: `"2026-01-03T00:00:00Z"`

### 5. Consistent Endpoints
Ensure endpoint references in cards match endpoint names defined in the `endpoints` section.

---

## See Also

- [SPECIFICATION.md](SPECIFICATION.md) - Complete field reference
- [GLOSSARY.md](GLOSSARY.md) - Telecommunications terminology
- [CONTRIBUTING.md](CONTRIBUTING.md) - Propose new card types

---

**Document Version**: 1.0.0  
**Last Updated**: January 3, 2026  
**License**: MIT
