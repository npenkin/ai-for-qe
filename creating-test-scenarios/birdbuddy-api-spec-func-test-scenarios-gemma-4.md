Prepared by gemma-4.

# Functional Test Scenarios for BirdBuddy API

## Stage 1: Functional Requirements Identification

| Name | Description | Reference |
| :--- | :--- | :--- |
| FR_SEARCH_NAME | Search for birds using common name with exact match. | Search for a Bird |
| FR_SEARCH_SCIENTIFIC | Search for birds using scientific name with exact match. | Search for a Bird |
| FR_SEARCH_SIZE | Search for birds using size (small, medium, large). | Search for a Bird |
| FR_SEARCH_PRIMARY_COLOR | Search for birds using primary color. | Search for a Bird |
| FR_SEARCH_SECONDARY_COLOR | Search for birds using secondary colors (OR logic). | Search for a Bird |
| FR_SEARCH_SONG | Search for birds using song string. | Search for a Bird |
| FR_SEARCH_FLIGHT_PATTERN | Search for birds using flight pattern. | Search for a Bird |
| FR_SEARCH_AREA | Search for birds using area keyword (OR logic). | Search for a Bird |
| FR_SEARCH_TIME | Search for birds using observation time. | Search for a Bird |
| FR_SEARCH_LIMIT | Limit the number of search results (1-100, default 25). | Search for a Bird |
| FR_SEARCH_LOGIC | Search uses AND logic for all parameters except 'area'. | Search for a Bird |
| FR_RETRIEVE_ID | Retrieve a bird record by its UUID. | Retrieve a Bird by ID |
| FR_ADD_BIRD | Add a new bird record with mandatory and optional fields. | Add a New Bird |
| FR_ADD_UNIQUE | Prevent duplicate records based on common and scientific names. | Add a New Bird |
| FR_UPDATE_BIRD | Update existing bird fields (except UUID). | Update a Bird |
| FR_UPDATE_UNIQUE | Prevent updates that create duplicate name and scientific name combinations. | Update a Bird |
| FR_DELETE_BIRD | Delete a bird record by its UUID. | Delete a Bird |
| FR_AUTH_KEY | All endpoints require a valid `X-API-Key` header. | Status Codes / Rate Limiting |
| FR_RATE_LIMIT | Enforce rate limits per endpoint and API token (429 response). | Rate Limiting |
| FR_VALIDATION | Validate all input parameters against the Bird Data Model (length, types, enums). | Bird Data Model |
| FR_SERVER_ERRORS | Handle internal server errors (500) and service unavailability (503). | Status Codes |

## Stage 2: Test Suites Mapping

| Name | Requirements | Description |
| :--- | :--- | :--- |
| TS_SEARCH | FR_SEARCH_NAME, FR_SEARCH_SCIENTIFIC, FR_SEARCH_SIZE, FR_SEARCH_PRIMARY_COLOR, FR_SEARCH_SECONDARY_COLOR, FR_SEARCH_SONG, FR_SEARCH_FLIGHT_PATTERN, FR_SEARCH_AREA, FR_SEARCH_TIME, FR_SEARCH_LIMIT, FR_SEARCH_LOGIC | Verifies the correctness of the search functionality, including filtering logic, parameter combinations, and result limiting. |
| TS_RETRIEVAL | FR_RETRIEVE_ID | Verifies that birds can be retrieved by ID and that errors are handled for invalid/missing IDs. |
| TS_CRUD_MANAGEMENT | FR_ADD_BIRD, FR_ADD_UNIQUE, FR_UPDATE_BIRD, FR_UPDATE_UNIQUE, FR_DELETE_BIRD | Verifies the lifecycle of a bird record (Create, Update, Delete) and uniqueness constraints. |
| TS_DATA_VALIDATION | FR_VALIDATION | Verifies that the API correctly rejects invalid data based on the Bird Data Model restrictions. |
| TS_SECURITY_AND_LIMITS | FR_AUTH_KEY, FR_RATE_LIMIT | Verifies authentication requirements and the enforcement of rate limits. |
| TS_SYSTEM_RELIABILITY | FR_SERVER_ERRORS | Verifies the API's response when the server is unstable or unavailable. |

## Stage 3: Functional Test Scenarios

| # | Suite | Requirements | Name | Description | Preconditions | Steps | Expected results |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| 1 | TS_SEARCH | FR_SEARCH_NAME | Search by common name - Success | Verify that search returns birds matching the exact common name. | Birds with the name 'Bald Eagle' exist in the DB. | 1. Send GET `/birds?name=Bald Eagle` with valid API key. | 1. Status 200 OK.<br>2. `data` array contains birds with `name == "Bald Eagle"`. |
| 2 | TS_SEARCH | FR_SEARCH_NAME | Search by common name - No results | Verify that search returns an empty list for a non-existent name. | No birds with the name 'Imaginary Bird' exist. | 1. Send GET `/birds?name=Imaginary Bird` with valid API key. | 1. Status 200 OK.<br>2. `total` is 0.<br>3. `data` is an empty array. |
| 3 | TS_SEARCH | FR_SEARCH_LOGIC | Search with AND logic - Success | Verify that combining multiple filters (except area) uses AND logic. | A bird 'Blue Jay' with size 'small' and primaryColor 'blue' exists. | 1. Send GET `/birds?name=Blue Jay&size=small&primaryColor=blue` with valid API key. | 1. Status 200 OK.<br>2. `data` contains birds matching ALL three criteria. |
| 4 | TS_SEARCH | FR_SEARCH_AREA | Search by area - OR logic | Verify that search using multiple areas returns birds found in any of those areas. | Bird A is in 'Mexico', Bird B is in 'US Texas'. | 1. Send GET `/birds?area=Mexico&area=US Texas` with valid API key. | 1. Status 200 OK.<br>2. `data` contains both Bird A and Bird B. |
| 5 | TS_SEARCH | FR_SEARCH_LIMIT | Result limit - Boundary | Verify that the `limit` parameter restricts the number of returned records. | More than 50 birds exist in the DB. | 1. Send GET `/birds?limit=10` with valid API key. | 1. Status 200 OK.<br>2. `data` array length is <= 10. |
| 6 | TS_SEARCH | FR_SEARCH_LIMIT | Result limit - Invalid value | Verify that a limit outside the 1-100 range returns a 400 error. | None. | 1. Send GET `/birds?limit=101` with valid API key. | 1. Status 400 Bad Request.<br>2. Response body contains error message. |
| 7 | TS_RETRIEVAL | FR_RETRIEVE_ID | Get bird by valid UUID | Verify that a bird record is retrieved using its UUID. | A bird with UUID 'f47ac10b-58cc-4372-a567-0e02b2c3d479' exists. | 1. Send GET `/birds/f47ac10b-58cc-4372-a567-0e02b2c3d479` with valid API key. | 1. Status 200 OK.<br>2. Response body is a JSON object matching the Bird Data Model. |
| 8 | TS_RETRIEVAL | FR_RETRIEVE_ID | Get bird by non-existent UUID | Verify that a 404 is returned for a UUID that does not exist. | UUID '00000000-0000-0000-0000-000000000000' does not exist. | 1. Send GET `/birds/00000000-0000-0000-0000-000000000000` with valid API key. | 1. Status 404 Not Found. |
| 9 | TS_RETRIEVAL | FR_RETRIEVE_ID | Get bird by invalid UUID format | Verify that a 400 is returned for a malformed UUID. | None. | 1. Send GET `/birds/invalid-uuid-123` with valid API key. | 1. Status 400 Bad Request. |
| 10 | TS_CRUD_MANAGEMENT | FR_ADD_BIRD | Add bird - Full payload | Verify that a bird can be created with all provided fields. | Valid API key available. | 1. Send POST `/birds` with a complete JSON body containing all fields. | 1. Status 201 Created.<br>2. Response contains the created bird object including a generated `uuid`. |
| 11 | TS_CRUD_MANAGEMENT | FR_ADD_BIRD | Add bird - Mandatory fields only | Verify that a bird can be created using only required fields. | Valid API key available. | 1. Send POST `/birds` with only `name`, `scientificName`, `description`, `primaryColor`, and `size`. | 1. Status 201 Created.<br>2. Optional fields in response are null or empty arrays. |
| 12 | TS_CRUD_MANAGEMENT | FR_ADD_UNIQUE | Add duplicate bird - Conflict | Verify that adding a bird with an existing name and scientific name fails. | Bird 'Bald Eagle' ('Haliaeetus leucocephalus') already exists. | 1. Send POST `/birds` with `name="Bald Eagle"` and `scientificName="Haliaeetus leucocephalus"`. | 1. Status 409 Conflict.<br>2. Body contains "Duplicate bird record" message. |
| 13 | TS_CRUD_MANAGEMENT | FR_UPDATE_BIRD | Update bird fields - Success | Verify that specific fields of a bird can be updated. | Bird with UUID 'f47ac10b-58cc-4372-a567-0e02b2c3d479' exists. | 1. Send PATCH `/birds/f47ac10b-58cc-4372-a567-0e02b2c3d479` with `{"song": "New Song"}`. | 1. Status 200 OK.<br>2. Response body shows updated `song` and unchanged other fields. |
| 14 | TS_CRUD_MANAGEMENT | FR_UPDATE_UNIQUE | Update to duplicate identity - Conflict | Verify that updating a bird to a name/scientific name combo of another bird fails. | Bird A (Bald Eagle) and Bird B (Blue Jay) exist. | 1. Send PATCH `/birds/{BirdB_UUID}` with `{"name": "Bald Eagle", "scientificName": "Haliaeetus leucocephalus"}`. | 1. Status 409 Conflict. |
| 15 | TS_CRUD_MANAGEMENT | FR_DELETE_BIRD | Delete existing bird | Verify that a bird can be removed from the catalogue. | Bird with UUID 'f47ac10b-58cc-4372-a567-0e02b2c3d479' exists. | 1. Send DELETE `/birds/f47ac10b-58cc-4372-a567-0e02b2c3d479` with valid API key. | 1. Status 204 No Content. |
| 16 | TS_CRUD_MANAGEMENT | FR_DELETE_BIRD | Delete non-existent bird | Verify that deleting a non-existent bird returns 404. | UUID does not exist. | 1. Send DELETE `/birds/00000000-0000-0000-0000-000000000000` with valid API key. | 1. Status 404 Not Found. |
| 17 | TS_DATA_VALIDATION | FR_VALIDATION | Name length - Too long | Verify that a name exceeding 100 characters is rejected. | Valid API key available. | 1. Send POST `/birds` with `name` = 101 characters. | 1. Status 400 Bad Request. |
| 18 | TS_DATA_VALIDATION | FR_VALIDATION | Primary Color - Invalid enum | Verify that an unsupported color value is rejected. | Valid API key available. | 1. Send POST `/birds` with `primaryColor="neon-pink"`. | 1. Status 400 Bad Request. |
| 19 | TS_DATA_VALIDATION | FR_VALIDATION | Secondary Colors - Max limit | Verify that more than 10 secondary colors are rejected. | Valid API key available. | 1. Send POST `/birds` with `secondaryColor` array containing 11 colors. | 1. Status 400 Bad Request. |
| 20 | TS_DATA_VALIDATION | FR_VALIDATION | Observation Time - Max limit | Verify that more than 4 distinct observation times are rejected. | Valid API key available. | 1. Send POST `/birds` with `time` array containing 5 values. | 1. Status 400 Bad Request. |
| 21 | TS_SECURITY_AND_LIMITS | FR_AUTH_KEY | Missing X-API-Key | Verify that requests without the API key header are rejected. | None. | 1. Send GET `/birds` without `X-API-Key` header. | 1. Status 401 Unauthorized. |
| 22 | TS_SECURITY_AND_LIMITS | FR_AUTH_KEY | Invalid X-API-Key | Verify that requests with an incorrect API key are rejected. | None. | 1. Send GET `/birds` with `X-API-Key: wrong-key`. | 1. Status 401 Unauthorized. |
| 23 | TS_SECURITY_AND_LIMITS | FR_RATE_LIMIT | Exceed Rate Limit - Search | Verify that the 429 error is returned after 60 requests per minute. | Valid API key available. | 1. Send 61 GET `/birds` requests within 60 seconds. | 1. The 61st request returns Status 429 Too Many Requests.<br>2. `Retry-After` header is present. |
| 24 | TS_SECURITY_AND_LIMITS | FR_RATE_LIMIT | Exceed Rate Limit - Add | Verify that the 429 error is returned after 10 requests per minute for POST. | Valid API key available. | 1. Send 11 POST `/birds` requests within 60 seconds. | 1. The 11th request returns Status 429 Too Many Requests. |
| 25 | TS_SYSTEM_RELIABILITY | FR_SERVER_ERRORS | Server Internal Error | Verify the API's response when a server-side crash occurs. | Mock server configured to return 500. | 1. Send GET `/birds` with valid API key. | 1. Status 500 Internal Server Error.<br>2. Body contains `statusCode: 500`. |
| 26 | TS_SYSTEM_RELIABILITY | FR_SERVER_ERRORS | Service Unavailable | Verify the API's response during maintenance or overload. | Mock server configured to return 503. | 1. Send GET `/birds` with valid API key. | 1. Status 503 Service Unavailable.<br>2. Body contains `statusCode: 503`. |
