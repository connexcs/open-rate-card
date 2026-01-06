# Open Rate Card Specification

[![Schema Validation](https://github.com/connexcs/open-rate-card/workflows/Schema%20Validation/badge.svg)](https://github.com/connexcs/open-rate-card/actions)
[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)

An open, standardized JSON format for telecommunications rate cards, enabling automated exchange of pricing, routing, and service information between VoIP carriers, wholesale providers, and system integrators.

## 🎯 Purpose

The Open Rate Card specification eliminates the inefficiencies of PDF and Excel-based rate cards by providing:

- **Standardization**: A common format that all systems can parse and validate
- **Automation**: Enable programmatic rate card ingestion and comparison
- **Interoperability**: Seamless integration between carriers, billing platforms, and routing systems
- **Version Control**: Built-in versioning and effective date management
- **Extensibility**: Forward-compatible design allowing custom fields without breaking parsers

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

### Validation

#### Using ajv-cli (Command Line)

```bash
# Install ajv-cli globally
npm install -g ajv-cli ajv-formats

# Validate your rate card
ajv validate -s schema/open-rate-card.schema.json -d your-rate-card.json --strict=false
```

#### Using Online Validators

- [JSON Schema Validator](https://www.jsonschemavalidator.net/) - Paste the schema and your JSON
- [JSON Schema Lint](https://jsonschemalint.com/) - Alternative online validator

#### Validation Examples

**✅ Passing Validation:**
```bash
$ ajv validate -s schema/open-rate-card.schema.json -d example.json
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

- **[SPECIFICATION.md](SPECIFICATION.md)** - Complete field reference with all 15 top-level sections
- **[CARD-TYPES.md](CARD-TYPES.md)** - Detailed guide to termination, origination, and messaging cards
- **[GLOSSARY.md](GLOSSARY.md)** - Telecommunications terminology reference (ACD, ASR, NER, STIR/SHAKEN, etc.)
- **[VERSIONING.md](VERSIONING.md)** - Schema evolution strategy and backward compatibility guidelines

### Contributing

- **[CONTRIBUTING.md](CONTRIBUTING.md)** - How to propose new fields, card types, or improvements via GitHub Issues

### Schema

- **[schema/open-rate-card.schema.json](schema/open-rate-card.schema.json)** - JSON Schema for validation

## 🏗️ Project Structure

```
open-rate-card/
├── README.md                          # This file
├── SPECIFICATION.md                   # Complete specification
├── CARD-TYPES.md                      # Card type documentation
├── GLOSSARY.md                        # Terminology reference
├── VERSIONING.md                      # Versioning strategy
├── CONTRIBUTING.md                    # Contribution guidelines
├── LICENSE                            # MIT License
├── example.json                       # Comprehensive example
├── schema/
│   └── open-rate-card.schema.json    # JSON Schema
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

- `name` - Rate card or provider name
- `schema_version` - Open Rate Card specification version (semantic versioning)
- `version` - Rate card document version
- `date` - Creation date (YYYY-MM-DD)
- `cards` - At least one rate card

### Card Level (4 required fields per card)

- `name` - Card name
- `type` - Card type (termination, origination, messaging, etc.)
- `currency` - ISO 4217 currency code (e.g., USD, EUR, GBP)
- `endpoint` - Reference to an endpoint name

## 🌐 Target Audience

- **VoIP Carriers** - Wholesale voice providers
- **Telecommunications Companies** - Retail and enterprise service providers
- **System Integrators** - Building billing and routing platforms
- **Rate Deck Management Systems** - Automated rate comparison and ingestion
- **Network Operations** - Traffic routing and quality management

## 🔄 Forward Compatibility

This specification is designed as an **evolving minimum requirement**:

- ✅ **Additional properties are allowed** - Add custom fields to any section without breaking validation
- ✅ **Parsers must ignore unknown fields** - Gracefully handle extensions they don't recognize
- ✅ **No breaking changes to existing fields** - Fields are never removed, only deprecated
- ✅ **Community-driven evolution** - Propose extensions via [GitHub Issues](https://github.com/connexcs/open-rate-card/issues)

The fields documented in this specification represent the **minimum standard** for interoperability. Implementations can extend any section with additional fields relevant to their use case, ensuring the specification grows with industry needs while maintaining backward compatibility.

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

1. **Propose new fields or card types** - Open a [GitHub Issue](https://github.com/connexcs/open-rate-card/issues/new/choose) using our templates
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

- **GitHub Repository**: [connexcs/open-rate-card](https://github.com/connexcs/open-rate-card)
- **Issue Tracker**: [Report issues or propose features](https://github.com/connexcs/open-rate-card/issues)
- **Discussions**: [Join the conversation](https://github.com/connexcs/open-rate-card/discussions)

## 📊 Version

- **Current Schema Version**: 1.0.0
- **Last Updated**: January 3, 2026

---

**Questions?** Open an issue or start a discussion on GitHub!
