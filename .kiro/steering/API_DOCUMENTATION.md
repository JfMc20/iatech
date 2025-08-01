# IATECH Frontend API Documentation

## Overview

This document provides comprehensive documentation for all API endpoints in the IATECH frontend application. The frontend acts as a proxy layer between the client and the backend services, providing additional validation, caching, and error handling.

## 🏗️ Backend Architecture Update (Clean Architecture)

The backend has been refactored following Clean Architecture principles with clear separation of concerns:

### Architecture Layers
```
Frontend Proxy → Backend Routes → Controllers → Services → Models
```

### Service Discovery Module (Refactored)
- **Routes**: `src/routes/serviceDiscovery.ts` - Clean endpoint definitions
- **Controllers**: `src/controllers/serviceDiscoveryController.ts` - HTTP handling
- **Services**: `src/services/serviceDiscoveryService.ts` - Business logic
- **Middleware**: `src/middleware/serviceDiscoveryMiddleware.ts` - Validation & security
- **Schemas**: `src/validation/serviceDiscoverySchemas.ts` - Zod validation

## Base Configuration

### Environment Variables
```bash
NEXT_PUBLIC_API_URL=https://iatech-backend.onrender.com
NEXT_PUBLIC_BACKEND_URL=https://iatech-backend.onrender.com
```

### Backend Base URL
All endpoints proxy to: `https://iatech-backend.onrender.com`

## API Endpoints

### 📦 Package Management

#### GET /api/packages
**Description**: Retrieve packages with filtering, pagination, and caching
**Proxy Target**: `${BACKEND_URL}/api/packages`

**Features**:
- Advanced filtering with PackageFilterManager
- Rate limiting (1 request per second per IP)
- Response caching
- Input sanitization and validation

**Query Parameters**:
- `page` - Page number for pagination
- `limit` - Number of items per page
- `search` - Search term for package names/descriptions
- `category` - Filter by package category
- `sortBy` - Sort field (newest, price, popularity)
- `sortOrder` - Sort direction (asc, desc)
- `isActive` - Filter active packages only

**Response Format**:
```typescript
{
  success: boolean;
  data: {
    data: Package[];
    meta: {
      page: number;
      totalPages: number;
      totalCount: number;
      limit: number;
      hasNextPage: boolean;
      hasPrevPage: boolean;
    }
  }
}
```

**Error Handling**:
- 429: Rate limit exceeded
- 500: Backend connection error
- Validation errors logged but don't block requests

---

#### GET /api/packages/featured
**Description**: Get featured packages (newest, active packages)
**Proxy Target**: `${BACKEND_URL}/api/packages?limit=6&sortBy=newest&sortOrder=desc&isActive=true`

**Query Parameters**:
- `limit` - Number of featured packages (default: 6)

**Response Format**:
```typescript
{
  success: boolean;
  data: Package[];
}
```

---

#### GET /api/packages/search
**Description**: Search packages by query term
**Proxy Target**: `${BACKEND_URL}/api/packages?search={query}&limit={limit}`

**Query Parameters**:
- `q` - Search query (minimum 3 characters)
- `limit` - Maximum results (default: 10)

**Validation**:
- Returns empty array if query < 3 characters
- URL encodes search query for safety

**Response Format**:
```typescript
{
  success: boolean;
  data: Package[];
}
```

---

#### GET /api/packages/[id]
**Description**: Get specific package by ID
**Proxy Target**: `${BACKEND_URL}/api/packages/{id}`

**Path Parameters**:
- `id` - Package ID (string, required)

**Validation**:
- ID must be non-empty string
- URL encodes ID parameter

**Error Handling**:
- 400: Invalid package ID
- 404: Package not found
- 500: Backend error

---

#### GET /api/packages/[id]/related
**Description**: Get related packages for a specific package
**Proxy Target**: `${BACKEND_URL}/api/packages/{id}/related`

**Path Parameters**:
- `id` - Package ID (string, required)

---

#### GET /api/packages/search/suggestions
**Description**: Get search suggestions for autocomplete
**Proxy Target**: `${BACKEND_URL}/api/packages/search/suggestions`

---

### 🔍 Service Discovery System

#### GET /api/service-discovery/questions
**Description**: Get all quiz questions for service discovery
**Proxy Target**: `${BACKEND_URL}/api/service-discovery/questions`

**Response Format**:
```typescript
{
  success: boolean;
  data: QuizQuestion[];
}

interface QuizQuestion {
  _id: string;
  type: 'single_choice' | 'multiple_choice' | 'scale' | 'text';
  category: 'business_profile' | 'current_situation' | 'goals' | 'budget' | 'timeline';
  text: string;
  options?: QuizOption[];
  required: boolean;
  order: number;
}
```

---

#### POST /api/service-discovery/submit
**Description**: Submit quiz answers and generate business assessment
**Proxy Target**: `${BACKEND_URL}/api/service-discovery/submit`

**Request Body**:
```typescript
{
  sessionId: string;
  answers: QuizAnswer[];
  userId?: string; // Optional
}

interface QuizAnswer {
  questionId: string;
  value: string | string[] | number;
  type: 'single_choice' | 'multiple_choice' | 'scale' | 'text';
}
```

**Validation**:
- Content-Type must be application/json
- `answers` must be non-empty array
- `sessionId` is required
- Comprehensive JSON parsing error handling

**Response Format**:
```typescript
{
  success: boolean;
  data: {
    assessmentId: string;
    businessProfile: BusinessProfile;
    recommendations: ServiceRecommendation[];
    roadmap: RoadmapPhase[];
    totalInvestment: number;
    estimatedROI: number;
  }
}
```

**Error Handling**:
- 400: Invalid JSON, missing required fields, invalid content type
- 500: Backend processing error
- Detailed error logging for debugging

---

#### GET /api/service-discovery/assessment/[id]
**Description**: Retrieve generated business assessment by ID
**Proxy Target**: `${BACKEND_URL}/api/service-discovery/assessment/{id}`

**Path Parameters**:
- `id` - Assessment ID (string, required)

**Validation**:
- ID must be non-empty string
- URL encodes assessment ID

**Error Handling**:
- 400: Invalid assessment ID
- 404: Assessment not found
- 500: Backend error

**Response Format**:
```typescript
{
  success: boolean;
  data: BusinessAssessment;
}

interface BusinessAssessment {
  _id: string;
  sessionId: string;
  userId?: string;
  businessProfile: BusinessProfile;
  currentSituation: CurrentSituation;
  recommendations: ServiceRecommendation[];
  roadmap: RoadmapPhase[];
  totalInvestment: number;
  estimatedROI: number;
  createdAt: string;
}
```

---

#### POST /api/service-discovery/progress
**Description**: Save quiz progress for later completion
**Proxy Target**: `${BACKEND_URL}/api/service-discovery/progress`

**Request Body**:
```typescript
{
  sessionId: string;
  currentStep: number;
  answers: QuizAnswer[];
  completedAt?: string;
}
```

**Validation**:
- Content-Type must be application/json
- `sessionId` is required
- JSON parsing with error handling

**Response Format**:
```typescript
{
  success: boolean;
  message: string;
}
```

---

#### GET /api/service-discovery/progress/[sessionId]
**Description**: Load saved quiz progress by session ID
**Proxy Target**: `${BACKEND_URL}/api/service-discovery/progress/{sessionId}`

**Path Parameters**:
- `sessionId` - Session ID (string, required)

**Validation**:
- SessionId must be non-empty string
- URL encodes session ID

**Error Handling**:
- 400: Invalid sessionId
- 404: No saved progress found
- 500: Backend error

**Response Format**:
```typescript
{
  success: boolean;
  data: {
    sessionId: string;
    currentStep: number;
    answers: QuizAnswer[];
    lastSaved: string;
  }
}
```

---

#### POST /api/service-discovery/create-inquiry
**Description**: Convert service discovery assessment into formal inquiry
**Proxy Target**: `${BACKEND_URL}/api/service-discovery/create-inquiry`

**Request Body**:
```typescript
{
  assessmentId?: string;
  contactInfo?: {
    name: string;
    email: string;
    phone?: string;
    company?: string;
  };
  additionalNotes?: string;
}
```

**Features**:
- Handles empty request bodies gracefully
- Optional JSON content with validation
- Safe content-length checking

**Response Format**:
```typescript
{
  success: boolean;
  data: {
    inquiryId: string;
    status: string;
    message: string;
  }
}
```

---

### ⚙️ Setup & Administration

#### POST /api/setup/seed
**Description**: Seed database with initial data (admin only)
**Proxy Target**: `${BACKEND_URL}/api/setup/seed`

**Authentication**: Admin access required (backend validation)

**Response Format**:
```typescript
{
  success: boolean;
  message: string;
  data?: any;
}
```

**Error Handling**:
- 500: Seeding failed
- Backend error details included

---

#### POST /api/setup/seed-service-discovery
**Description**: Seed service discovery questions and templates
**Proxy Target**: `${BACKEND_URL}/api/setup/seed-service-discovery`

**Authentication**: Admin access required (backend validation)

---

## Error Handling Patterns

### Standard Error Response
```typescript
{
  success: false;
  message: string;
  error: string;
}
```

### Common HTTP Status Codes
- `200` - Success
- `400` - Bad Request (validation errors)
- `404` - Not Found
- `429` - Too Many Requests (rate limiting)
- `500` - Internal Server Error

### Error Logging
- All errors logged with `console.error`
- Validation warnings with `console.warn`
- Request details included for debugging

## Security Features

### Rate Limiting
- Implemented on `/api/packages` endpoint
- 1 request per second per IP address
- IP detection via headers: `x-forwarded-for`, `x-real-ip`

### Input Validation
- JSON parsing with comprehensive error handling
- URL parameter sanitization
- Content-Type validation for POST requests
- Path parameter encoding for security

### Caching
- Response caching on packages endpoint
- Cache key generation based on filters
- Automatic cache invalidation

## Performance Optimizations

### PackageFilterManager
- Singleton pattern for filter management
- Input sanitization and validation
- Cache key generation
- Response caching with TTL

### Request Optimization
- Minimal request intervals
- Efficient parameter passing
- Response transformation for frontend needs

## Integration Notes

### Backend Communication
- All endpoints proxy to backend services
- Consistent error handling across endpoints
- Request/response transformation when needed
- Proper HTTP method forwarding

### Frontend Integration
- TypeScript interfaces for all responses
- Consistent error handling patterns
- Loading states and error boundaries supported
- Real-time validation feedback

## Development Guidelines

### Adding New Endpoints
1. Create route file in appropriate directory
2. Implement proper validation
3. Add error handling with logging
4. Include TypeScript interfaces
5. Update this documentation

### Testing Endpoints
```bash
# Test packages endpoint
curl http://localhost:3000/api/packages

# Test service discovery questions
curl http://localhost:3000/api/service-discovery/questions

# Test quiz submission
curl -X POST http://localhost:3000/api/service-discovery/submit \
  -H "Content-Type: application/json" \
  -d '{"sessionId":"test","answers":[]}'
```

### Environment Setup
```bash
# Required environment variables
NEXT_PUBLIC_API_URL=https://iatech-backend.onrender.com
NEXT_PUBLIC_BACKEND_URL=https://iatech-backend.onrender.com
```

## Monitoring & Debugging

### Logging Levels
- `console.error` - Critical errors requiring attention
- `console.warn` - Warnings and validation issues
- Debug information included in error responses

### Health Checks
- Backend connectivity validated on each request
- Proper error propagation from backend
- Timeout handling for slow responses

---

**Last Updated**: January 2025
**Version**: 1.0.0
**Maintainer**: IATECH Development Team