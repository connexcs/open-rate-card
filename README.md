# Interconnect Made Easy Specification

[![Schema Validation](https://github.com/connexcs/interconnect-made-easy/workflows/Schema%20Validation/badge.svg)](https://github.com/connexcs/interconnect-made-easy/actions)
[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)

An open, standardized JSON specification for telecommunications interconnect information exchange, covering rate cards, routing constraints, service requirements, and technical interconnect metadata between VoIP carriers, wholesale providers, and system integrators.

## 🎯 Purpose

The Interconnect Made Easy specification defines a **standardised, machine-readable interface for exchanging telecom interconnect information**.

Today, interconnect data is exchanged inconsistently via:

- Emails with attached Excel / CSV files  
- Ad-hoc rate decks with undocumented assumptions  
- Separate and incomplete “interconnect guides”  
- Vendor- or platform-specific formats with no common structure  

There is **no standard way** to exchange:

- Who the provider is  
- What traffic is allowed  
- What technical endpoint should be used  
- What quality, compliance, or routing constraints apply  
- How rate information relates to the interconnect itself  

This specification addresses that gap by defining a **single, structured format** for interconnect information exchange.

### Specifically, it provides:

- **Standardization**: A consistent schema for interconnect and rate data  
- **Information completeness**: Rate, routing, quality, and endpoint context in one structure  
- **Automation**: Enable systems to ingest, validate, and process interconnect data programmatically  
- **Interoperability**: Remove vendor-specific formats and assumptions  
- **Version Control**: Explicit schema and document versioning  
- **Extensibility**: Forward-compatible design allowing additional fields without breaking parsers  

> **Note:** This specification focuses on *information exchange*, not on how individual platforms configure SIP trunks or switches internally.

## 🚀 Quick Start

### Minimal Valid Example

```json
{
  "name": "My Telecom Rate Card",
  "schema_version": "1.0.0",
  "version": "1.0",
  "date": "2026-01-03",
  "cards": {
    "default": {
      "name": "Basic Termination",
      "type": "termination",
      "currency": "USD",
      "endpoint": "default"
    }
  }
}
```
This represents the minimum viable interconnect definition:

+ Identifies the provider
+ Declares schema compatibility
+ Defines at least one interconnect card
+ References a technical endpoint 

### 🔗 Rate Card Location (Inline or External)

Rate information may be **embedded directly** in the document or **referenced externally**.

This enables:

+ Hosting large rate decks separately
+ Updating rates without changing interconnect metadata
+ API- or CDN-based rate distribution

**Example (conceptual):**

```json
"rate_card": {
  "uri": "https://rates.provider.example/termination/us.json",
  "format": "json",
  "checksum": "sha256:abc123..."
}
```

The interconnect document remains the **authoritative context**, while rate data may evolve independently.

### Validation

#### Using ajv-cli (Command Line)

```bash
# Install ajv-cli globally
npm install -g ajv-cli ajv-formats

# Validate your rate card
ajv validate -s schema/interconnect-made-easy.schema.json -d your-rate-card.json --strict=false
```

#### Using Online Validators

- [JSON Schema Validator](https://www.jsonschemavalidator.net/) - Paste the schema and your JSON
- [JSON Schema Lint](https://jsonschemalint.com/) - Alternative online validator

--strict=false is recommended during schema evolution. Production implementations may enable strict mode.

#### Validation Examples

**✅ Passing Validation:**
```bash
$ ajv validate -s schema/interconnect-made-easy.schema.json -d example.json
example.json valid
```

**❌ Missing Required Field:**
```json
{
  "name": "Test Card",
  "version": "1.0",
  "date": "2026-01-03"
  // Missing: schema_version and cards
}
```
```bash
Error: data must have required property 'schema_version'
Error: data must have required property 'cards'
```

**❌ Invalid Type:**
```json
{
  "name": "Test Card",
  "schema_version": "1.0.0",
  "version": 1.0,  // Should be string, not number
  "date": "2026-01-03",
  "cards": {}
}
```
```bash
Error: data/version must be string
```

**❌ Invalid Card (Missing Required Fields):**
```json
{
  "name": "Test Card",
  "schema_version": "1.0.0",
  "version": "1.0",
  "date": "2026-01-03",
  "cards": {
    "default": {
      "name": "My Card",
      "type": "termination"
      // Missing: currency and endpoint (required in cards)
    }
  }
}
```
```bash
Error: data/cards/default must have required property 'currency'
Error: data/cards/default must have required property 'endpoint'
```

## 📚 Documentation

### Core Documentation

- **[SPECIFICATION.md](SPECIFICATION.md)** - Normative schema and field definitions
- **[CARD-TYPES.md](CARD-TYPES.md)** - Termination, origination, messaging, and future card types
- **[GLOSSARY.md](GLOSSARY.md)** - Telecommunications terminology reference
- **[VERSIONING.md](VERSIONING.md)** - Schema evolution and compatibility rules

### Schema
- **schema/interconnect-made-easy.schema.json** – JSON Schema definition

### Contributing

- **[CONTRIBUTING.md](CONTRIBUTING.md)** - How to propose new fields, card types, or improvements via GitHub Issues

### Schema

- **[schema/interconnect-made-easy.schema.json](schema/interconnect-made-easy.schema.json)** - JSON Schema for validation

## 🏗️ Project Structure

```
interconnect-made-easy/
├── README.md                          # This file
├── SPECIFICATION.md                   # Complete specification
├── CARD-TYPES.md                      # Card type documentation
├── GLOSSARY.md                        # Terminology reference
├── VERSIONING.md                      # Versioning strategy
├── CONTRIBUTING.md                    # Contribution guidelines
├── LICENSE                            # MIT License
├── example.json                       # Comprehensive example
├── schema/
│   └── interconnect-made-easy.schema.json    # JSON Schema
└── .github/
    ├── workflows/
    │   └── validate-schema.yml       # CI/CD validation
    └── ISSUE_TEMPLATE/               # Issue templates
        ├── new-field-proposal.md
        ├── new-card-type.md
        ├── clarification.md
        ├── bug-report.md
        └── config.yml
```

## 🔧 Required Fields

### Document Level (5 required fields)

- `name` - Interconnect or provider identifier
- `schema_version` - Interconnect Made Easy specification version (semantic versioning)
- `version` - Interconnect document revision
- `date` - Publication or effective date
- `cards` - One or more interconnect cards

### Card Level (4 required fields per card)

- `name` - Card identifier
- `type` - Interconnect type (termination, origination, messaging, etc.)
- `currency` - ISO 4217 currency code (e.g., USD, EUR, GBP)
- `endpoint` - Reference to a technical endpoint

### 🔄 Forward Compatibility

+ Additional fields are permitted at all levels
+ Parsers must ignore unknown fields
+ Existing fields are never removed, only deprecated
+ Schema evolution is explicit and versioned

This ensures long-term interoperability across carriers and platforms.

## 💡 What This Specification Is (and Is Not)

**This is**:

+ A standard interface for exchanging interconnect information
+ A machine-readable replacement for ad-hoc interconnect documents
+ A neutral, vendor-agnostic schema

**This is not**:

+ A SIP configuration guide
+ A switch provisioning manual
+ A billing engine definition
+ A routing algorithm specification
+ This will not replace contracts

## 🌐 Target Audience

- **VoIP Carriers** - Wholesale voice providers
- **Telecommunications Companies** - Retail and enterprise service providers
- **System Integrators** - Building billing and routing platforms
- **Rate Deck Management Systems** - Automated rate comparison and ingestion
- **Network Operations** - Traffic routing and quality management

## 💡 Key Features

### Multi-Card Support
Define multiple rate cards in a single document (termination, origination, SMS, etc.)

### Comprehensive Endpoint Configuration
SIP/VoIP connection parameters with authentication, transport options, and failover

### Flexible Rate Structures
- Columnar rate data format (efficient storage)
- Self-describing fields array
- Support for prefixes, destinations, pricing tiers
- Connection fees and billing intervals

### Quality & Performance Metrics
- Quality criteria (ACD, ASR, CLI requirements)
- Performance SLAs (NER, uptime, CPS, latency, jitter, MOS)
- STIR/SHAKEN attestation levels

### Rich Metadata
- Legal terms and jurisdiction
- Customer information
- Payment and billing terms
- Compliance flags (KYC, GDPR, FCC, OFCOM)
- Support and monitoring configuration

## 🤝 Contributing

We welcome community contributions! This is an open specification designed to serve the entire telecommunications industry.

**How to contribute:**

1. **Propose new fields or card types** - Open a [GitHub Issue](https://github.com/connexcs/interconnect-made-easy/issues/new/choose) using our templates
2. **Discuss and refine** - Community discussion period (minimum 14 days)
3. **Review and approval** - Maintainers review for backward compatibility
4. **Documentation updates** - Accepted changes are added to the specification

See [CONTRIBUTING.md](CONTRIBUTING.md) for detailed guidelines.

## 📖 Example Use Cases

- **Automated Rate Updates** - Systems can automatically ingest and compare rate cards
- **Multi-Vendor Routing** - Normalize rates from different carriers into a common format
- **Billing System Integration** - Direct import into billing platforms
- **Rate Comparison Tools** - Build tools that compare rates across providers
- **Contract Digitization** - Convert traditional rate sheets into machine-readable format

## 📄 License

This specification is released under the [MIT License](LICENSE).

Copyright © 2026 Connex Carrier Services

## 🔗 Links

- **GitHub Repository**: [connexcs/interconnect-made-easy](https://github.com/connexcs/interconnect-made-easy)
- **Issue Tracker**: [Report issues or propose features](https://github.com/connexcs/interconnect-made-easy/issues)
- **Discussions**: [Join the conversation](https://github.com/connexcs/interconnect-made-easy/discussions)

## 📊 Version

- **Current Schema Version**: 1.0.0
- **Last Updated**: January 3, 2026

---

**Questions?** Open an issue or start a discussion on GitHub!
