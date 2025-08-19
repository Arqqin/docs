# Property Management API Integration Guide

## Overview

This document provides comprehensive API documentation for property management software companies to integrate with the Arqqin Residential Parking Management System. The API allows property management companies to manage parking access for their residents across multiple residential locations.

## System Architecture

The API follows a multi-tenant architecture where:
- Each property management company has a unique API key
- API keys are scoped to specific locations
- Residents are identified by unique identifiers (not personal information)
- Vehicle management is handled through the property management system

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

### 2. Resident Vehicle Management

#### List Resident Vehicles
**GET** `/locations/{locationId}/residents/{residentId}/vehicles`

Returns all vehicles registered for a specific resident at a location.

**Parameters:**
- `locationId` (string, required): The location ID
- `residentId` (string, required): The resident's unique identifier

**Response:**
```json
{
  "success": true,
  "data": [
    {
      "id": "vehicle_456",
      "plateNumber": "DXB 12345",
      "plateType": "Private",
      "plateColor": "White",
      "vehicleType": "Sedan",
      "isActive": true,
      "addedAt": "2024-01-15T10:00:00Z",
      "lastSeen": "2024-01-20T15:30:00Z"
    }
  ]
}
```

#### Add Vehicle for Resident
**POST** `/locations/{locationId}/residents/{residentId}/vehicles`

Adds a new vehicle for a resident at a specific location.

**Request Body:**
```json
{
  "plateNumber": "DXB 67890",
  "plateType": "Private",
  "plateColor": "White",
  "vehicleType": "SUV",
  "notes": "Resident's second vehicle"
}
```

**Response:**
```json
{
  "success": true,
  "data": {
    "id": "vehicle_789",
    "plateNumber": "DXB 67890",
    "plateType": "Private",
    "plateColor": "White",
    "vehicleType": "SUV",
    "isActive": true,
    "addedAt": "2024-01-20T16:00:00Z"
  }
}
```

#### Remove Vehicle from Resident
**DELETE** `/locations/{locationId}/residents/{residentId}/vehicles/{vehicleId}`

Removes a vehicle from a resident's account.

**Response:**
```json
{
  "success": true,
  "message": "Vehicle removed successfully"
}
```

#### Update Vehicle Information
**PUT** `/locations/{locationId}/residents/{residentId}/vehicles/{vehicleId}`

Updates vehicle information for a resident.

**Request Body:**
```json
{
  "plateNumber": "DXB 67890",
  "plateType": "Private",
  "plateColor": "White",
  "vehicleType": "SUV",
  "notes": "Updated notes"
}
```

**Response:**
```json
{
  "success": true,
  "data": {
    "id": "vehicle_789",
    "plateNumber": "DXB 67890",
    "plateType": "Private",
    "plateColor": "White",
    "vehicleType": "SUV",
    "notes": "Updated notes",
    "isActive": true,
    "updatedAt": "2024-01-20T16:30:00Z"
  }
}
```

### 3. Resident Management

#### Create Resident
**POST** `/locations/{locationId}/residents`

Creates a new resident at a specific location.

**Request Body:**
```json
{
  "residentId": "RES_001",
  "maxVehicles": 2,
  "notes": "Apartment 101"
}
```

**Response:**
```json
{
  "success": true,
  "data": {
    "id": "resident_123",
    "residentId": "RES_001",
    "locationId": "location_123",
    "maxVehicles": 2,
    "currentVehicles": 0,
    "notes": "Apartment 101",
    "isActive": true,
    "createdAt": "2024-01-20T16:00:00Z"
  }
}
```

#### Update Resident
**PUT** `/locations/{locationId}/residents/{residentId}`

Updates resident information.

**Request Body:**
```json
{
  "maxVehicles": 3,
  "notes": "Apartment 101 - Updated"
}
```

**Response:**
```json
{
  "success": true,
  "data": {
    "id": "resident_123",
    "residentId": "RES_001",
    "locationId": "location_123",
    "maxVehicles": 3,
    "currentVehicles": 1,
    "notes": "Apartment 101 - Updated",
    "isActive": true,
    "updatedAt": "2024-01-20T16:30:00Z"
  }
}
```

#### Deactivate Resident
**DELETE** `/locations/{locationId}/residents/{residentId}`

Deactivates a resident (soft delete).

**Response:**
```json
{
  "success": true,
  "message": "Resident deactivated successfully"
}
```

### 4. Parking Activity

#### Get Resident Parking History
**GET** `/locations/{locationId}/residents/{residentId}/parking-history`

Returns parking history for a specific resident.

**Query Parameters:**
- `startDate` (string, optional): Start date in ISO format
- `endDate` (string, optional): End date in ISO format
- `limit` (number, optional): Maximum number of records to return (default: 50)

**Response:**
```json
{
  "success": true,
  "data": [
    {
      "id": "session_789",
      "plateNumber": "DXB 12345",
      "entryTime": "2024-01-20T10:00:00Z",
      "exitTime": "2024-01-20T18:00:00Z",
      "duration": "8h 0m",
      "status": "completed",
      "entryGate": "Main Entrance",
      "exitGate": "Main Exit"
    }
  ]
}
```

#### Get Current Parking Status
**GET** `/locations/{locationId}/residents/{residentId}/current-parking`

Returns current parking status for a resident.

**Response:**
```json
{
  "success": true,
  "data": {
    "currentlyParked": true,
    "activeSessions": [
      {
        "id": "session_789",
        "plateNumber": "DXB 12345",
        "entryTime": "2024-01-20T10:00:00Z",
        "currentDuration": "2h 30m",
        "entryGate": "Main Entrance"
      }
    ]
  }
}
```

### 5. System Status

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

### Resident
```typescript
interface Resident {
  id: string;
  residentId: string;        // Unique identifier from property management system
  locationId: string;        // Location where resident is registered
  maxVehicles: number;       // Maximum allowed vehicles
  currentVehicles: number;   // Current number of registered vehicles
  notes?: string;            // Additional notes
  isActive: boolean;         // Whether resident is active
  createdAt: string;         // ISO timestamp
  updatedAt: string;         // ISO timestamp
}
```

### Vehicle
```typescript
interface Vehicle {
  id: string;
  residentId: string;        // Associated resident ID
  locationId: string;        // Location where vehicle is registered
  plateNumber: string;       // License plate number
  plateType: string;         // Type of plate (Private, etc.)
  plateColor: string;        // Color of plate
  vehicleType: string;       // Type of vehicle (Sedan, SUV, etc.)
  notes?: string;            // Additional notes
  isActive: boolean;         // Whether vehicle is active
  addedAt: string;           // ISO timestamp
  lastSeen?: string;         // Last time vehicle was detected
  updatedAt: string;         // ISO timestamp
}
```

### Parking Session
```typescript
interface ParkingSession {
  id: string;
  locationId: string;        // Location where session occurred
  plateNumber: string;       // License plate number
  entryTime: string;         // Entry timestamp
  exitTime?: string;         // Exit timestamp (if session closed)
  duration?: string;         // Duration in human-readable format

  status: string;            // Session status
  entryGate: string;         // Entry gate name
  exitGate?: string;         // Exit gate name (if session closed)
}
```

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

  // Add vehicle for resident
  async addVehicle(locationId, residentId, vehicleData) {
    try {
      const response = await this.client.post(
        `/locations/${locationId}/residents/${residentId}/vehicles`,
        vehicleData
      );
      return response.data;
    } catch (error) {
      console.error('Error adding vehicle:', error.response?.data || error.message);
      throw error;
    }
  }

  // Get resident parking history
  async getParkingHistory(locationId, residentId, options = {}) {
    try {
      const params = new URLSearchParams();
      if (options.startDate) params.append('startDate', options.startDate);
      if (options.endDate) params.append('endDate', options.endDate);
      if (options.limit) params.append('limit', options.limit);

      const response = await this.client.get(
        `/locations/${locationId}/residents/${residentId}/parking-history?${params}`
      );
      return response.data;
    } catch (error) {
      console.error('Error fetching parking history:', error.response?.data || error.message);
      throw error;
    }
  }
}

// Usage example
const api = new ArqqinParkingAPI('your-api-key', 'https://apistg.arqq.in/api/');

// Get all locations
api.getLocations()
  .then(locations => console.log('Locations:', locations))
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

    def add_vehicle(self, location_id: str, resident_id: str, vehicle_data: Dict[str, Any]) -> Dict[str, Any]:
        """Add a vehicle for a resident"""
        endpoint = f"/locations/{location_id}/residents/{resident_id}/vehicles"
        return self._make_request('POST', endpoint, vehicle_data)

    def get_parking_history(self, location_id: str, resident_id: str, 
                           start_date: Optional[str] = None, 
                           end_date: Optional[str] = None,
                           limit: Optional[int] = None) -> Dict[str, Any]:
        """Get parking history for a resident"""
        params = []
        if start_date:
            params.append(f"startDate={start_date}")
        if end_date:
            params.append(f"endDate={end_date}")
        if limit:
            params.append(f"limit={limit}")
        
        query_string = "&".join(params)
        endpoint = f"/locations/{location_id}/residents/{resident_id}/parking-history"
        if query_string:
            endpoint += f"?{query_string}"
        
        return self._make_request('GET', endpoint)

# Usage example
api = ArqqinParkingAPI('your-api-key', 'https://apistg.arqq.in/api/')

# Get all locations
try:
    locations = api.get_locations()
    print("Locations:", locations)
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

### Version 1.2.0 (2024-08-07)
- Initial API release
- Location management
- Resident and vehicle management
- Parking activity tracking

---

*This document is version 1.2.0 and was last updated on August 07, 2024.*
