# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Overview

This is the **Arqqin Parking Management API Documentation** repository, containing public API documentation for property management software companies to integrate parking access management for residential properties. The repository uses **Mintlify** as the documentation platform.

## Architecture & Structure

### Documentation Platform
- **Mintlify-based documentation**: All documentation is built using Mintlify platform
- **Staging API**: All examples use `https://apistg.arqq.in/api/` as the base URL
- **Migration Target**: Documentation will eventually move to `https://developers.arqqin.com`

### Core API Architecture
- **Multi-tenant**: Each property management company has scoped API access
- **Location-based**: API keys are scoped to specific locations
- **Reference-based vehicle management**: Uses generic `referenceId` (no PII) to group vehicles
- **RESTful endpoints**: Standard HTTP methods with JSON request/response

### Key Concepts
- **WhitelistVehicle**: Core entity representing vehicles allowed at locations
- **Location**: Residential properties managed by the system
- **ReferenceId**: External identifier to group vehicles (residents, contracts, units, etc.)
- **Plate validation**: Strict rules for `plateType`, `plateCode`, and `plateNumber`

## File Structure

```
/
├── README.md                     # Repository overview and quick start
├── api.md                        # Complete legacy API documentation
└── mintlify/                     # Mintlify documentation source
    ├── mint.json                 # Mintlify configuration
    ├── openapi.json              # OpenAPI spec for API reference
    ├── *.mdx                     # Documentation pages
    └── guides/                   # Integration guides and examples
        ├── whitelist-management.mdx
        ├── error-handling.mdx
        └── integration-examples.mdx
```

## Development Commands

### Documentation Development
Since this is a documentation-only repository, there are no build commands. Content is managed through:
- Direct editing of `.mdx` files in the `mintlify/` directory
- Updates to `mint.json` for navigation and configuration
- OpenAPI spec updates in `openapi.json`

### Content Validation
- Ensure all API examples use the staging URL: `https://apistg.arqq.in/api/`
- Validate plate number formats follow regex: `^\d+$` (digits only)
- Verify `plateCode` follows: `^(White|[A-Za-z0-9]{1,3})$`
- Check `plateType` uses uppercase letters (e.g., DXB, KSA, AUH)

## Key API Endpoints Structure

### Core Patterns
- **Authentication**: `X-API-Key` header required for all requests
- **Base URL**: `https://apistg.arqq.in/api/`
- **Response format**: All responses follow `{success: boolean, data: object}` pattern
- **Error format**: `{success: false, error: {code, message, details}}`

### Primary Endpoints
- `GET /locations` - List accessible locations
- `GET /locations/{id}/whitelist` - List whitelist vehicles
- `POST /locations/{id}/whitelist` - Add vehicle to whitelist
- `PUT /locations/{id}/whitelist/{vehicleId}` - Update whitelist vehicle
- `DELETE /locations/{id}/whitelist/{vehicleId}` - Remove from whitelist
- `GET /locations/{id}/status` - Get location parking status

## Documentation Standards

### Content Guidelines
- Use staging API URLs in all examples
- Provide code examples in cURL, JavaScript, and Python
- Include error handling in all integration examples
- Use realistic but non-sensitive data in examples
- Reference the upcoming migration to `developers.arqqin.com`

### Mintlify-Specific
- Use `.mdx` format for all content files
- Configure navigation in `mint.json`
- OpenAPI reference auto-generated from `openapi.json`
- Maintain consistent branding (colors: primary #2563eb, gradient to #7c3aed)

### Example Data Patterns
- LocationId: `location_123`
- ReferenceId: `REF_001`, `RESIDENT_001`
- PlateType: `DXB`, `KSA`, `AUH`
- PlateCode: `White`, `A`, `ABC`, `123`
- PlateNumber: `12345`, `67890` (digits only)

## Contact & Migration Notes

- **Current contact**: hello@arqqin.com
- **Future platform**: https://developers.arqqin.com
- **API staging**: https://apistg.arqq.in/api/
- **Company website**: https://arqqin.com

When updating documentation, always consider the eventual migration to the developers portal and maintain consistency with the staging API environment.