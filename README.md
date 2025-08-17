# Student Rooms Management API

A comprehensive REST API specification for managing students and rooms designed with OpenAPI 3.0.3.

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

## Overview

This project provides a complete OpenAPI 3.0.3 specification for a Student Rooms Management API. It defines all endpoints, request/response schemas, JWT authentication, business rules, and comprehensive enterprise features for managing students and room assignments in an educational institution.

## Features

### Core Features
- Complete CRUD operations for students and rooms
- Student room assignment management with capacity constraints
- Advanced filtering and search capabilities
- Pagination support with X-Total-Count headers
- JWT Bearer token authentication
- Comprehensive error handling and validation

### Enterprise Features
- **JWT Authentication**: Secure Bearer token authentication
- **Rate Limiting**: 100 requests/minute, 1000 requests/hour per user
- **Health Monitoring**: Health check endpoint with system metrics
- **Bulk Operations**: Move multiple students simultaneously with transaction support
- **Audit Trail**: Track creation/modification timestamps and move reasons
- **Capacity Management**: Enforce room capacity limits (1-50 students)
- **Business Rules**: Prevent deletion of occupied rooms, enforce unique names
- **Search & Filtering**: Name search, room filtering, gender filtering
- **System Metrics**: Real-time occupancy and system statistics

## API Documentation

This project contains a complete OpenAPI 3.0.3 specification that can be used with various API documentation tools:

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
│       └── openapi.yaml  # Complete OpenAPI 3.0.3 specification
├── data/
│   ├── students.json     # Sample student data
│   └── rooms.json        # Sample room data
├── README.md             # This documentation
└── .gitignore           # Git ignore file
```

## Authentication

This API uses JWT Bearer token authentication. Include your JWT token in the `Authorization` header with every request:

```bash
curl -H "Authorization: Bearer your-jwt-token-here" \
     https://api.studentrooms.com/v1/students
```

### JWT Token Format
```
Authorization: Bearer <your-jwt-token>
```

## Rate Limiting

The API implements rate limiting to ensure fair usage:
- **100 requests per minute** per authenticated user
- **1000 requests per hour** per authenticated user
- Rate limit information is included in response headers:
  - `X-RateLimit-Limit`: Maximum requests per window
  - `X-RateLimit-Remaining`: Remaining requests in current window
  - `X-RateLimit-Reset`: Time when the rate limit resets (Unix timestamp)
- When rate limit is exceeded, the API returns `429 Too Many Requests`

## Endpoints

The API is organized into four main categories:

### Health
Health check and monitoring endpoints:
- `GET /health` - Check API health status and system metrics

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
- `GET /rooms/{id}/students` - Get all students in a specific room

### Administration
Cross-resource and bulk operations:
- `PATCH /students/{student_id}/move` - Move student to a different room
- `PATCH /students/bulk-move` - Move multiple students to different rooms

## Data Models

### Student
```json
{
  "id": 1,
  "name": "John Doe",
  "age": 20,
  "sex": "M",
  "room_id": 101,
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
  "current_occupancy": 2,
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
  "uptime_seconds": 3600,
  "metrics": {
    "students_count": 10000,
    "rooms_count": 1000,
    "avg_occupancy": 75.5
  },
  "message": "All systems operational"
}
```

## Error Handling

The API uses a standardized error response format:

```json
{
  "code": "ERR_STUDENT_NOT_FOUND",
  "message": "Student not found",
  "details": "No student exists with ID 999"
}
```

### Common Error Codes
- `ERR_MISSING_TOKEN`: Authentication token required
- `ERR_INVALID_TOKEN`: Invalid or expired JWT token
- `ERR_STUDENT_NOT_FOUND`: Student with specified ID not found
- `ERR_ROOM_NOT_FOUND`: Room with specified ID not found
- `ERR_DUPLICATE_NAME`: Student name already exists
- `ERR_DUPLICATE_ROOM`: Room name already exists
- `ERR_ROOM_OCCUPIED`: Cannot delete room with assigned students
- `ERR_ROOM_FULL`: Room is at maximum capacity
- `ERR_VALIDATION_FAILED`: Request validation failed
- `ERR_CAPACITY_VIOLATION`: Cannot reduce capacity below current occupancy

## Usage Examples

### Health Check
```bash
curl https://api.studentrooms.com/v1/health
```

### Get All Students
```bash
curl -H "Authorization: Bearer your-jwt-token" \
     "https://api.studentrooms.com/v1/students?limit=10&offset=0"
```

### Create a New Student
```bash
curl -X POST -H "Authorization: Bearer your-jwt-token" \
     -H "Content-Type: application/json" \
     -d '{
       "name": "Jane Smith",
       "age": 19,
       "sex": "F",
       "room_id": 101
     }' \
     https://api.studentrooms.com/v1/students
```

### Create a New Room with Capacity
```bash
curl -X POST -H "Authorization: Bearer your-jwt-token" \
     -H "Content-Type: application/json" \
     -d '{
       "name": "Room #201",
       "capacity": 4
     }' \
     https://api.studentrooms.com/v1/rooms
```

### Move Student to Different Room
```bash
curl -X PATCH -H "Authorization: Bearer your-jwt-token" \
     -H "Content-Type: application/json" \
     -d '{
       "room_id": 102,
       "reason": "Student requested room change"
     }' \
     https://api.studentrooms.com/v1/students/1/move
```

### Bulk Move Students
```bash
curl -X PATCH -H "Authorization: Bearer your-jwt-token" \
     -H "Content-Type: application/json" \
     -d '{
       "moves": [
         {
           "student_id": 1,
           "room_id": 105,
           "reason": "Semester room reassignment"
         },
         {
           "student_id": 2,
           "room_id": 106,
           "reason": "Semester room reassignment"
         }
       ]
     }' \
     https://api.studentrooms.com/v1/students/bulk-move
```

### Get Students in a Specific Room
```bash
curl -H "Authorization: Bearer your-jwt-token" \
     "https://api.studentrooms.com/v1/rooms/101/students?limit=20&offset=0"
```

### Filter Students
```bash
# Filter by room
curl -H "Authorization: Bearer your-jwt-token" \
     "https://api.studentrooms.com/v1/students?room_id=101"

# Filter by gender
curl -H "Authorization: Bearer your-jwt-token" \
     "https://api.studentrooms.com/v1/students?sex=M"

# Search by name
curl -H "Authorization: Bearer your-jwt-token" \
     "https://api.studentrooms.com/v1/students?search=john"
```

## Response Format

All successful responses return the requested data directly. List responses include pagination headers:

### Single Resource Response
```json
{
  "id": 1,
  "name": "John Doe",
  "age": 20,
  "sex": "M",
  "room_id": 101,
  "created_at": "2024-01-15T10:30:00Z",
  "updated_at": "2024-01-15T10:30:00Z"
}
```

### List Response with Headers
```
X-Total-Count: 100
```

```json
[
  {
    "id": 1,
    "name": "John Doe",
    "age": 20,
    "sex": "M",
    "room_id": 101,
    "created_at": "2024-01-15T10:30:00Z",
    "updated_at": "2024-01-15T10:30:00Z"
  },
  {
    "id": 2,
    "name": "Jane Smith",
    "age": 19,
    "sex": "F",
    "room_id": 101,
    "created_at": "2024-01-15T10:30:00Z",
    "updated_at": "2024-01-15T10:30:00Z"
  }
]
```

### Bulk Operation Response
```json
{
  "successful_moves": [
    {
      "student_id": 1,
      "old_room_id": 101,
      "new_room_id": 105
    }
  ],
  "failed_moves": [
    {
      "student_id": 2,
      "error_code": "ERR_ROOM_FULL",
      "error_message": "Room 106 is at maximum capacity"
    }
  ],
  "summary": {
    "total_requested": 2,
    "successful_count": 1,
    "failed_count": 1
  }
}
```

