Prepared by claude-haiku-4-5.

# BirdBuddy API - Functional Test Scenarios

## Stage 1: Functional Requirements Analysis

| Name | Description | Reference |
|------|-------------|-----------|
| REQ-AUTH-001 | All API requests require authentication via `X-API-Key` header | Authentication and Authorization section |
| REQ-AUTH-002 | Missing or invalid `X-API-Key` header returns 401 Unauthorized | Authentication and Authorization section |
| REQ-RATE-001 | Search for a Bird endpoint has rate limit of 60 requests per 60 seconds | Rate Limiting section, Rate Limiting table |
| REQ-RATE-002 | Retrieve a Bird by ID endpoint has rate limit of 60 requests per 60 seconds | Rate Limiting section, Rate Limiting table |
| REQ-RATE-003 | Add a New Bird endpoint has rate limit of 10 requests per 60 seconds | Rate Limiting section, Rate Limiting table |
| REQ-RATE-004 | Update a Bird endpoint has rate limit of 10 requests per 60 seconds | Rate Limiting section, Rate Limiting table |
| REQ-RATE-005 | Delete a Bird endpoint has rate limit of 60 requests per 60 seconds | Rate Limiting section, Rate Limiting table |
| REQ-RATE-006 | Rate limit exceeded returns 429 Too Many Requests with `Retry-After` header | Rate Limiting section |
| REQ-SEARCH-001 | Search endpoint supports filtering by name parameter (exact match) | Search for a Bird section, Query parameters |
| REQ-SEARCH-002 | Search endpoint supports filtering by scientificName parameter (exact match) | Search for a Bird section, Query parameters |
| REQ-SEARCH-003 | Search endpoint supports filtering by size parameter (small, medium, large) | Search for a Bird section, Query parameters |
| REQ-SEARCH-004 | Search endpoint supports filtering by primaryColor parameter | Search for a Bird section, Query parameters |
| REQ-SEARCH-005 | Search endpoint supports filtering by secondaryColor parameter (array) | Search for a Bird section, Query parameters |
| REQ-SEARCH-006 | Search endpoint supports filtering by song parameter | Search for a Bird section, Query parameters |
| REQ-SEARCH-007 | Search endpoint supports filtering by flightPattern parameter | Search for a Bird section, Query parameters |
| REQ-SEARCH-008 | Search endpoint supports filtering by area parameter with OR logic | Search for a Bird section, Query parameters |
| REQ-SEARCH-009 | Search endpoint supports filtering by time parameter (array) | Search for a Bird section, Query parameters |
| REQ-SEARCH-010 | Search endpoint applies AND logic to all parameters except area | Search for a Bird section, Request specification |
| REQ-SEARCH-011 | Search endpoint supports limit parameter (1-100, default 25) | Search for a Bird section, Query parameters |
| REQ-SEARCH-012 | Search endpoint returns total count and data array on success | Search for a Bird section, Response Specification |
| REQ-SEARCH-013 | Search endpoint returns 200 OK with results for valid parameters | Search for a Bird section, Status Codes |
| REQ-SEARCH-014 | Search endpoint returns empty results (total=0) for non-matching criteria | Search for a Bird section, Examples |
| REQ-SEARCH-015 | Search endpoint returns 400 Bad Request for invalid query parameter values | Search for a Bird section, Status Codes |
| REQ-SEARCH-016 | Search endpoint returns 500 Internal Server Error on server issues | Search for a Bird section, Status Codes |
| REQ-SEARCH-017 | Search endpoint returns 503 Service Unavailable when server is unable to handle requests | Search for a Bird section, Status Codes |
| REQ-RETRIEVE-001 | Retrieve a Bird by ID endpoint accepts UUID in path parameter | Retrieve a Bird by ID section, Request Specification |
| REQ-RETRIEVE-002 | Retrieve a Bird by ID returns bird object on success | Retrieve a Bird by ID section, Response Specification |
| REQ-RETRIEVE-003 | Retrieve a Bird by ID returns 200 OK for valid UUID | Retrieve a Bird by ID section, Status Codes |
| REQ-RETRIEVE-004 | Retrieve a Bird by ID returns 400 Bad Request for invalid UUID format | Retrieve a Bird by ID section, Status Codes |
| REQ-RETRIEVE-005 | Retrieve a Bird by ID returns 404 Not Found for non-existing bird | Retrieve a Bird by ID section, Status Codes |
| REQ-RETRIEVE-006 | Retrieve a Bird by ID returns 500 Internal Server Error on server issues | Retrieve a Bird by ID section, Status Codes |
| REQ-RETRIEVE-007 | Retrieve a Bird by ID returns 503 Service Unavailable when server is unable to handle requests | Retrieve a Bird by ID section, Status Codes |
| REQ-ADD-001 | Add a New Bird endpoint requires name parameter (string, 1-100 chars) | Add a New Bird section, Request Specification |
| REQ-ADD-002 | Add a New Bird endpoint requires scientificName parameter (string, 1-200 chars) | Add a New Bird section, Request Specification |
| REQ-ADD-003 | Add a New Bird endpoint requires description parameter (string, 1-5000 chars) | Add a New Bird section, Request Specification |
| REQ-ADD-004 | Add a New Bird endpoint requires primaryColor parameter (1 value from predefined list) | Add a New Bird section, Request Specification |
| REQ-ADD-005 | Add a New Bird endpoint has optional secondaryColor parameter (array, max 10 values) | Add a New Bird section, Request Specification |
| REQ-ADD-006 | Add a New Bird endpoint has optional flightPattern parameter (linear, sine, circle, grounded) | Add a New Bird section, Request Specification |
| REQ-ADD-007 | Add a New Bird endpoint requires size parameter (small, medium, large) | Add a New Bird section, Request Specification |
| REQ-ADD-008 | Add a New Bird endpoint has optional song parameter (string, 1-200 chars) | Add a New Bird section, Request Specification |
| REQ-ADD-009 | Add a New Bird endpoint has optional area parameter (array, max 50 values) | Add a New Bird section, Request Specification |
| REQ-ADD-010 | Add a New Bird endpoint has optional time parameter (array, max 4 distinct values) | Add a New Bird section, Request Specification |
| REQ-ADD-011 | Add a New Bird endpoint has optional migratory parameter (boolean or null) | Add a New Bird section, Request Specification |
| REQ-ADD-012 | Add a New Bird generates UUID for new bird | Bird Data Model section, uuid |
| REQ-ADD-013 | Add a New Bird returns 201 Created on success with bird object | Add a New Bird section, Status Codes |
| REQ-ADD-014 | Add a New Bird returns 400 Bad Request for invalid parameters | Add a New Bird section, Status Codes |
| REQ-ADD-015 | Add a New Bird returns 409 Conflict for duplicate bird (same name and scientificName) | Add a New Bird section, Status Codes |
| REQ-ADD-016 | Add a New Bird returns 500 Internal Server Error on server issues | Add a New Bird section, Status Codes |
| REQ-ADD-017 | Add a New Bird returns 503 Service Unavailable when server is unable to handle requests | Add a New Bird section, Status Codes |
| REQ-UPDATE-001 | Update a Bird endpoint accepts UUID in path parameter | Update a Bird section, Request Specification |
| REQ-UPDATE-002 | Update a Bird endpoint accepts partial updates (one or more fields) | Update a Bird section, Request Specification |
| REQ-UPDATE-003 | Update a Bird endpoint does not allow UUID modification | Update a Bird section, Request Specification |
| REQ-UPDATE-004 | Update a Bird endpoint supports updating name parameter | Update a Bird section, Request Specification |
| REQ-UPDATE-005 | Update a Bird endpoint supports updating scientificName parameter | Update a Bird section, Request Specification |
| REQ-UPDATE-006 | Update a Bird endpoint supports updating description parameter | Update a Bird section, Request Specification |
| REQ-UPDATE-007 | Update a Bird endpoint supports updating primaryColor parameter | Update a Bird section, Request Specification |
| REQ-UPDATE-008 | Update a Bird endpoint supports updating secondaryColor parameter | Update a Bird section, Request Specification |
| REQ-UPDATE-009 | Update a Bird endpoint supports updating flightPattern parameter | Update a Bird section, Request Specification |
| REQ-UPDATE-010 | Update a Bird endpoint supports updating size parameter | Update a Bird section, Request Specification |
| REQ-UPDATE-011 | Update a Bird endpoint supports updating song parameter | Update a Bird section, Request Specification |
| REQ-UPDATE-012 | Update a Bird endpoint supports updating area parameter | Update a Bird section, Request Specification |
| REQ-UPDATE-013 | Update a Bird endpoint supports updating time parameter | Update a Bird section, Request Specification |
| REQ-UPDATE-014 | Update a Bird endpoint supports updating migratory parameter | Update a Bird section, Request Specification |
| REQ-UPDATE-015 | Update a Bird returns 200 OK with updated bird object on success | Update a Bird section, Status Codes |
| REQ-UPDATE-016 | Update a Bird returns 400 Bad Request for invalid parameters | Update a Bird section, Status Codes |
| REQ-UPDATE-017 | Update a Bird returns 404 Not Found for non-existing bird | Update a Bird section, Status Codes |
| REQ-UPDATE-018 | Update a Bird returns 409 Conflict for duplicate bird (same name and scientificName) | Update a Bird section, Status Codes |
| REQ-UPDATE-019 | Update a Bird returns 500 Internal Server Error on server issues | Update a Bird section, Status Codes |
| REQ-UPDATE-020 | Update a Bird returns 503 Service Unavailable when server is unable to handle requests | Update a Bird section, Status Codes |
| REQ-DELETE-001 | Delete a Bird endpoint accepts UUID in path parameter | Delete a Bird section, Request Specification |
| REQ-DELETE-002 | Delete a Bird returns 204 No Content on success with empty body | Delete a Bird section, Response Specification |
| REQ-DELETE-003 | Delete a Bird returns 400 Bad Request for invalid UUID format | Delete a Bird section, Status Codes |
| REQ-DELETE-004 | Delete a Bird returns 404 Not Found for non-existing bird | Delete a Bird section, Status Codes |
| REQ-DELETE-005 | Delete a Bird returns 500 Internal Server Error on server issues | Delete a Bird section, Status Codes |
| REQ-DELETE-006 | Delete a Bird returns 503 Service Unavailable when server is unable to handle requests | Delete a Bird section, Status Codes |
| REQ-DATA-001 | Bird name must be 1-100 characters | Bird Data Model section |
| REQ-DATA-002 | Bird scientificName must be 1-200 characters | Bird Data Model section |
| REQ-DATA-003 | Bird description must be 1-5000 characters | Bird Data Model section |
| REQ-DATA-004 | Bird primaryColor must be one of 15 predefined colors | Bird Data Model section |
| REQ-DATA-005 | Bird secondaryColor array must contain max 10 colors | Bird Data Model section |
| REQ-DATA-006 | Bird flightPattern must be one of: linear, sine, circle, grounded | Bird Data Model section |
| REQ-DATA-007 | Bird size must be one of: small, medium, large | Bird Data Model section |
| REQ-DATA-008 | Bird song must be 1-200 characters | Bird Data Model section |
| REQ-DATA-009 | Bird area array must contain max 50 location values | Bird Data Model section |
| REQ-DATA-010 | Bird time array must contain max 4 distinct values | Bird Data Model section |
| REQ-DATA-011 | Bird migratory can be true, false, or null | Bird Data Model section |
| REQ-DATA-012 | Bird uuid must be in UUID v4 format | Bird Data Model section |

---

## Stage 2: Test Suites and Requirements Coverage

| Name | Requirements | Description |
|------|--------------|-------------|
| TS-AUTH | REQ-AUTH-001, REQ-AUTH-002 | Tests authentication and authorization requirements. Verifies that all endpoints require API key authentication and that missing/invalid keys are properly rejected with 401 status. |
| TS-RATE-LIMIT | REQ-RATE-001, REQ-RATE-002, REQ-RATE-003, REQ-RATE-004, REQ-RATE-005, REQ-RATE-006 | Tests rate limiting functionality across all endpoints. Verifies correct rate limits per endpoint and that 429 status is returned when limits are exceeded with proper Retry-After header. |
| TS-SEARCH-BASIC | REQ-SEARCH-001, REQ-SEARCH-002, REQ-SEARCH-003, REQ-SEARCH-004, REQ-SEARCH-005, REQ-SEARCH-006, REQ-SEARCH-007, REQ-SEARCH-008, REQ-SEARCH-009, REQ-SEARCH-010, REQ-SEARCH-011, REQ-SEARCH-012 | Tests basic search functionality including all filter parameters, AND/OR logic application, limit parameter, and response format. |
| TS-SEARCH-RESULTS | REQ-SEARCH-013, REQ-SEARCH-014, REQ-SEARCH-015 | Tests search endpoint response scenarios including successful searches, empty results, and error handling for invalid parameters. |
| TS-SEARCH-ERRORS | REQ-SEARCH-016, REQ-SEARCH-017 | Tests error handling in search endpoint for server-side issues (500, 503). |
| TS-RETRIEVE | REQ-RETRIEVE-001, REQ-RETRIEVE-002, REQ-RETRIEVE-003, REQ-RETRIEVE-004, REQ-RETRIEVE-005, REQ-RETRIEVE-006, REQ-RETRIEVE-007 | Tests bird retrieval by ID including successful retrieval, invalid UUID format, non-existing birds, and error scenarios. |
| TS-ADD-REQUIRED | REQ-ADD-001, REQ-ADD-002, REQ-ADD-003, REQ-ADD-004, REQ-ADD-007, REQ-ADD-012 | Tests adding new birds with all required fields (name, scientificName, description, primaryColor, size) and UUID generation. |
| TS-ADD-OPTIONAL | REQ-ADD-005, REQ-ADD-006, REQ-ADD-008, REQ-ADD-009, REQ-ADD-010, REQ-ADD-011 | Tests adding new birds with optional fields (secondaryColor, flightPattern, song, area, time, migratory). |
| TS-ADD-RESULTS | REQ-ADD-013, REQ-ADD-014, REQ-ADD-015, REQ-ADD-016, REQ-ADD-017 | Tests add endpoint response scenarios including successful creation, validation errors, duplicate detection, and server errors. |
| TS-UPDATE-PARTIAL | REQ-UPDATE-001, REQ-UPDATE-002, REQ-UPDATE-003 | Tests partial updates with multiple field combinations while preserving UUID. |
| TS-UPDATE-FIELDS | REQ-UPDATE-004, REQ-UPDATE-005, REQ-UPDATE-006, REQ-UPDATE-007, REQ-UPDATE-008, REQ-UPDATE-009, REQ-UPDATE-010, REQ-UPDATE-011, REQ-UPDATE-012, REQ-UPDATE-013, REQ-UPDATE-014 | Tests updating individual bird fields including name, scientificName, description, colors, flight pattern, size, song, area, time, and migratory status. |
| TS-UPDATE-RESULTS | REQ-UPDATE-015, REQ-UPDATE-016, REQ-UPDATE-017, REQ-UPDATE-018, REQ-UPDATE-019, REQ-UPDATE-020 | Tests update endpoint response scenarios including successful updates, validation errors, not found, duplicates, and server errors. |
| TS-DELETE | REQ-DELETE-001, REQ-DELETE-002, REQ-DELETE-003, REQ-DELETE-004, REQ-DELETE-005, REQ-DELETE-006 | Tests bird deletion including successful deletion, invalid UUID format, non-existing birds, and error scenarios. |
| TS-DATA-VALIDATION | REQ-DATA-001, REQ-DATA-002, REQ-DATA-003, REQ-DATA-004, REQ-DATA-005, REQ-DATA-006, REQ-DATA-007, REQ-DATA-008, REQ-DATA-009, REQ-DATA-010, REQ-DATA-011, REQ-DATA-012 | Tests data model validation for all fields including string length constraints, allowed values, array size limits, and UUID format. |

---

## Stage 3: Functional Test Scenarios

### TS-AUTH: Authentication and Authorization Tests

| # | Suite | Requirements | Name | Description | Preconditions | Steps | Expected Results |
|---|-------|--------------|------|-------------|---------------|-------|------------------|
| 1 | TS-AUTH | REQ-AUTH-001 | Missing API Key Header | Verify that requests without X-API-Key header are rejected | No API key configured in request | 1. Send GET request to `/birds?size=large` without X-API-Key header | 1. Response status is 401 Unauthorized. 2. Response body contains `{"error": "401 Unauthorized", "statusCode": 401}` |
| 2 | TS-AUTH | REQ-AUTH-001 | Invalid API Key Header | Verify that requests with invalid X-API-Key header are rejected | Invalid API key value | 1. Send GET request to `/birds?size=large` with invalid X-API-Key header value | 1. Response status is 401 Unauthorized. 2. Response body contains `{"error": "401 Unauthorized", "statusCode": 401}` |
| 3 | TS-AUTH | REQ-AUTH-001 | Valid API Key Header | Verify that requests with valid X-API-Key header are accepted | Valid API key configured | 1. Send GET request to `/birds?size=large` with valid X-API-Key header. 2. Request should succeed with appropriate response | 1. Response status is 200 OK or empty results (depending on data). 2. Response body is valid JSON with `total` and `data` fields |
| 4 | TS-AUTH | REQ-AUTH-002 | Missing Auth on Search | Verify search endpoint requires authentication | No API key in request | 1. Send GET request to `/birds` endpoint without X-API-Key | 1. Response status is 401 Unauthorized |
| 5 | TS-AUTH | REQ-AUTH-002 | Missing Auth on Retrieve | Verify retrieve endpoint requires authentication | No API key in request | 1. Send GET request to `/birds/{uuid}` without X-API-Key | 1. Response status is 401 Unauthorized |
| 6 | TS-AUTH | REQ-AUTH-002 | Missing Auth on Add | Verify add endpoint requires authentication | No API key in request | 1. Send POST request to `/birds` without X-API-Key with valid body | 1. Response status is 401 Unauthorized |
| 7 | TS-AUTH | REQ-AUTH-002 | Missing Auth on Update | Verify update endpoint requires authentication | No API key in request | 1. Send PATCH request to `/birds/{uuid}` without X-API-Key | 1. Response status is 401 Unauthorized |
| 8 | TS-AUTH | REQ-AUTH-002 | Missing Auth on Delete | Verify delete endpoint requires authentication | No API key in request | 1. Send DELETE request to `/birds/{uuid}` without X-API-Key | 1. Response status is 401 Unauthorized |

### TS-RATE-LIMIT: Rate Limiting Tests

| # | Suite | Requirements | Name | Description | Preconditions | Steps | Expected Results |
|---|-------|--------------|------|-------------|---------------|-------|------------------|
| 9 | TS-RATE-LIMIT | REQ-RATE-001, REQ-RATE-006 | Search Rate Limit Exceeded | Verify 429 response when search rate limit is exceeded | Valid API key with known rate limit | 1. Send 61 GET requests to `/birds` within 60 seconds. 2. Observe response to 61st request | 1. First 60 requests return 200 or appropriate status. 2. 61st request returns 429 Too Many Requests. 3. Response includes Retry-After header with cooldown duration |
| 10 | TS-RATE-LIMIT | REQ-RATE-002, REQ-RATE-006 | Retrieve Rate Limit Exceeded | Verify 429 response when retrieve rate limit is exceeded | Valid API key, existing bird UUID | 1. Send 61 GET requests to `/birds/{uuid}` within 60 seconds | 1. First 60 requests return 200. 2. 61st request returns 429 Too Many Requests. 3. Response includes Retry-After header |
| 11 | TS-RATE-LIMIT | REQ-RATE-003, REQ-RATE-006 | Add Rate Limit Exceeded | Verify 429 response when add rate limit is exceeded | Valid API key | 1. Send 11 POST requests to `/birds` with valid bodies within 60 seconds | 1. First 10 requests return 201 Created. 2. 11th request returns 429 Too Many Requests. 3. Response includes Retry-After header |
| 12 | TS-RATE-LIMIT | REQ-RATE-004, REQ-RATE-006 | Update Rate Limit Exceeded | Verify 429 response when update rate limit is exceeded | Valid API key, existing bird UUID | 1. Send 11 PATCH requests to `/birds/{uuid}` with valid bodies within 60 seconds | 1. First 10 requests return 200 OK. 2. 11th request returns 429 Too Many Requests. 3. Response includes Retry-After header |
| 13 | TS-RATE-LIMIT | REQ-RATE-005, REQ-RATE-006 | Delete Rate Limit Exceeded | Verify 429 response when delete rate limit is exceeded | Valid API key, multiple existing bird UUIDs | 1. Send 61 DELETE requests to various `/birds/{uuid}` within 60 seconds | 1. First 60 requests return 204 No Content. 2. 61st request returns 429 Too Many Requests. 3. Response includes Retry-After header |
| 14 | TS-RATE-LIMIT | REQ-RATE-006 | Retry-After Header Accuracy | Verify Retry-After header contains correct cooldown duration | Rate limit exceeded | 1. Send requests to exceed rate limit. 2. Capture Retry-After header value | 1. Retry-After header is present in 429 response. 2. Retry-After value is a positive integer (seconds). 3. Waiting for duration specified allows subsequent requests |

### TS-SEARCH-BASIC: Search Endpoint Basic Functionality Tests

| # | Suite | Requirements | Name | Description | Preconditions | Steps | Expected Results |
|---|-------|--------------|------|-------------|---------------|-------|------------------|
| 15 | TS-SEARCH-BASIC | REQ-SEARCH-001 | Search by Name Exact Match | Verify exact match search by bird name | Valid API key, birds in database | 1. Send GET `/birds?name=Bald%20Eagle` | 1. Response status is 200 OK. 2. Response contains only birds with exact name match. 3. Total count reflects matched results |
| 16 | TS-SEARCH-BASIC | REQ-SEARCH-001 | Search by Name Partial No Match | Verify partial names do not match | Valid API key | 1. Send GET `/birds?name=Bald` (partial name) | 1. Response status is 200 OK. 2. No birds returned (total = 0) as exact match required |
| 17 | TS-SEARCH-BASIC | REQ-SEARCH-002 | Search by Scientific Name | Verify exact match search by scientific name | Valid API key, birds in database | 1. Send GET `/birds?scientificName=Haliaeetus%20leucocephalus` | 1. Response status is 200 OK. 2. Response contains matching bird with correct scientific name |
| 18 | TS-SEARCH-BASIC | REQ-SEARCH-003 | Search by Size Small | Verify search filtering by small size | Valid API key, small birds in database | 1. Send GET `/birds?size=small` | 1. Response status is 200 OK. 2. All returned birds have size = "small" |
| 19 | TS-SEARCH-BASIC | REQ-SEARCH-003 | Search by Size Medium | Verify search filtering by medium size | Valid API key, medium birds in database | 1. Send GET `/birds?size=medium` | 1. Response status is 200 OK. 2. All returned birds have size = "medium" |
| 20 | TS-SEARCH-BASIC | REQ-SEARCH-003 | Search by Size Large | Verify search filtering by large size | Valid API key, large birds in database | 1. Send GET `/birds?size=large` | 1. Response status is 200 OK. 2. All returned birds have size = "large" |
| 21 | TS-SEARCH-BASIC | REQ-SEARCH-004 | Search by Primary Color | Verify search filtering by primary color | Valid API key, birds with specific colors | 1. Send GET `/birds?primaryColor=white` | 1. Response status is 200 OK. 2. All returned birds have primaryColor = "white" |
| 22 | TS-SEARCH-BASIC | REQ-SEARCH-005 | Search by Secondary Color Single | Verify search by single secondary color | Valid API key, birds with secondary colors | 1. Send GET `/birds?secondaryColor=brown` | 1. Response status is 200 OK. 2. All returned birds have "brown" in their secondaryColor array |
| 23 | TS-SEARCH-BASIC | REQ-SEARCH-005 | Search by Secondary Color Multiple | Verify search by multiple secondary colors (AND logic) | Valid API key, birds with multiple colors | 1. Send GET `/birds?secondaryColor=brown&secondaryColor=white` | 1. Response status is 200 OK. 2. All returned birds contain both "brown" AND "white" in secondaryColor |
| 24 | TS-SEARCH-BASIC | REQ-SEARCH-006 | Search by Song | Verify search filtering by song | Valid API key, birds with specific songs | 1. Send GET `/birds?song=Rat-tat-tat` | 1. Response status is 200 OK. 2. All returned birds have song = "Rat-tat-tat" |
| 25 | TS-SEARCH-BASIC | REQ-SEARCH-007 | Search by Flight Pattern Linear | Verify search by linear flight pattern | Valid API key | 1. Send GET `/birds?flightPattern=linear` | 1. Response status is 200 OK. 2. All returned birds have flightPattern = "linear" |
| 26 | TS-SEARCH-BASIC | REQ-SEARCH-007 | Search by Flight Pattern Sine | Verify search by sine flight pattern | Valid API key | 1. Send GET `/birds?flightPattern=sine` | 1. Response status is 200 OK. 2. All returned birds have flightPattern = "sine" |
| 27 | TS-SEARCH-BASIC | REQ-SEARCH-007 | Search by Flight Pattern Circle | Verify search by circle flight pattern | Valid API key | 1. Send GET `/birds?flightPattern=circle` | 1. Response status is 200 OK. 2. All returned birds have flightPattern = "circle" |
| 28 | TS-SEARCH-BASIC | REQ-SEARCH-007 | Search by Flight Pattern Grounded | Verify search by grounded flight pattern | Valid API key | 1. Send GET `/birds?flightPattern=grounded` | 1. Response status is 200 OK. 2. All returned birds have flightPattern = "grounded" |
| 29 | TS-SEARCH-BASIC | REQ-SEARCH-008 | Search by Area Single Location | Verify area search with single location | Valid API key, birds in various areas | 1. Send GET `/birds?area=US%20Texas` | 1. Response status is 200 OK. 2. All returned birds have "US Texas" in their area array |
| 30 | TS-SEARCH-BASIC | REQ-SEARCH-008 | Search by Area Multiple Locations OR Logic | Verify area uses OR logic with multiple locations | Valid API key, birds in multiple areas | 1. Send GET `/birds?area=Mexico&area=New%20York` | 1. Response status is 200 OK. 2. Returned birds contain "Mexico" OR "New York" in their area array |
| 31 | TS-SEARCH-BASIC | REQ-SEARCH-009 | Search by Time Single | Verify search by single observation time | Valid API key, birds observed at various times | 1. Send GET `/birds?time=morning` | 1. Response status is 200 OK. 2. All returned birds have "morning" in their time array |
| 32 | TS-SEARCH-BASIC | REQ-SEARCH-009 | Search by Time Multiple | Verify search by multiple observation times (AND logic) | Valid API key | 1. Send GET `/birds?time=morning&time=afternoon` | 1. Response status is 200 OK. 2. All returned birds have BOTH "morning" AND "afternoon" in their time array |
| 33 | TS-SEARCH-BASIC | REQ-SEARCH-010 | Combined Filters AND Logic | Verify AND logic applied to all filters except area | Valid API key, birds matching criteria | 1. Send GET `/birds?size=large&primaryColor=white&song=Rat-tat-tat` | 1. Response status is 200 OK. 2. All returned birds are large AND white AND have song "Rat-tat-tat". 3. Results match specification example |
| 34 | TS-SEARCH-BASIC | REQ-SEARCH-010 | Complex Filter Combination | Verify complex combination with AND/OR logic | Valid API key | 1. Send GET `/birds?size=large&primaryColor=white&area=Texas&area=Mexico` | 1. Response status is 200 OK. 2. All results are large AND white AND (Texas OR Mexico) |
| 35 | TS-SEARCH-BASIC | REQ-SEARCH-011 | Limit Default | Verify default limit of 25 results | Valid API key, many birds in database | 1. Send GET `/birds` without limit parameter | 1. Response status is 200 OK. 2. Returned array has maximum 25 items (or fewer if database has less) |
| 36 | TS-SEARCH-BASIC | REQ-SEARCH-011 | Limit Custom Value | Verify custom limit parameter | Valid API key, many birds in database | 1. Send GET `/birds?limit=50` | 1. Response status is 200 OK. 2. Returned array has maximum 50 items |
| 37 | TS-SEARCH-BASIC | REQ-SEARCH-011 | Limit Minimum | Verify limit=1 | Valid API key | 1. Send GET `/birds?limit=1` | 1. Response status is 200 OK. 2. Returned array has maximum 1 item |
| 38 | TS-SEARCH-BASIC | REQ-SEARCH-011 | Limit Maximum | Verify limit=100 | Valid API key | 1. Send GET `/birds?limit=100` | 1. Response status is 200 OK. 2. Returned array has maximum 100 items |
| 39 | TS-SEARCH-BASIC | REQ-SEARCH-012 | Response Format with Results | Verify response structure with data | Valid API key, birds in database | 1. Send GET `/birds?size=large` | 1. Response status is 200 OK. 2. Response body is valid JSON with `total` (number) and `data` (array). 3. Data array contains bird objects |
| 40 | TS-SEARCH-BASIC | REQ-SEARCH-012 | Response Format Empty Results | Verify response structure with no results | Valid API key | 1. Send GET `/birds?name=NONEXISTENT` | 1. Response status is 200 OK. 2. Response body has `total = 0` and `data = []` |

### TS-SEARCH-RESULTS: Search Endpoint Response Scenarios

| # | Suite | Requirements | Name | Description | Preconditions | Steps | Expected Results |
|---|-------|--------------|------|-------------|---------------|-------|------------------|
| 41 | TS-SEARCH-RESULTS | REQ-SEARCH-013 | Successful Search with Results | Verify successful search returns matching birds | Valid API key, birds in database matching criteria | 1. Send GET `/birds?size=large&primaryColor=white` | 1. Response status is 200 OK. 2. Response body contains valid bird objects. 3. Total count matches number of results |
| 42 | TS-SEARCH-RESULTS | REQ-SEARCH-014 | Empty Results Search | Verify empty results returned for non-matching criteria | Valid API key | 1. Send GET `/birds?name=BIRD_DOES_NOT_EXIST` | 1. Response status is 200 OK. 2. Response body has total=0 and data=[] |
| 43 | TS-SEARCH-RESULTS | REQ-SEARCH-015 | Invalid Time Parameter | Verify 400 error for invalid time value | Valid API key | 1. Send GET `/birds?time=supper` (invalid time) | 1. Response status is 400 Bad Request. 2. Response body contains error message mentioning invalid 'time' parameter |
| 44 | TS-SEARCH-RESULTS | REQ-SEARCH-015 | Invalid Size Parameter | Verify 400 error for invalid size value | Valid API key | 1. Send GET `/birds?size=extra_large` | 1. Response status is 400 Bad Request. 2. Error message indicates invalid size parameter |
| 45 | TS-SEARCH-RESULTS | REQ-SEARCH-015 | Invalid Color Parameter | Verify 400 error for invalid primary color | Valid API key | 1. Send GET `/birds?primaryColor=chartreuse` (not in allowed list) | 1. Response status is 400 Bad Request. 2. Error message indicates invalid color parameter |
| 46 | TS-SEARCH-RESULTS | REQ-SEARCH-015 | Invalid Flight Pattern | Verify 400 error for invalid flight pattern | Valid API key | 1. Send GET `/birds?flightPattern=zigzag` | 1. Response status is 400 Bad Request. 2. Error message indicates invalid flight pattern |
| 47 | TS-SEARCH-RESULTS | REQ-SEARCH-015 | Invalid Limit Value | Verify 400 error for limit outside range | Valid API key | 1. Send GET `/birds?limit=101` (exceeds maximum) | 1. Response status is 400 Bad Request. 2. Error message indicates limit must be 1-100 |
| 48 | TS-SEARCH-RESULTS | REQ-SEARCH-015 | Invalid Limit Negative | Verify 400 error for negative limit | Valid API key | 1. Send GET `/birds?limit=-5` | 1. Response status is 400 Bad Request |
| 49 | TS-SEARCH-RESULTS | REQ-SEARCH-015 | Invalid Limit Zero | Verify 400 error for zero limit | Valid API key | 1. Send GET `/birds?limit=0` | 1. Response status is 400 Bad Request |

### TS-SEARCH-ERRORS: Search Endpoint Server Error Scenarios

| # | Suite | Requirements | Name | Description | Preconditions | Steps | Expected Results |
|---|-------|--------------|------|-------------|---------------|-------|------------------|
| 50 | TS-SEARCH-ERRORS | REQ-SEARCH-016 | Server Internal Error | Verify 500 error handling | Valid API key, server experiencing issues | 1. Send GET `/birds` when server returns internal error | 1. Response status is 500 Internal Server Error. 2. Response body contains `{"error": "500 Internal Server Error", "statusCode": 500}` |
| 51 | TS-SEARCH-ERRORS | REQ-SEARCH-017 | Service Unavailable | Verify 503 error handling | Valid API key, service temporarily unavailable | 1. Send GET `/birds` when service is unavailable | 1. Response status is 503 Service Unavailable. 2. Response body contains `{"error": "503 Service Unavailable", "statusCode": 503}` |

### TS-RETRIEVE: Retrieve Bird by ID Tests

| # | Suite | Requirements | Name | Description | Preconditions | Steps | Expected Results |
|---|-------|--------------|------|-------------|---------------|-------|------------------|
| 52 | TS-RETRIEVE | REQ-RETRIEVE-001, REQ-RETRIEVE-003 | Retrieve Existing Bird | Verify successful bird retrieval by valid UUID | Valid API key, existing bird UUID (e.g., from search) | 1. Send GET `/birds/f47ac10b-58cc-4372-a567-0e02b2c3d479` | 1. Response status is 200 OK. 2. Response body is a complete bird object with all fields. 3. UUID matches the requested ID |
| 53 | TS-RETRIEVE | REQ-RETRIEVE-002 | Retrieve Response Format | Verify response format is single bird object | Valid API key, existing bird UUID | 1. Send GET `/birds/{uuid}` | 1. Response body is a JSON bird object (not wrapped in array or with total/data). 2. Contains all bird fields: uuid, name, scientificName, description, colors, flight pattern, size, song, area, time, migratory |
| 54 | TS-RETRIEVE | REQ-RETRIEVE-004 | Invalid UUID Format | Verify 400 error for malformed UUID | Valid API key | 1. Send GET `/birds/not-a-valid-uuid` | 1. Response status is 400 Bad Request. 2. Response body contains error message |
| 55 | TS-RETRIEVE | REQ-RETRIEVE-004 | UUID Wrong Length | Verify 400 error for UUID wrong format | Valid API key | 1. Send GET `/birds/f47ac10b-58cc-4372` (incomplete UUID) | 1. Response status is 400 Bad Request. 2. Error message indicates invalid UUID format |
| 56 | TS-RETRIEVE | REQ-RETRIEVE-004 | UUID Invalid Characters | Verify 400 error for UUID with invalid characters | Valid API key | 1. Send GET `/birds/g47ac10b-58cc-4372-a567-0e02b2c3d479` (invalid char 'g') | 1. Response status is 400 Bad Request |
| 57 | TS-RETRIEVE | REQ-RETRIEVE-005 | Non-Existing Bird | Verify 404 error for non-existing bird UUID | Valid API key, valid UUID format but bird doesn't exist | 1. Send GET `/birds/00000000-0000-0000-0000-000000000000` | 1. Response status is 404 Not Found. 2. Response body contains `{"error": "404 Not Found", "statusCode": 404}` |
| 58 | TS-RETRIEVE | REQ-RETRIEVE-006 | Server Internal Error | Verify 500 error handling | Valid API key, UUID format valid, server error | 1. Send GET `/birds/{valid_uuid}` when server returns error | 1. Response status is 500 Internal Server Error. 2. Response body contains error message |
| 59 | TS-RETRIEVE | REQ-RETRIEVE-007 | Service Unavailable | Verify 503 error handling | Valid API key, service temporarily down | 1. Send GET `/birds/{uuid}` when service unavailable | 1. Response status is 503 Service Unavailable. 2. Response body contains error message |

### TS-ADD-REQUIRED: Add New Bird - Required Fields Tests

| # | Suite | Requirements | Name | Description | Preconditions | Steps | Expected Results |
|---|-------|--------------|------|-------------|---------------|-------|------------------|
| 60 | TS-ADD-REQUIRED | REQ-ADD-001 | Add Bird with Valid Name | Verify name parameter is accepted | Valid API key | 1. Send POST `/birds` with name="Bald Eagle" and other required fields | 1. Response status is 201 Created. 2. Response contains bird object with name="Bald Eagle" |
| 61 | TS-ADD-REQUIRED | REQ-ADD-001 | Add Bird Missing Name | Verify name is required | Valid API key | 1. Send POST `/birds` without name field | 1. Response status is 400 Bad Request. 2. Error message indicates name is required |
| 62 | TS-ADD-REQUIRED | REQ-ADD-001 | Name Too Short | Verify name minimum length | Valid API key | 1. Send POST `/birds` with name="" (empty) | 1. Response status is 400 Bad Request. 2. Error indicates name must be 1-100 characters |
| 63 | TS-ADD-REQUIRED | REQ-ADD-001 | Name Too Long | Verify name maximum length | Valid API key | 1. Send POST `/birds` with name of 101 characters | 1. Response status is 400 Bad Request. 2. Error indicates name must be max 100 characters |
| 64 | TS-ADD-REQUIRED | REQ-ADD-002 | Add Bird with Valid Scientific Name | Verify scientificName parameter | Valid API key | 1. Send POST `/birds` with scientificName="Haliaeetus leucocephalus" | 1. Response status is 201 Created. 2. Response contains correct scientificName |
| 65 | TS-ADD-REQUIRED | REQ-ADD-002 | Scientific Name Missing | Verify scientificName is required | Valid API key | 1. Send POST `/birds` without scientificName field | 1. Response status is 400 Bad Request. 2. Error indicates scientificName is required |
| 66 | TS-ADD-REQUIRED | REQ-ADD-002 | Scientific Name Too Long | Verify scientificName maximum length | Valid API key | 1. Send POST `/birds` with scientificName of 201 characters | 1. Response status is 400 Bad Request. 2. Error indicates max 200 characters |
| 67 | TS-ADD-REQUIRED | REQ-ADD-003 | Add Bird with Valid Description | Verify description parameter | Valid API key | 1. Send POST `/birds` with valid description | 1. Response status is 201 Created. 2. Response contains description |
| 68 | TS-ADD-REQUIRED | REQ-ADD-003 | Description Missing | Verify description is required | Valid API key | 1. Send POST `/birds` without description | 1. Response status is 400 Bad Request. 2. Error indicates description is required |
| 69 | TS-ADD-REQUIRED | REQ-ADD-003 | Description Too Long | Verify description maximum length | Valid API key | 1. Send POST `/birds` with description of 5001 characters | 1. Response status is 400 Bad Request. 2. Error indicates max 5000 characters |
| 70 | TS-ADD-REQUIRED | REQ-ADD-004 | Add Bird with Primary Color Red | Verify primaryColor with valid value | Valid API key | 1. Send POST `/birds` with primaryColor="red" | 1. Response status is 201 Created. 2. Response contains primaryColor="red" |
| 71 | TS-ADD-REQUIRED | REQ-ADD-004 | Add Bird with Primary Color White | Verify primaryColor accepts white | Valid API key | 1. Send POST `/birds` with primaryColor="white" | 1. Response status is 201 Created. 2. Response contains primaryColor="white" |
| 72 | TS-ADD-REQUIRED | REQ-ADD-004 | Primary Color Missing | Verify primaryColor is required | Valid API key | 1. Send POST `/birds` without primaryColor | 1. Response status is 400 Bad Request. 2. Error indicates primaryColor is required |
| 73 | TS-ADD-REQUIRED | REQ-ADD-004 | Invalid Primary Color | Verify primaryColor validation | Valid API key | 1. Send POST `/birds` with primaryColor="chartreuse" | 1. Response status is 400 Bad Request. 2. Error indicates invalid color |
| 74 | TS-ADD-REQUIRED | REQ-ADD-007 | Add Bird with Size Small | Verify size parameter with small | Valid API key | 1. Send POST `/birds` with size="small" | 1. Response status is 201 Created. 2. Response contains size="small" |
| 75 | TS-ADD-REQUIRED | REQ-ADD-007 | Add Bird with Size Medium | Verify size parameter with medium | Valid API key | 1. Send POST `/birds` with size="medium" | 1. Response status is 201 Created. 2. Response contains size="medium" |
| 76 | TS-ADD-REQUIRED | REQ-ADD-007 | Add Bird with Size Large | Verify size parameter with large | Valid API key | 1. Send POST `/birds` with size="large" | 1. Response status is 201 Created. 2. Response contains size="large" |
| 77 | TS-ADD-REQUIRED | REQ-ADD-007 | Size Missing | Verify size is required | Valid API key | 1. Send POST `/birds` without size | 1. Response status is 400 Bad Request. 2. Error indicates size is required |
| 78 | TS-ADD-REQUIRED | REQ-ADD-007 | Invalid Size | Verify size validation | Valid API key | 1. Send POST `/birds` with size="extra_large" | 1. Response status is 400 Bad Request. 2. Error indicates invalid size |
| 79 | TS-ADD-REQUIRED | REQ-ADD-012 | UUID Generated | Verify UUID is generated for new bird | Valid API key, new bird added | 1. Send POST `/birds` with valid data | 1. Response status is 201 Created. 2. Response contains uuid field with UUID v4 format |
| 80 | TS-ADD-REQUIRED | REQ-ADD-012 | UUID Unique | Verify each new bird gets unique UUID | Valid API key, adding multiple birds | 1. Send 2 POST requests with different bird data. 2. Compare UUIDs in responses | 1. Both requests return 201. 2. UUIDs are different |

### TS-ADD-OPTIONAL: Add New Bird - Optional Fields Tests

| # | Suite | Requirements | Name | Description | Preconditions | Steps | Expected Results |
|---|-------|--------------|------|-------------|---------------|-------|------------------|
| 81 | TS-ADD-OPTIONAL | REQ-ADD-005 | Add Bird with Secondary Color | Verify secondaryColor can be provided | Valid API key | 1. Send POST `/birds` with secondaryColor=["white", "orange"] | 1. Response status is 201 Created. 2. Response contains secondaryColor array with both colors |
| 82 | TS-ADD-OPTIONAL | REQ-ADD-005 | Add Bird Without Secondary Color | Verify secondaryColor is optional | Valid API key | 1. Send POST `/birds` without secondaryColor field | 1. Response status is 201 Created. 2. Response contains secondaryColor as empty array [] |
| 83 | TS-ADD-OPTIONAL | REQ-ADD-005 | Secondary Color Array Empty | Verify empty secondary color array accepted | Valid API key | 1. Send POST `/birds` with secondaryColor=[] | 1. Response status is 201 Created. 2. Response contains empty secondaryColor array |
| 84 | TS-ADD-OPTIONAL | REQ-ADD-005 | Secondary Color Max Limit | Verify max 10 colors in array | Valid API key | 1. Send POST `/birds` with 10 secondary colors | 1. Response status is 201 Created. 2. secondaryColor contains all 10 colors |
| 85 | TS-ADD-OPTIONAL | REQ-ADD-005 | Secondary Color Exceeds Limit | Verify exceeding 10 colors is rejected | Valid API key | 1. Send POST `/birds` with 11 secondary colors | 1. Response status is 400 Bad Request. 2. Error indicates max 10 secondary colors |
| 86 | TS-ADD-OPTIONAL | REQ-ADD-006 | Add Bird with Flight Pattern Linear | Verify flightPattern can be linear | Valid API key | 1. Send POST `/birds` with flightPattern="linear" | 1. Response status is 201 Created. 2. Response contains flightPattern="linear" |
| 87 | TS-ADD-OPTIONAL | REQ-ADD-006 | Add Bird with Flight Pattern Sine | Verify flightPattern can be sine | Valid API key | 1. Send POST `/birds` with flightPattern="sine" | 1. Response status is 201 Created |
| 88 | TS-ADD-OPTIONAL | REQ-ADD-006 | Add Bird with Flight Pattern Circle | Verify flightPattern can be circle | Valid API key | 1. Send POST `/birds` with flightPattern="circle" | 1. Response status is 201 Created |
| 89 | TS-ADD-OPTIONAL | REQ-ADD-006 | Add Bird with Flight Pattern Grounded | Verify flightPattern can be grounded | Valid API key | 1. Send POST `/birds` with flightPattern="grounded" | 1. Response status is 201 Created |
| 90 | TS-ADD-OPTIONAL | REQ-ADD-006 | Add Bird Without Flight Pattern | Verify flightPattern is optional | Valid API key | 1. Send POST `/birds` without flightPattern | 1. Response status is 201 Created |
| 91 | TS-ADD-OPTIONAL | REQ-ADD-006 | Invalid Flight Pattern | Verify flightPattern validation | Valid API key | 1. Send POST `/birds` with flightPattern="zigzag" | 1. Response status is 400 Bad Request |
| 92 | TS-ADD-OPTIONAL | REQ-ADD-008 | Add Bird with Song | Verify song field can be provided | Valid API key | 1. Send POST `/birds` with song="Rat-tat-tat" | 1. Response status is 201 Created. 2. Response contains song="Rat-tat-tat" |
| 93 | TS-ADD-OPTIONAL | REQ-ADD-008 | Add Bird Without Song | Verify song is optional | Valid API key | 1. Send POST `/birds` without song field | 1. Response status is 201 Created |
| 94 | TS-ADD-OPTIONAL | REQ-ADD-008 | Song Too Long | Verify song maximum length | Valid API key | 1. Send POST `/birds` with song of 201 characters | 1. Response status is 400 Bad Request. 2. Error indicates max 200 characters |
| 95 | TS-ADD-OPTIONAL | REQ-ADD-009 | Add Bird with Area | Verify area field can be provided | Valid API key | 1. Send POST `/birds` with area=["US Texas", "Mexico"] | 1. Response status is 201 Created. 2. Response contains area array |
| 96 | TS-ADD-OPTIONAL | REQ-ADD-009 | Add Bird Without Area | Verify area is optional | Valid API key | 1. Send POST `/birds` without area field | 1. Response status is 201 Created |
| 97 | TS-ADD-OPTIONAL | REQ-ADD-009 | Area Max Limit | Verify max 50 locations in array | Valid API key | 1. Send POST `/birds` with 50 area locations | 1. Response status is 201 Created. 2. area contains all 50 locations |
| 98 | TS-ADD-OPTIONAL | REQ-ADD-009 | Area Exceeds Limit | Verify exceeding 50 areas is rejected | Valid API key | 1. Send POST `/birds` with 51 area locations | 1. Response status is 400 Bad Request. 2. Error indicates max 50 areas |
| 99 | TS-ADD-OPTIONAL | REQ-ADD-010 | Add Bird with Time Morning | Verify time field accepts morning | Valid API key | 1. Send POST `/birds` with time=["morning"] | 1. Response status is 201 Created. 2. Response contains time=["morning"] |
| 100 | TS-ADD-OPTIONAL | REQ-ADD-010 | Add Bird with Multiple Times | Verify time field accepts multiple values | Valid API key | 1. Send POST `/birds` with time=["morning", "afternoon", "evening", "night"] | 1. Response status is 201 Created. 2. Response contains all 4 time values |
| 101 | TS-ADD-OPTIONAL | REQ-ADD-010 | Add Bird Without Time | Verify time is optional | Valid API key | 1. Send POST `/birds` without time field | 1. Response status is 201 Created |
| 102 | TS-ADD-OPTIONAL | REQ-ADD-010 | Time Exceeds Limit | Verify max 4 distinct time values | Valid API key | 1. Send POST `/birds` with time containing more than 4 values or invalid value | 1. Response status is 400 Bad Request |
| 103 | TS-ADD-OPTIONAL | REQ-ADD-011 | Add Bird Migratory True | Verify migratory can be true | Valid API key | 1. Send POST `/birds` with migratory=true | 1. Response status is 201 Created. 2. Response contains migratory=true |
| 104 | TS-ADD-OPTIONAL | REQ-ADD-011 | Add Bird Migratory False | Verify migratory can be false | Valid API key | 1. Send POST `/birds` with migratory=false | 1. Response status is 201 Created. 2. Response contains migratory=false |
| 105 | TS-ADD-OPTIONAL | REQ-ADD-011 | Add Bird Migratory Null | Verify migratory can be null | Valid API key | 1. Send POST `/birds` with migratory=null | 1. Response status is 201 Created. 2. Response contains migratory=null |
| 106 | TS-ADD-OPTIONAL | REQ-ADD-011 | Add Bird Without Migratory | Verify migratory is optional | Valid API key | 1. Send POST `/birds` without migratory field | 1. Response status is 201 Created |

### TS-ADD-RESULTS: Add New Bird Response Scenarios

| # | Suite | Requirements | Name | Description | Preconditions | Steps | Expected Results |
|---|-------|--------------|------|-------------|---------------|-------|------------------|
| 107 | TS-ADD-RESULTS | REQ-ADD-013 | Successful Bird Addition | Verify 201 Created response with bird object | Valid API key, unique bird data | 1. Send POST `/birds` with valid, complete bird data | 1. Response status is 201 Created. 2. Response body is the complete bird object with generated uuid |
| 108 | TS-ADD-RESULTS | REQ-ADD-014 | Missing Required Field Name | Verify 400 error when name is missing | Valid API key | 1. Send POST `/birds` without name field but with all other required fields | 1. Response status is 400 Bad Request. 2. Error message mentions missing or invalid name |
| 109 | TS-ADD-RESULTS | REQ-ADD-014 | Invalid Field Type | Verify 400 error for wrong field type | Valid API key | 1. Send POST `/birds` with size as array instead of string | 1. Response status is 400 Bad Request. 2. Error indicates type mismatch |
| 110 | TS-ADD-RESULTS | REQ-ADD-014 | Invalid JSON | Verify 400 error for malformed JSON | Valid API key | 1. Send POST `/birds` with malformed JSON body | 1. Response status is 400 Bad Request |
| 111 | TS-ADD-RESULTS | REQ-ADD-015 | Duplicate Bird Same Name and Scientific Name | Verify 409 Conflict for duplicate bird | Valid API key, bird already exists | 1. Add a bird with name="Bald Eagle", scientificName="Haliaeetus leucocephalus". 2. Attempt to add another bird with same name and scientific name | 1. First request returns 201 Created. 2. Second request returns 409 Conflict with message about duplicate |
| 112 | TS-ADD-RESULTS | REQ-ADD-015 | Same Name Different Scientific Name | Verify duplicate check uses both fields | Valid API key, bird exists | 1. Add bird A with name="Eagle", scientificName="Name1". 2. Add bird B with same name but scientificName="Name2" | 1. Both requests return 201 Created as they are not duplicates |
| 113 | TS-ADD-RESULTS | REQ-ADD-015 | Same Scientific Name Different Name | Verify duplicate check uses both fields | Valid API key, bird exists | 1. Add bird A with name="Name1", scientificName="Eagle". 2. Add bird B with different name but same scientificName | 1. Both requests return 201 Created as they are not duplicates |
| 114 | TS-ADD-RESULTS | REQ-ADD-016 | Server Internal Error | Verify 500 error handling | Valid API key, server error | 1. Send POST `/birds` when server experiences internal error | 1. Response status is 500 Internal Server Error. 2. Response body contains error message |
| 115 | TS-ADD-RESULTS | REQ-ADD-017 | Service Unavailable | Verify 503 error handling | Valid API key, service down | 1. Send POST `/birds` when service is unavailable | 1. Response status is 503 Service Unavailable. 2. Response body contains error message |

### TS-UPDATE-PARTIAL: Update Bird - Partial Updates Tests

| # | Suite | Requirements | Name | Description | Preconditions | Steps | Expected Results |
|---|-------|--------------|------|-------------|---------------|-------|------------------|
| 116 | TS-UPDATE-PARTIAL | REQ-UPDATE-001, REQ-UPDATE-002 | Update Single Field | Verify partial update with one field | Valid API key, existing bird UUID | 1. Send PATCH `/birds/{uuid}` with only flightPattern="sine" in body | 1. Response status is 200 OK. 2. flightPattern is updated. 3. All other fields remain unchanged |
| 117 | TS-UPDATE-PARTIAL | REQ-UPDATE-001, REQ-UPDATE-002 | Update Multiple Fields | Verify partial update with multiple fields | Valid API key, existing bird UUID | 1. Send PATCH `/birds/{uuid}` with flightPattern="sine", song="New Song" | 1. Response status is 200 OK. 2. Both fields are updated. 3. Other fields unchanged |
| 118 | TS-UPDATE-PARTIAL | REQ-UPDATE-001, REQ-UPDATE-002 | Update All Fields | Verify partial update with all optional fields | Valid API key, existing bird UUID | 1. Send PATCH `/birds/{uuid}` with all possible fields | 1. Response status is 200 OK. 2. All fields are updated as specified |
| 119 | TS-UPDATE-PARTIAL | REQ-UPDATE-003 | UUID Cannot Be Updated | Verify UUID is immutable | Valid API key, existing bird UUID | 1. Send PATCH `/birds/{uuid}` with uuid="different-uuid" in body | 1. Response status is 200 OK (or 400). 2. Returned bird UUID remains the original value |
| 120 | TS-UPDATE-PARTIAL | REQ-UPDATE-002 | Empty Update Body | Verify empty PATCH body is handled | Valid API key, existing bird UUID | 1. Send PATCH `/birds/{uuid}` with empty JSON object {} | 1. Response status is 200 OK. 2. Bird object returned with no changes |

### TS-UPDATE-FIELDS: Update Bird - Individual Field Tests

| # | Suite | Requirements | Name | Description | Preconditions | Steps | Expected Results |
|---|-------|--------------|------|-------------|---------------|-------|------------------|
| 121 | TS-UPDATE-FIELDS | REQ-UPDATE-004 | Update Bird Name | Verify name field can be updated | Valid API key, existing bird UUID | 1. Send PATCH `/birds/{uuid}` with name="New Name" | 1. Response status is 200 OK. 2. Returned bird has new name |
| 122 | TS-UPDATE-FIELDS | REQ-UPDATE-004 | Update Name Validation | Verify name constraints apply on update | Valid API key, existing bird UUID | 1. Send PATCH `/birds/{uuid}` with name of 101 characters | 1. Response status is 400 Bad Request. 2. Error indicates max 100 characters |
| 123 | TS-UPDATE-FIELDS | REQ-UPDATE-005 | Update Scientific Name | Verify scientificName field can be updated | Valid API key, existing bird UUID | 1. Send PATCH `/birds/{uuid}` with scientificName="New Scientific Name" | 1. Response status is 200 OK. 2. Returned bird has new scientificName |
| 124 | TS-UPDATE-FIELDS | REQ-UPDATE-005 | Update Scientific Name Validation | Verify scientificName constraints on update | Valid API key, existing bird UUID | 1. Send PATCH `/birds/{uuid}` with scientificName of 201 characters | 1. Response status is 400 Bad Request |
| 125 | TS-UPDATE-FIELDS | REQ-UPDATE-006 | Update Description | Verify description field can be updated | Valid API key, existing bird UUID | 1. Send PATCH `/birds/{uuid}` with description="Updated description" | 1. Response status is 200 OK. 2. Returned bird has new description |
| 126 | TS-UPDATE-FIELDS | REQ-UPDATE-006 | Update Description Validation | Verify description constraints on update | Valid API key, existing bird UUID | 1. Send PATCH `/birds/{uuid}` with description of 5001 characters | 1. Response status is 400 Bad Request |
| 127 | TS-UPDATE-FIELDS | REQ-UPDATE-007 | Update Primary Color | Verify primaryColor field can be updated | Valid API key, existing bird UUID | 1. Send PATCH `/birds/{uuid}` with primaryColor="green" | 1. Response status is 200 OK. 2. Returned bird has new primaryColor |
| 128 | TS-UPDATE-FIELDS | REQ-UPDATE-007 | Update Primary Color Validation | Verify primaryColor must be valid | Valid API key, existing bird UUID | 1. Send PATCH `/birds/{uuid}` with primaryColor="invalid" | 1. Response status is 400 Bad Request |
| 129 | TS-UPDATE-FIELDS | REQ-UPDATE-008 | Update Secondary Color | Verify secondaryColor field can be updated | Valid API key, existing bird UUID | 1. Send PATCH `/birds/{uuid}` with secondaryColor=["new", "colors"] | 1. Response status is 200 OK. 2. Returned bird has new secondaryColor |
| 130 | TS-UPDATE-FIELDS | REQ-UPDATE-008 | Update Secondary Color to Empty | Verify secondaryColor can be cleared | Valid API key, existing bird UUID | 1. Send PATCH `/birds/{uuid}` with secondaryColor=[] | 1. Response status is 200 OK. 2. secondaryColor becomes empty array |
| 131 | TS-UPDATE-FIELDS | REQ-UPDATE-009 | Update Flight Pattern | Verify flightPattern field can be updated | Valid API key, existing bird UUID | 1. Send PATCH `/birds/{uuid}` with flightPattern="circle" | 1. Response status is 200 OK. 2. Returned bird has new flightPattern |
| 132 | TS-UPDATE-FIELDS | REQ-UPDATE-009 | Update Flight Pattern Validation | Verify flightPattern validation on update | Valid API key, existing bird UUID | 1. Send PATCH `/birds/{uuid}` with flightPattern="invalid" | 1. Response status is 400 Bad Request |
| 133 | TS-UPDATE-FIELDS | REQ-UPDATE-010 | Update Size | Verify size field can be updated | Valid API key, existing bird UUID | 1. Send PATCH `/birds/{uuid}` with size="small" | 1. Response status is 200 OK. 2. Returned bird has new size |
| 134 | TS-UPDATE-FIELDS | REQ-UPDATE-010 | Update Size Validation | Verify size validation on update | Valid API key, existing bird UUID | 1. Send PATCH `/birds/{uuid}` with size="giant" | 1. Response status is 400 Bad Request |
| 135 | TS-UPDATE-FIELDS | REQ-UPDATE-011 | Update Song | Verify song field can be updated | Valid API key, existing bird UUID | 1. Send PATCH `/birds/{uuid}` with song="New Song" | 1. Response status is 200 OK. 2. Returned bird has new song |
| 136 | TS-UPDATE-FIELDS | REQ-UPDATE-011 | Clear Song | Verify song can be cleared | Valid API key, existing bird UUID | 1. Send PATCH `/birds/{uuid}` with song="" or song=null | 1. Response handles appropriately (201 or 400) |
| 137 | TS-UPDATE-FIELDS | REQ-UPDATE-012 | Update Area | Verify area field can be updated | Valid API key, existing bird UUID | 1. Send PATCH `/birds/{uuid}` with area=["New Location"] | 1. Response status is 200 OK. 2. Returned bird has new area |
| 138 | TS-UPDATE-FIELDS | REQ-UPDATE-012 | Update Area to Multiple Locations | Verify area can be updated with multiple values | Valid API key, existing bird UUID | 1. Send PATCH `/birds/{uuid}` with area=["Loc1", "Loc2", "Loc3"] | 1. Response status is 200 OK. 2. area array contains all 3 locations |
| 139 | TS-UPDATE-FIELDS | REQ-UPDATE-013 | Update Time | Verify time field can be updated | Valid API key, existing bird UUID | 1. Send PATCH `/birds/{uuid}` with time=["evening"] | 1. Response status is 200 OK. 2. Returned bird has new time |
| 140 | TS-UPDATE-FIELDS | REQ-UPDATE-013 | Update Time Multiple Values | Verify time can be updated with multiple values | Valid API key, existing bird UUID | 1. Send PATCH `/birds/{uuid}` with time=["morning", "afternoon"] | 1. Response status is 200 OK. 2. time array contains both values |
| 141 | TS-UPDATE-FIELDS | REQ-UPDATE-014 | Update Migratory True to False | Verify migratory can be changed | Valid API key, bird with migratory=true | 1. Send PATCH `/birds/{uuid}` with migratory=false | 1. Response status is 200 OK. 2. migratory is now false |
| 142 | TS-UPDATE-FIELDS | REQ-UPDATE-014 | Update Migratory to Null | Verify migratory can be set to null | Valid API key, existing bird UUID | 1. Send PATCH `/birds/{uuid}` with migratory=null | 1. Response status is 200 OK. 2. migratory is null |

### TS-UPDATE-RESULTS: Update Bird Response Scenarios

| # | Suite | Requirements | Name | Description | Preconditions | Steps | Expected Results |
|---|-------|--------------|------|-------------|---------------|-------|------------------|
| 143 | TS-UPDATE-RESULTS | REQ-UPDATE-015 | Successful Update | Verify 200 OK response with updated bird | Valid API key, existing bird UUID | 1. Send PATCH `/birds/{uuid}` with valid update data | 1. Response status is 200 OK. 2. Response body is updated bird object |
| 144 | TS-UPDATE-RESULTS | REQ-UPDATE-016 | Invalid Update Parameter | Verify 400 error for invalid parameter | Valid API key, existing bird UUID | 1. Send PATCH `/birds/{uuid}` with size="huge" | 1. Response status is 400 Bad Request. 2. Error message indicates invalid parameter |
| 145 | TS-UPDATE-RESULTS | REQ-UPDATE-017 | Update Non-Existing Bird | Verify 404 error for non-existing UUID | Valid API key, UUID doesn't exist | 1. Send PATCH `/birds/00000000-0000-0000-0000-000000000000` | 1. Response status is 404 Not Found. 2. Response contains "404 Not Found" error |
| 146 | TS-UPDATE-RESULTS | REQ-UPDATE-017 | Update Invalid UUID Format | Verify 400 error for invalid UUID | Valid API key | 1. Send PATCH `/birds/invalid-uuid` with update data | 1. Response status is 400 Bad Request |
| 147 | TS-UPDATE-RESULTS | REQ-UPDATE-018 | Update to Duplicate Bird Name | Verify 409 Conflict for duplicate names | Valid API key, two existing birds | 1. Add Bird A with name="Eagle1", scientificName="Sci1". 2. Add Bird B with name="Eagle2", scientificName="Sci2". 3. Update Bird B to name="Eagle1", scientificName="Sci1" | 1. Steps 1-2 return 201. 2. Step 3 returns 409 Conflict with duplicate message |
| 148 | TS-UPDATE-RESULTS | REQ-UPDATE-018 | Update Name Only Different Scientific Name | Verify update to different bird's name is allowed if scientific names differ | Valid API key, two existing birds | 1. Add Bird A with name="Eagle1", scientificName="Sci1". 2. Add Bird B with name="Eagle2", scientificName="Sci2". 3. Update Bird B to name="Eagle1" only | 1. Both adds return 201. 2. Update returns 200 (allowed as scientificName differs) |
| 149 | TS-UPDATE-RESULTS | REQ-UPDATE-019 | Server Error on Update | Verify 500 error handling | Valid API key, existing bird UUID, server error | 1. Send PATCH `/birds/{uuid}` when server returns error | 1. Response status is 500 Internal Server Error |
| 150 | TS-UPDATE-RESULTS | REQ-UPDATE-020 | Service Unavailable on Update | Verify 503 error handling | Valid API key, service down | 1. Send PATCH `/birds/{uuid}` when service unavailable | 1. Response status is 503 Service Unavailable |

### TS-DELETE: Delete Bird Tests

| # | Suite | Requirements | Name | Description | Preconditions | Steps | Expected Results |
|---|-------|--------------|------|-------------|---------------|-------|------------------|
| 151 | TS-DELETE | REQ-DELETE-001, REQ-DELETE-002 | Delete Existing Bird | Verify successful deletion of existing bird | Valid API key, existing bird UUID | 1. Send DELETE `/birds/{uuid}` for existing bird | 1. Response status is 204 No Content. 2. Response body is empty |
| 152 | TS-DELETE | REQ-DELETE-002 | Delete and Verify Removal | Verify bird is actually deleted | Valid API key, existing bird UUID | 1. Add a bird. 2. Delete the bird using returned UUID. 3. Attempt to retrieve the deleted bird | 1. Add returns 201. 2. Delete returns 204. 3. Retrieve returns 404 |
| 153 | TS-DELETE | REQ-DELETE-003 | Delete Invalid UUID Format | Verify 400 error for malformed UUID | Valid API key | 1. Send DELETE `/birds/not-a-uuid` | 1. Response status is 400 Bad Request. 2. Error message about invalid UUID |
| 154 | TS-DELETE | REQ-DELETE-003 | Delete Incomplete UUID | Verify 400 error for incomplete UUID | Valid API key | 1. Send DELETE `/birds/f47ac10b-58cc-4372` (partial UUID) | 1. Response status is 400 Bad Request |
| 155 | TS-DELETE | REQ-DELETE-003 | Delete UUID Invalid Characters | Verify 400 error for UUID with invalid chars | Valid API key | 1. Send DELETE `/birds/g47ac10b-58cc-4372-a567-0e02b2c3d479` | 1. Response status is 400 Bad Request |
| 156 | TS-DELETE | REQ-DELETE-004 | Delete Non-Existing Bird | Verify 404 error for non-existing bird | Valid API key | 1. Send DELETE `/birds/00000000-0000-0000-0000-000000000000` | 1. Response status is 404 Not Found. 2. Response contains "404 Not Found" error message |
| 157 | TS-DELETE | REQ-DELETE-005 | Server Error on Delete | Verify 500 error handling | Valid API key, existing bird UUID, server error | 1. Send DELETE `/birds/{uuid}` when server returns error | 1. Response status is 500 Internal Server Error. 2. Response contains error message |
| 158 | TS-DELETE | REQ-DELETE-006 | Service Unavailable on Delete | Verify 503 error handling | Valid API key, service down | 1. Send DELETE `/birds/{uuid}` when service unavailable | 1. Response status is 503 Service Unavailable. 2. Response contains error message |

### TS-DATA-VALIDATION: Data Model Validation Tests

| # | Suite | Requirements | Name | Description | Preconditions | Steps | Expected Results |
|---|-------|--------------|------|-------------|---------------|-------|------------------|
| 159 | TS-DATA-VALIDATION | REQ-DATA-001 | Bird Name Length Min | Verify name accepts 1 character | Valid API key | 1. Send POST `/birds` with name="A" | 1. Response status is 201 Created |
| 160 | TS-DATA-VALIDATION | REQ-DATA-001 | Bird Name Length Max | Verify name accepts 100 characters | Valid API key | 1. Send POST `/birds` with 100-character name | 1. Response status is 201 Created |
| 161 | TS-DATA-VALIDATION | REQ-DATA-001 | Bird Name Exceeds Max | Verify 101+ characters rejected | Valid API key | 1. Send POST `/birds` with 101-character name | 1. Response status is 400 Bad Request |
| 162 | TS-DATA-VALIDATION | REQ-DATA-002 | Scientific Name Min Length | Verify scientificName accepts 1 character | Valid API key | 1. Send POST `/birds` with scientificName="A" | 1. Response status is 201 Created |
| 163 | TS-DATA-VALIDATION | REQ-DATA-002 | Scientific Name Max Length | Verify scientificName accepts 200 characters | Valid API key | 1. Send POST `/birds` with 200-character scientificName | 1. Response status is 201 Created |
| 164 | TS-DATA-VALIDATION | REQ-DATA-002 | Scientific Name Exceeds Max | Verify 201+ characters rejected | Valid API key | 1. Send POST `/birds` with 201-character scientificName | 1. Response status is 400 Bad Request |
| 165 | TS-DATA-VALIDATION | REQ-DATA-003 | Description Min Length | Verify description accepts 1 character | Valid API key | 1. Send POST `/birds` with description="A" | 1. Response status is 201 Created |
| 166 | TS-DATA-VALIDATION | REQ-DATA-003 | Description Max Length | Verify description accepts 5000 characters | Valid API key | 1. Send POST `/birds` with 5000-character description | 1. Response status is 201 Created |
| 167 | TS-DATA-VALIDATION | REQ-DATA-003 | Description Exceeds Max | Verify 5001+ characters rejected | Valid API key | 1. Send POST `/birds` with 5001-character description | 1. Response status is 400 Bad Request |
| 168 | TS-DATA-VALIDATION | REQ-DATA-004 | Primary Color Red | Verify color "red" is accepted | Valid API key | 1. Send POST `/birds` with primaryColor="red" | 1. Response status is 201 Created |
| 169 | TS-DATA-VALIDATION | REQ-DATA-004 | Primary Color White | Verify color "white" is accepted | Valid API key | 1. Send POST `/birds` with primaryColor="white" | 1. Response status is 201 Created |
| 170 | TS-DATA-VALIDATION | REQ-DATA-004 | Primary Color All Valid Values | Verify all 15 colors accepted | Valid API key | 1. Create 15 test requests, each with different valid primaryColor | 1. All 15 requests return 201 Created |
| 171 | TS-DATA-VALIDATION | REQ-DATA-004 | Primary Color Invalid | Verify invalid color rejected | Valid API key | 1. Send POST `/birds` with primaryColor="silver" | 1. Response status is 400 Bad Request |
| 172 | TS-DATA-VALIDATION | REQ-DATA-005 | Secondary Color Max Items | Verify max 10 colors accepted | Valid API key | 1. Send POST `/birds` with secondaryColor array of 10 items | 1. Response status is 201 Created |
| 173 | TS-DATA-VALIDATION | REQ-DATA-005 | Secondary Color Exceeds Max | Verify 11+ colors rejected | Valid API key | 1. Send POST `/birds` with secondaryColor array of 11 items | 1. Response status is 400 Bad Request |
| 174 | TS-DATA-VALIDATION | REQ-DATA-005 | Secondary Color Empty | Verify empty array accepted | Valid API key | 1. Send POST `/birds` with secondaryColor=[] | 1. Response status is 201 Created |
| 175 | TS-DATA-VALIDATION | REQ-DATA-006 | Flight Pattern Linear | Verify "linear" accepted | Valid API key | 1. Send POST `/birds` with flightPattern="linear" | 1. Response status is 201 Created |
| 176 | TS-DATA-VALIDATION | REQ-DATA-006 | Flight Pattern Sine | Verify "sine" accepted | Valid API key | 1. Send POST `/birds` with flightPattern="sine" | 1. Response status is 201 Created |
| 177 | TS-DATA-VALIDATION | REQ-DATA-006 | Flight Pattern Circle | Verify "circle" accepted | Valid API key | 1. Send POST `/birds` with flightPattern="circle" | 1. Response status is 201 Created |
| 178 | TS-DATA-VALIDATION | REQ-DATA-006 | Flight Pattern Grounded | Verify "grounded" accepted | Valid API key | 1. Send POST `/birds` with flightPattern="grounded" | 1. Response status is 201 Created |
| 179 | TS-DATA-VALIDATION | REQ-DATA-006 | Flight Pattern Invalid | Verify invalid pattern rejected | Valid API key | 1. Send POST `/birds` with flightPattern="spiral" | 1. Response status is 400 Bad Request |
| 180 | TS-DATA-VALIDATION | REQ-DATA-007 | Size Small | Verify "small" accepted | Valid API key | 1. Send POST `/birds` with size="small" | 1. Response status is 201 Created |
| 181 | TS-DATA-VALIDATION | REQ-DATA-007 | Size Medium | Verify "medium" accepted | Valid API key | 1. Send POST `/birds` with size="medium" | 1. Response status is 201 Created |
| 182 | TS-DATA-VALIDATION | REQ-DATA-007 | Size Large | Verify "large" accepted | Valid API key | 1. Send POST `/birds` with size="large" | 1. Response status is 201 Created |
| 183 | TS-DATA-VALIDATION | REQ-DATA-007 | Size Invalid | Verify invalid size rejected | Valid API key | 1. Send POST `/birds` with size="xlarge" | 1. Response status is 400 Bad Request |
| 184 | TS-DATA-VALIDATION | REQ-DATA-008 | Song Min Length | Verify song of 1 character accepted | Valid API key | 1. Send POST `/birds` with song="A" | 1. Response status is 201 Created |
| 185 | TS-DATA-VALIDATION | REQ-DATA-008 | Song Max Length | Verify song of 200 characters accepted | Valid API key | 1. Send POST `/birds` with 200-character song | 1. Response status is 201 Created |
| 186 | TS-DATA-VALIDATION | REQ-DATA-008 | Song Exceeds Max | Verify 201+ characters rejected | Valid API key | 1. Send POST `/birds` with 201-character song | 1. Response status is 400 Bad Request |
| 187 | TS-DATA-VALIDATION | REQ-DATA-009 | Area Max Items | Verify max 50 locations accepted | Valid API key | 1. Send POST `/birds` with 50 area locations | 1. Response status is 201 Created |
| 188 | TS-DATA-VALIDATION | REQ-DATA-009 | Area Exceeds Max | Verify 51+ locations rejected | Valid API key | 1. Send POST `/birds` with 51 area locations | 1. Response status is 400 Bad Request |
| 189 | TS-DATA-VALIDATION | REQ-DATA-010 | Time Max Items | Verify max 4 time values accepted | Valid API key | 1. Send POST `/birds` with time=["morning", "afternoon", "evening", "night"] | 1. Response status is 201 Created |
| 190 | TS-DATA-VALIDATION | REQ-DATA-010 | Time Exceeds Max | Verify more than 4 values rejected | Valid API key | 1. Send POST `/birds` with time array of 5+ items | 1. Response status is 400 Bad Request |
| 191 | TS-DATA-VALIDATION | REQ-DATA-010 | Time Valid Values Morning | Verify "morning" accepted | Valid API key | 1. Send POST `/birds` with time=["morning"] | 1. Response status is 201 Created |
| 192 | TS-DATA-VALIDATION | REQ-DATA-010 | Time Valid Values Afternoon | Verify "afternoon" accepted | Valid API key | 1. Send POST `/birds` with time=["afternoon"] | 1. Response status is 201 Created |
| 193 | TS-DATA-VALIDATION | REQ-DATA-010 | Time Valid Values Evening | Verify "evening" accepted | Valid API key | 1. Send POST `/birds` with time=["evening"] | 1. Response status is 201 Created |
| 194 | TS-DATA-VALIDATION | REQ-DATA-010 | Time Valid Values Night | Verify "night" accepted | Valid API key | 1. Send POST `/birds` with time=["night"] | 1. Response status is 201 Created |
| 195 | TS-DATA-VALIDATION | REQ-DATA-010 | Time Invalid Value | Verify invalid time rejected | Valid API key | 1. Send POST `/birds` with time=["supper"] | 1. Response status is 400 Bad Request |
| 196 | TS-DATA-VALIDATION | REQ-DATA-011 | Migratory True | Verify boolean true accepted | Valid API key | 1. Send POST `/birds` with migratory=true | 1. Response status is 201 Created. 2. migratory=true in response |
| 197 | TS-DATA-VALIDATION | REQ-DATA-011 | Migratory False | Verify boolean false accepted | Valid API key | 1. Send POST `/birds` with migratory=false | 1. Response status is 201 Created. 2. migratory=false in response |
| 198 | TS-DATA-VALIDATION | REQ-DATA-011 | Migratory Null | Verify null accepted | Valid API key | 1. Send POST `/birds` with migratory=null | 1. Response status is 201 Created. 2. migratory=null in response |
| 199 | TS-DATA-VALIDATION | REQ-DATA-012 | UUID V4 Format | Verify generated UUID is v4 format | Valid API key | 1. Send POST `/birds` with complete data | 1. Response status is 201. 2. UUID field matches UUID v4 format pattern |
| 200 | TS-DATA-VALIDATION | REQ-DATA-012 | UUID Uniqueness | Verify each bird gets unique UUID | Valid API key | 1. Add 5 birds. 2. Compare UUIDs | 1. All 5 requests succeed. 2. All UUIDs are different |

---

## Summary

This document contains **200 comprehensive functional test scenarios** organized into **14 test suites** covering all **100 functional requirements** identified in the BirdBuddy API specification. The scenarios cover:

- **Authentication and Authorization** (8 scenarios)
- **Rate Limiting** (6 scenarios)
- **Search Functionality** (34 scenarios)
- **Bird Retrieval** (8 scenarios)
- **Adding Birds** (46 scenarios)
- **Updating Birds** (42 scenarios)
- **Deleting Birds** (8 scenarios)
- **Data Validation** (42 scenarios)

Each scenario includes detailed preconditions, step-by-step instructions, and explicit expected results to facilitate comprehensive testing of the BirdBuddy API.
