# Whitelist Vehicle API Integration Guide

## Overview

This document provides comprehensive API documentation for integrating with the Arqqin Parking Management System using a generic, reference-based vehicle whitelist. The API allows partners to manage parking access by adding, updating, and removing vehicles in a whitelist per location.

## System Architecture

The API follows below architecture where:
- API keys are scoped to specific locations
- Vehicles are managed in a per-location whitelist
- A generic `referenceId` (no PII) is used to group and manage one or more vehicles. The `referenceId` can represent any external entity (e.g., resident, contract, unit, fleet account).

## Base URL (Staging)

```
https://apistg.arqq.in/api/
```

## Authentication

All API requests require authentication using an API key in the request header:

```
X-API-Key: YOUR_API_KEY
```

## API Endpoints

### 1. Location Management

#### List Locations
**GET** `/locations`

Returns all locations accessible by the API key.

**Response:**
```json
{
  "success": true,
  "data": [
    {
      "id": "location_123",
      "name": "Downtown Residences",
      "address": "123 Main Street, Dubai",
      "city": "Dubai",
      "country": "UAE",
      "parkingCapacity": 150,
      "isActive": true,
      "createdAt": "2024-01-15T10:00:00Z"
    }
  ]
}
```

#### Get Location Details
**GET** `/locations/{locationId}`

Returns detailed information about a specific location.

**Response:**
```json
{
  "success": true,
  "data": {
    "id": "location_123",
    "name": "Downtown Residences",
    "description": "Luxury residential complex in downtown Dubai",
    "address": "123 Main Street, Dubai",
    "city": "Dubai",
    "state": "Dubai",
    "country": "UAE",
    "postalCode": "12345",
    "coordinates": {
      "lat": 25.2048,
      "lng": 55.2708
    },
    "timezone": "Asia/Dubai",
    "parkingCapacity": 150,
    "isActive": true,
    "createdAt": "2024-01-15T10:00:00Z",
    "updatedAt": "2024-01-15T10:00:00Z"
  }
}
```

### 2. Whitelist Vehicle Management

Manage location-level whitelist entries. Each entry represents a single vehicle associated with a `referenceId` that you control.

#### List Whitelist Vehicles
**GET** `/locations/{locationId}/whitelist`

Returns whitelist vehicles for a location, optionally filtered.

**Query Parameters:**
- `referenceId` (string, optional): Filter by a specific external reference (e.g residents, tenants, etc..)
- `plateType` (string, optional): Filter by plate type (e.g., `DXB`, `KSA`)
- `plateCode` (string, optional): Filter by plate code/color (e.g., `White`, `A`, `ABC`, `123`)
- `plateNumber` (string, optional): Digits-only plate number (e.g '12345', '123')
- `limit` (number, optional): Max items to return (default: 50)

**Response:**
```json
{
  "success": true,
  "data": [
    {
      "id": "whitelist_456",
      "locationId": "location_123",
      "referenceId": "REF_001",
      "plateType": "DXB",
      "plateCode": "White",
      "plateNumber": "12345",
      "vehicleType": "Sedan",
      "note": "Preferred entrance: Gate A",
      "addedAt": "2024-01-15T10:00:00Z",
      "updatedAt": "2024-01-20T15:30:00Z"
    }
  ]
}
```

#### Add Whitelist Vehicle
**POST** `/locations/{locationId}/whitelist`

Adds a new vehicle to the whitelist for a specific location, associated with a `referenceId`.

**Request Body:**
```json
{
  "referenceId": "REF_001",
  "plateType": "DXB",
  "plateCode": "White",
  "plateNumber": "67890",
  "vehicleType": "SUV",
  "note": "Second vehicle"
}
```

**Response:**
```json
{
  "success": true,
  "data": {
    "id": "whitelist_789",
    "locationId": "location_123",
    "referenceId": "REF_001",
    "plateType": "DXB",
    "plateCode": "White",
    "plateNumber": "67890",
    "vehicleType": "SUV",
    "note": "Second vehicle",
    "addedAt": "2024-01-20T16:00:00Z",
    "updatedAt": "2024-01-20T16:00:00Z"
  }
}
```

#### Remove Whitelist Vehicle
**DELETE** `/locations/{locationId}/whitelist/{whitelistId}`

Removes a vehicle from the location's whitelist.

**Response:**
```json
{
  "success": true,
  "message": "Vehicle removed successfully"
}
```

#### Update Whitelist Vehicle
**PUT** `/locations/{locationId}/whitelist/{whitelistId}`

Updates a whitelist vehicle.

**Request Body:**
```json
{
  "referenceId": "REF_001",
  "plateType": "DXB",
  "plateCode": "A",
  "plateNumber": "67890",
  "vehicleType": "SUV",
  "note": "Updated notes"
}
```

**Response:**
```json
{
  "success": true,
  "data": {
    "id": "whitelist_789",
    "locationId": "location_123",
    "referenceId": "REF_001",
    "plateType": "DXB",
    "plateCode": "A",
    "plateNumber": "67890",
    "vehicleType": "SUV",
    "note": "Updated notes",
    "updatedAt": "2024-01-20T16:30:00Z"
  }
}
```

### 3. System Status

#### Get Location Status

#### Get Location Status
**GET** `/locations/{locationId}/status`

Returns current status and statistics for a location.

**Response:**
```json
{
  "success": true,
  "data": {
    "totalCapacity": 150,
    "currentOccupancy": 89,
    "availableSpaces": 61,
    "occupancyRate": "59.3%",
    "activeSessions": 89,
    "gates": [
      {
        "id": "gate_1",
        "name": "Main Entrance",
        "type": "entry",
        "isActive": true
      },
      {
        "id": "gate_2",
        "name": "Main Exit",
        "type": "exit",
        "isActive": true
      }
    ],
    "lastUpdated": "2024-01-20T16:30:00Z"
  }
}
```

#### Health Check
**GET** `/health`

Returns API health status.

**Response:**
```json
{
  "status": "healthy",
  "timestamp": "2024-01-20T16:30:00Z",
  "service": "arqqin-parking-management",
  "version": "1.0.0"
}
```

## Error Handling

All API endpoints return consistent error responses:

### Error Response Format
```json
{
  "success": false,
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Invalid plate number format",
    "details": {
      "field": "plateNumber",
      "value": "INVALID_PLATE"
    }
  }
}
```

### Common Error Codes

| Code | HTTP Status | Description |
|------|-------------|-------------|
| `UNAUTHORIZED` | 401 | Invalid or missing API key |
| `FORBIDDEN` | 403 | API key doesn't have access to requested resource |
| `NOT_FOUND` | 404 | Resource not found |
| `VALIDATION_ERROR` | 400 | Invalid request data |
| `RATE_LIMIT_EXCEEDED` | 429 | Too many requests |
| `INTERNAL_ERROR` | 500 | Internal server error |

## Rate Limiting

- **Rate Limit**: 1000 requests per hour
- Rate limit headers are included in responses:
  - `X-RateLimit-Limit`: Maximum requests per hour
  - `X-RateLimit-Remaining`: Remaining requests in current hour
  - `X-RateLimit-Reset`: Time when rate limit resets (Unix timestamp)

## Data Models

### WhitelistVehicle
```typescript
interface WhitelistVehicle {
  id: string;
  locationId: string;     // Location where whitelist entry applies
  referenceId: string;    // External reference (e.g., resident, contract, unit, fleet)
  plateType: string;      // Issuing authority or region code (e.g., DXB, KSA)
  plateCode: string;      // 'White' or 1-3 alphanumeric code (e.g., A, AB, 123)
  plateNumber: string;    // Digits-only license number (e.g., 12345)
  vehicleType?: string;   // Vehicle type (e.g., Sedan, SUV)
  note?: string;          // Optional note
  addedAt: string;        // ISO timestamp
  updatedAt: string;      // ISO timestamp
}
```

Validation rules:
- plateNumber: digits only, regex: `^\d+$`
- plateType: uppercase letters only, 2-4 chars recommended (e.g., DXB, AUH, KSA)
- plateCode: either the literal `White` or 1-3 alphanumeric characters, regex: `^(White|[A-Za-z0-9]{1,3})$`

## Integration Examples

### JavaScript/Node.js Example

```javascript
const axios = require('axios');

class ArqqinParkingAPI {
  constructor(apiKey, baseURL) {
    this.apiKey = apiKey;
    this.baseURL = baseURL;
    this.client = axios.create({
      baseURL,
      headers: {
        'X-API-Key': apiKey,
        'Content-Type': 'application/json'
      }
    });
  }

  // List all locations
  async getLocations() {
    try {
      const response = await this.client.get('/locations');
      return response.data;
    } catch (error) {
      console.error('Error fetching locations:', error.response?.data || error.message);
      throw error;
    }
  }

  // List whitelist vehicles with optional filters
  async listWhitelist(locationId, filters = {}) {
    try {
      const params = new URLSearchParams();
      if (filters.referenceId) params.append('referenceId', filters.referenceId);
      if (filters.plateType) params.append('plateType', filters.plateType);
      if (filters.plateCode) params.append('plateCode', filters.plateCode);
      if (filters.plateNumber) params.append('plateNumber', filters.plateNumber);
      if (filters.limit) params.append('limit', String(filters.limit));

      const response = await this.client.get(`/locations/${locationId}/whitelist?${params}`);
      return response.data;
    } catch (error) {
      console.error('Error listing whitelist:', error.response?.data || error.message);
      throw error;
    }
  }

  // Add a vehicle to the whitelist
  async addWhitelistVehicle(locationId, whitelistEntry) {
    try {
      const response = await this.client.post(`/locations/${locationId}/whitelist`, whitelistEntry);
      return response.data;
    } catch (error) {
      console.error('Error adding whitelist vehicle:', error.response?.data || error.message);
      throw error;
    }
  }

  // Update a whitelist vehicle
  async updateWhitelistVehicle(locationId, whitelistId, updates) {
    try {
      const response = await this.client.put(`/locations/${locationId}/whitelist/${whitelistId}`, updates);
      return response.data;
    } catch (error) {
      console.error('Error updating whitelist vehicle:', error.response?.data || error.message);
      throw error;
    }
  }

  // Delete a whitelist vehicle
  async deleteWhitelistVehicle(locationId, whitelistId) {
    try {
      const response = await this.client.delete(`/locations/${locationId}/whitelist/${whitelistId}`);
      return response.data;
    } catch (error) {
      console.error('Error deleting whitelist vehicle:', error.response?.data || error.message);
      throw error;
    }
  }
}

// Usage example
const api = new ArqqinParkingAPI('your-api-key', 'https://apistg.arqq.in/api/');

// List whitelist vehicles for a reference
api.listWhitelist('location_123', { referenceId: 'REF_001' })
  .then(result => console.log('Whitelist:', result))
  .catch(error => console.error('Error:', error));
```

### Python Example

```python
import requests
from typing import Optional, Dict, Any

class ArqqinParkingAPI:
    def __init__(self, api_key: str, base_url: str):
        self.api_key = api_key
        self.base_url = base_url
        self.headers = {
            'X-API-Key': api_key,
            'Content-Type': 'application/json'
        }

    def _make_request(self, method: str, endpoint: str, data: Optional[Dict] = None) -> Dict[str, Any]:
        url = f"{self.base_url}{endpoint}"
        
        try:
            if method.upper() == 'GET':
                response = requests.get(url, headers=self.headers)
            elif method.upper() == 'POST':
                response = requests.post(url, headers=self.headers, json=data)
            elif method.upper() == 'PUT':
                response = requests.put(url, headers=self.headers, json=data)
            elif method.upper() == 'DELETE':
                response = requests.delete(url, headers=self.headers)
            else:
                raise ValueError(f"Unsupported HTTP method: {method}")

            response.raise_for_status()
            return response.json()
        except requests.exceptions.RequestException as e:
            print(f"API request failed: {e}")
            if hasattr(e, 'response') and e.response is not None:
                print(f"Response: {e.response.text}")
            raise

    def get_locations(self) -> Dict[str, Any]:
        """Get all accessible locations"""
        return self._make_request('GET', '/locations')

    def list_whitelist(self, location_id: str, 
                        reference_id: Optional[str] = None,
                        plate_type: Optional[str] = None,
                        plate_code: Optional[str] = None,
                        plate_number: Optional[str] = None,
                        limit: Optional[int] = None) -> Dict[str, Any]:
        """List whitelist vehicles for a location with optional filters"""
        params = []
        if reference_id:
            params.append(f"referenceId={reference_id}")
        if plate_type:
            params.append(f"plateType={plate_type}")
        if plate_code:
            params.append(f"plateCode={plate_code}")
        if plate_number:
            params.append(f"plateNumber={plate_number}")
        if limit:
            params.append(f"limit={limit}")

        query_string = "&".join(params)
        endpoint = f"/locations/{location_id}/whitelist"
        if query_string:
            endpoint += f"?{query_string}"
        return self._make_request('GET', endpoint)

    def add_whitelist_vehicle(self, location_id: str, entry: Dict[str, Any]) -> Dict[str, Any]:
        """Add a vehicle to the whitelist"""
        endpoint = f"/locations/{location_id}/whitelist"
        return self._make_request('POST', endpoint, entry)

    def update_whitelist_vehicle(self, location_id: str, whitelist_id: str, updates: Dict[str, Any]) -> Dict[str, Any]:
        """Update a vehicle in the whitelist"""
        endpoint = f"/locations/{location_id}/whitelist/{whitelist_id}"
        return self._make_request('PUT', endpoint, updates)

    def delete_whitelist_vehicle(self, location_id: str, whitelist_id: str) -> Dict[str, Any]:
        """Delete a vehicle from the whitelist"""
        endpoint = f"/locations/{location_id}/whitelist/{whitelist_id}"
        return self._make_request('DELETE', endpoint)

# Usage example
api = ArqqinParkingAPI('your-api-key', 'https://apistg.arqq.in/api/')

# List whitelist for a reference
try:
    result = api.list_whitelist('location_123', reference_id='REF_001')
    print("Whitelist:", result)
except Exception as e:
    print(f"Error: {e}")
```



## Security Considerations

1. **API Key Security**: Keep your API keys secure and never expose them in client-side code
2. **HTTPS**: All API communication is encrypted using HTTPS
3. **Rate Limiting**: Implement proper rate limiting in your integration
4. **Data Validation**: Always validate data before sending to the API
5. **Error Handling**: Implement proper error handling for all API calls

## Changelog

### Version 1.3.0 (2025-09-03)
- Replace resident-specific endpoints with generic whitelist vehicle management
- Introduce `referenceId` to group vehicles under any external entity
- Add validation for `plateType`, `plateCode`, and digits-only `plateNumber`

### Version 1.2.0 (2025-08-07)
- Initial API release
- Location management
- Resident and vehicle management
- Parking activity tracking

---

*This document is version 1.3.0 and was last updated on September 02, 2025.*
