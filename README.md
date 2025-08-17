# Student Rooms Management API

A comprehensive REST API specification for managing students and rooms designed with OpenAPI 3.0.

## Table of Contents
- [Overview](#overview)
- [Features](#features)
- [API Documentation](#api-documentation)
- [Authentication](#authentication)
- [Rate Limiting](#rate-limiting)
- [Endpoints](#endpoints)
- [Data Models](#data-models)
- [Error Handling](#error-handling)
- [Usage Examples](#usage-examples)
- [Response Format](#response-format)
- [Support](#support)
- [License](#license)

## Overview

This project provides a complete OpenAPI 3.0 specification for a Student Rooms Management API. It defines all endpoints, request/response schemas, authentication, business rules, and comprehensive features for managing students and room assignments in an educational institution.

## Features

### Core Features
- Complete CRUD operations for students and rooms
- Student room assignment management with capacity constraints
- Advanced filtering and search capabilities
- Pagination support for all list endpoints
- API key authentication with rate limiting
- Comprehensive error handling and validation

### Advanced Features
- **Bulk Operations**: Move multiple students simultaneously
- **Health Monitoring**: Health check endpoint with system information
- **Audit Trail**: Track creation and modification timestamps
- **Capacity Management**: Enforce room capacity limits (1-20 students)
- **Rate Limiting**: 100 requests/minute, 1000 requests/hour per API key
- **Business Rules**: Prevent deletion of occupied rooms, enforce unique names

## API Documentation

This project contains a complete OpenAPI 3.0 specification that can be used with various API documentation tools:

### Viewing the Documentation

1. **Swagger Editor**: 
   - Go to https://editor.swagger.io/
   - Copy and paste the contents of `docs/specs/openapi.yaml`
   - View the interactive documentation

2. **Swagger UI**:
   - Import the `docs/specs/openapi.yaml` file into any Swagger UI instance
   - View the organized API documentation with all endpoints

3. **Postman**:
   - Import the `docs/specs/openapi.yaml` file into Postman
   - Generate a complete API collection with all endpoints

4. **ReDoc**:
   - Import the `docs/specs/openapi.yaml` file into ReDoc
   - View a clean, responsive documentation

### Project Structure
```
OpenAPI-student-rooms/
├── docs/
│   └── specs/
│       └── openapi.yaml  # Complete OpenAPI 3.0 specification
├── data/
│   ├── students.json     # Sample student data
│   └── rooms.json        # Sample room data
├── README.md             # This documentation
└── .gitignore           # Git ignore file
```

## Authentication

This API uses API key authentication. Include your API key in the `X-API-Key` header with every request:

```bash
curl -H "X-API-Key: your-api-key-here" \
     https://api.studentrooms.com/v1/students
```

### Test API Key
For testing purposes, use: `test-api-key`

## Rate Limiting

The API implements rate limiting to ensure fair usage:
- **100 requests per minute** per API key
- **1000 requests per hour** per API key
- Rate limit information is included in response headers:
  - `X-RateLimit-Limit`: Maximum requests per window
  - `X-RateLimit-Remaining`: Remaining requests in current window
  - `X-RateLimit-Reset`: Time when the rate limit resets (Unix timestamp)
- When rate limit is exceeded, the API returns `429 Too Many Requests`

## Endpoints

The API is organized into four main categories:

### Health
Health check and monitoring endpoints:
- `GET /health` - Check API health status and system information

### Students
Operations for managing student records:
- `GET /students` - Get all students with pagination and filtering
- `POST /students` - Create a new student
- `GET /students/{id}` - Get a specific student
- `PUT /students/{id}` - Update a student
- `DELETE /students/{id}` - Delete a student

### Rooms
Operations for managing room records:
- `GET /rooms` - Get all rooms with pagination and search
- `POST /rooms` - Create a new room (with capacity specification)
- `GET /rooms/{id}` - Get a specific room
- `PUT /rooms/{id}` - Update a room
- `DELETE /rooms/{id}` - Delete a room (only if empty)

### Room Assignment
Operations for managing student room assignments:
- `POST /students/{id}/move` - Move student to a different room
- `POST /students/bulk-move` - Move multiple students to different rooms
- `GET /rooms/{id}/students` - Get all students in a specific room

## Data Models

### Student
```json
{
  "id": 1,
  "name": "John Doe",
  "birthday": "2000-01-15T00:00:00.000000",
  "room": 101,
  "sex": "M",
  "created_at": "2024-01-15T10:30:00Z",
  "updated_at": "2024-01-15T10:30:00Z"
}
```

### Room
```json
{
  "id": 101,
  "name": "Room #101",
  "capacity": 4,
  "student_count": 2,
  "created_at": "2024-01-15T10:30:00Z",
  "updated_at": "2024-01-15T10:30:00Z"
}
```

### Health Response
```json
{
  "status": "healthy",
  "timestamp": "2024-01-15T10:30:00Z",
  "version": "1.0.0",
  "uptime": 3600,
  "students_count": 10000,
  "rooms_count": 1000,
  "message": "Student Rooms API is running normally"
}
```

## Error Handling

The API uses a standardized error response format:

```json
{
  "success": false,
  "error": {
    "code": "ERROR_CODE",
    "message": "Human-readable error message",
    "details": "Additional error details"
  }
}
```

### Common Error Codes
- `INVALID_API_KEY`: Authentication failed
- `STUDENT_NOT_FOUND`: Student with specified ID not found
- `ROOM_NOT_FOUND`: Room with specified ID not found
- `DUPLICATE_STUDENT`: Student name already exists
- `DUPLICATE_ROOM`: Room name already exists
- `ROOM_OCCUPIED`: Cannot delete room with students
- `ROOM_CAPACITY_EXCEEDED`: Room is at maximum capacity
- `VALIDATION_ERROR`: Request validation failed
- `RATE_LIMIT_EXCEEDED`: Rate limit exceeded

## Usage Examples

### Health Check
```bash
curl https://api.studentrooms.com/v1/health
```

### Get All Students
```bash
curl -H "X-API-Key: test-api-key" \
     "https://api.studentrooms.com/v1/students?page=1&limit=10"
```

### Create a New Student
```bash
curl -X POST -H "X-API-Key: test-api-key" \
     -H "Content-Type: application/json" \
     -d '{
       "name": "Jane Smith",
       "birthday": "2001-05-20T00:00:00.000000",
       "room": 101,
       "sex": "F"
     }' \
     https://api.studentrooms.com/v1/students
```

### Create a New Room with Capacity
```bash
curl -X POST -H "X-API-Key: test-api-key" \
     -H "Content-Type: application/json" \
     -d '{
       "name": "Room #201",
       "capacity": 4
     }' \
     https://api.studentrooms.com/v1/rooms
```

### Move Student to Different Room
```bash
curl -X POST -H "X-API-Key: test-api-key" \
     -H "Content-Type: application/json" \
     -d '{
       "new_room_id": 102,
       "reason": "Room change request"
     }' \
     https://api.studentrooms.com/v1/students/1/move
```

### Bulk Move Students
```bash
curl -X POST -H "X-API-Key: test-api-key" \
     -H "Content-Type: application/json" \
     -d '{
       "moves": [
         {
           "student_id": 1,
           "new_room_id": 105,
           "reason": "Room change request"
         },
         {
           "student_id": 2,
           "new_room_id": 106,
           "reason": "Room change request"
         }
       ]
     }' \
     https://api.studentrooms.com/v1/students/bulk-move
```

### Get Students in a Specific Room
```bash
curl -H "X-API-Key: test-api-key" \
     "https://api.studentrooms.com/v1/rooms/101/students?page=1&limit=20"
```

### Filter Students
```bash
# Filter by room
curl -H "X-API-Key: test-api-key" \
     "https://api.studentrooms.com/v1/students?room_id=101"

# Filter by gender
curl -H "X-API-Key: test-api-key" \
     "https://api.studentrooms.com/v1/students?sex=M"

# Search by name
curl -H "X-API-Key: test-api-key" \
     "https://api.studentrooms.com/v1/students?search=john"
```

## Response Format

All successful responses follow this format:
```json
{
  "success": true,
  "data": {
    // Response data
  },
  "message": "Success message"
}
```

List responses include pagination metadata:
```json
{
  "success": true,
  "data": {
    "students": [...],
    "pagination": {
      "page": 1,
      "limit": 20,
      "total": 100,
      "total_pages": 5
    }
  },
  "message": "Students retrieved successfully"
}
```

Bulk operation responses include detailed results:
```json
{
  "success": true,
  "data": {
    "moved_students": [...],
    "failed_moves": [...],
    "total_moved": 2,
    "total_failed": 0
  },
  "message": "Successfully moved 2 students"
}
```
