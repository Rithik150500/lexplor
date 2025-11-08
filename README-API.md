# LeXploR API Documentation

This document provides detailed information about the LeXploR API endpoints, including request formats, response structures, and authentication requirements.

## Table of Contents

- [Authentication](#authentication)
- [Next.js API Routes](#nextjs-api-routes)
  - [Sessions](#sessions)
  - [User Management](#user-management)
  - [Legal Analysis](#legal-analysis)
  - [Case Details](#case-details)
- [Python Backend API](#python-backend-api)
  - [Process Dispute](#process-dispute)
  - [Chat](#chat)
  - [Case Details](#case-details-1)
- [Error Handling](#error-handling)
- [Server-Sent Events (SSE)](#server-sent-events-sse)

## Authentication

All API routes (except public routes) require authentication using Clerk. The authentication is handled by the Next.js middleware, which validates the user's session and provides the `userId` to the API routes.

Authentication errors will return a `401 Unauthorized` status code.

## Next.js API Routes

### Sessions

#### Get All Sessions

Retrieves all chat sessions for the authenticated user.

- **URL**: `/api/sessions`
- **Method**: `GET`
- **Query Parameters**:
  - `userId` (optional): Filter sessions by user ID (must match authenticated user)
- **Authentication**: Required

**Response (200 OK)**:

```json
[
  {
    "id": "session_uuid",
    "userId": "user_id",
    "title": "Session Title",
    "messages": [...],
    "memo": {...},
    "relevantCases": [...],
    "caseFile": "...",
    "fileMetadata": {...},
    "createdAt": "2023-05-01T12:00:00Z",
    "updatedAt": "2023-05-01T13:00:00Z"
  },
  ...
]
```

**Error Responses**:

- `401 Unauthorized`: User is not authenticated
- `500 Internal Server Error`: Server error

#### Get Session by ID

Retrieves a specific chat session by ID.

- **URL**: `/api/sessions/:id`
- **Method**: `GET`
- **URL Parameters**:
  - `id`: Session ID
- **Authentication**: Required

**Response (200 OK)**:

```json
{
  "id": "session_uuid",
  "userId": "user_id",
  "title": "Session Title",
  "messages": [...],
  "memo": {
    "issues": "...",
    "discussion": "...",
    "conclusion": "..."
  },
  "relevantCases": [
    {
      "docId": 123,
      "caseTitle": "Smith v. Jones",
      "discussionId": 456,
      "relevance": "This case addresses similar issues..."
    },
    ...
  ],
  "caseFile": "Case file content...",
  "fileMetadata": {
    "name": "case.pdf",
    "isBinary": false
  },
  "createdAt": "2023-05-01T12:00:00Z",
  "updatedAt": "2023-05-01T13:00:00Z"
}
```

**Error Responses**:

- `401 Unauthorized`: User is not authenticated
- `403 Forbidden`: User does not own the session
- `404 Not Found`: Session not found
- `500 Internal Server Error`: Server error

#### Create or Update Session

Creates a new session or updates an existing one.

- **URL**: `/api/sessions`
- **Method**: `POST`
- **Body**:

```json
{
  "id": "session_uuid", // Optional for new sessions
  "userId": "user_id",
  "title": "Session Title",
  "messages": [...],
  "memo": {...},
  "relevantCases": [...],
  "caseFile": "...",
  "fileMetadata": {...}
}
```

- **Authentication**: Required

**Response (200 OK)**:

```json
{
  "id": "session_uuid",
  "userId": "user_id",
  "title": "Session Title",
  "messages": [...],
  "memo": {...},
  "relevantCases": [...],
  "caseFile": "...",
  "fileMetadata": {...},
  "createdAt": "2023-05-01T12:00:00Z",
  "updatedAt": "2023-05-01T13:00:00Z"
}
```

**Error Responses**:

- `401 Unauthorized`: User is not authenticated
- `403 Forbidden`: User does not own the session being updated
- `500 Internal Server Error`: Server error

#### Delete Session

Deletes a specific chat session.

- **URL**: `/api/sessions/:id`
- **Method**: `DELETE`
- **URL Parameters**:
  - `id`: Session ID
- **Authentication**: Required

**Response (200 OK)**:

```json
{
  "success": true
}
```

**Error Responses**:

- `401 Unauthorized`: User is not authenticated
- `403 Forbidden`: User does not own the session
- `404 Not Found`: Session not found
- `500 Internal Server Error`: Server error

### User Management

#### Get User Profile

Retrieves the authenticated user's profile.

- **URL**: `/api/user`
- **Method**: `GET`
- **Authentication**: Required

**Response (200 OK)**:

```json
{
  "id": "user_id",
  "email": "user@example.com",
  "phoneNumber": "+1234567890",
  "firstName": "John",
  "lastName": "Doe",
  "imageUrl": "https://example.com/image.jpg",
  "lastSignedIn": "2023-05-01T12:00:00Z",
  "createdAt": "2023-01-01T00:00:00Z",
  "updatedAt": "2023-05-01T12:00:00Z"
}
```

**Error Responses**:

- `401 Unauthorized`: User is not authenticated
- `404 Not Found`: User not found
- `500 Internal Server Error`: Server error

#### Create or Update User Profile

Creates or updates the user's profile.

- **URL**: `/api/user`
- **Method**: `POST`
- **Body**:

```json
{
  "email": "user@example.com",
  "phoneNumber": "+1234567890",
  "firstName": "John",
  "lastName": "Doe",
  "imageUrl": "https://example.com/image.jpg"
}
```

- **Authentication**: Required

**Response (200 OK)**:

```json
{
  "id": "user_id",
  "email": "user@example.com",
  "phoneNumber": "+1234567890",
  "firstName": "John",
  "lastName": "Doe",
  "imageUrl": "https://example.com/image.jpg",
  "lastSignedIn": "2023-05-01T12:00:00Z",
  "createdAt": "2023-01-01T00:00:00Z",
  "updatedAt": "2023-05-01T12:00:00Z"
}
```

**Error Responses**:

- `401 Unauthorized`: User is not authenticated
- `500 Internal Server Error`: Server error

### Legal Analysis

#### Process Legal Dispute

Processes a legal dispute and returns analysis via Server-Sent Events (SSE).

- **URL**: `/api/process-dispute`
- **Method**: `POST`
- **Body**:

```json
{
  "dispute": "Legal matter text..."
}
```

- **Authentication**: Required
- **Response Type**: Server-Sent Events (SSE)

**SSE Events**:

- `partial_memo`: Partial memo text as it's being generated
- `doc_titles`: Array of document titles being analyzed
- `relevant_cases`: Object containing relevant cases
- `partial_final_memo`: Chunks of the final memo
- `final_memo`: Complete final memo
- `conversation`: Conversation history
- `done`: Processing complete
- `error`: Error message

**Error Responses**:

- `400 Bad Request`: No dispute provided
- `401 Unauthorized`: User is not authenticated
- `500 Internal Server Error`: Server error

#### Submit Chat Message

Submits a follow-up question and returns responses via Server-Sent Events (SSE).

- **URL**: `/api/chat`
- **Method**: `POST`
- **Body**:

```json
{
  "conversation": [
    {
      "role": "user",
      "parts": ["Previous message..."]
    },
    {
      "role": "assistant",
      "parts": ["Previous response..."]
    }
  ],
  "relevant_cases": {
    "case": [
      {
        "doc_id": 123,
        "case_title": "Smith v. Jones",
        "discussion_id": 456,
        "relevance": "This case addresses similar issues..."
      }
    ]
  },
  "user_task": "Follow-up question...",
  "dispute": "Original legal matter...",
  "final_memo": "{\"issues\":\"...\",\"discussion\":\"...\",\"conclusion\":\"...\"}"
}
```

- **Authentication**: Required
- **Response Type**: Server-Sent Events (SSE)

**SSE Events**:

- `partial_chat_response`: Partial response chunks
- `relevant_cases`: Updated relevant cases
- `conversation`: Updated conversation history
- `done`: Processing complete
- `error`: Error message

**Error Responses**:

- `400 Bad Request`: No user query provided
- `401 Unauthorized`: User is not authenticated
- `500 Internal Server Error`: Server error

### Case Details

#### Get Case Details

Retrieves detailed information about a specific case.

- **URL**: `/api/case-details`
- **Method**: `POST`
- **Body**:

```json
{
  "document_id": 123,
  "analysis_reasoning_ids": [456, 789]
}
```

- **Authentication**: Required

**Response (200 OK)**:

```json
{
  "case_summary": {
    "case_title": "Smith v. Jones",
    "background_facts": "...",
    "legal_issues": [
      {
        "text": "..."
      }
    ],
    "arguments": [
      {
        "party": "appellant",
        "text": "..."
      }
    ],
    "analysis_reasoning": [
      {
        "text": "...",
        "highlight": true
      }
    ],
    "decision_order": "...",
    "dissenting_concurring": [
      {
        "text": "..."
      }
    ],
    "cases_referred": [
      {
        "case_title": "..."
      }
    ]
  },
  "case_text": "Full case text..."
}
```

**Error Responses**:

- `400 Bad Request`: No document ID provided
- `401 Unauthorized`: User is not authenticated
- `500 Internal Server Error`: Server error

## Python Backend API

These endpoints are called by the Next.js API routes and are not directly accessible to clients.

### Process Dispute

Processes a legal dispute and returns analysis via Server-Sent Events (SSE).

- **URL**: `${BACKEND_URL}/process_dispute_sse`
- **Method**: `POST`
- **Body**:

```json
{
  "dispute": "Legal matter text..."
}
```

- **Response Type**: Server-Sent Events (SSE)

**SSE Events**: Same as `/api/process-dispute`

### Chat

Processes a follow-up question and returns responses via Server-Sent Events (SSE).

- **URL**: `${BACKEND_URL}/chat`
- **Method**: `POST`
- **Body**: Same as `/api/chat`
- **Response Type**: Server-Sent Events (SSE)

**SSE Events**: Same as `/api/chat`

### Case Details

Retrieves detailed information about a specific case.

- **URL**: `${BACKEND_URL}/get_case_details`
- **Method**: `POST`
- **Body**:

```json
{
  "document_id": 123,
  "analysis_reasoning_ids": [456, 789]
}
```

**Response (200 OK)**: Same as `/api/case-details`

## Error Handling

All API endpoints follow a consistent error handling pattern:

- Client errors (4xx) include a JSON response with an `error` field describing the issue
- Server errors (5xx) include a JSON response with an `error` field and may include additional details

Example error response:

```json
{
  "error": "Error message",
  "details": "Additional error details" // Optional
}
```

## Server-Sent Events (SSE)

Several endpoints use Server-Sent Events (SSE) to stream responses back to the client. These endpoints follow this pattern:

1. Client makes a POST request to the endpoint
2. Server starts processing and sends events as they occur
3. Client receives events in real-time and updates the UI accordingly
4. Server sends a `done` event when processing is complete

Example client-side SSE handling:

```javascript
const response = await fetch('/api/process-dispute', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json',
  },
  body: JSON.stringify({ dispute: 'Legal matter...' }),
});

const reader = response.body.getReader();
const decoder = new TextDecoder();

while (true) {
  const { done, value } = await reader.read();
  if (done) break;

  const chunk = decoder.decode(value);
  const lines = chunk.split('\n');

  let eventName = null;
  let data = '';

  for (const line of lines) {
    if (line.startsWith('event:')) {
      eventName = line.replace('event:', '').trim();
    } else if (line.startsWith('data:')) {
      data = line.replace('data:', '').trim();
    } else if (line === '' && eventName && data) {
      // Handle the event based on eventName and data
      handleEvent(eventName, data);

      eventName = null;
      data = '';
    }
  }
}
```
