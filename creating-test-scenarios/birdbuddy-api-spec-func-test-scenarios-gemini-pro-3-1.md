Prepared by gemini-3.1-pro-preview.

# Functional Test Scenarios for BirdBuddy API

## Stage 1: Functional Requirements

| Name | Description | Reference |
| :--- | :--- | :--- |
| **REQ-SRCH-01** | Search allows filtering by multiple criteria (name, scientificName, size, primaryColor, secondaryColor, song, flightPattern, time) using AND logic. Exact match is used for names. | `GET /birds` (Search for a Bird) |
| **REQ-SRCH-02** | Search parameter `area` uses keyword search and applies OR logic when multiple values are provided. | `GET /birds` (Search for a Bird) |
| **REQ-SRCH-03** | Repeated keys for array parameters (`secondaryColor`, `area`, `time`) are grouped, and duplicate values are ignored. | `GET /birds` (Search for a Bird) |
| **REQ-SRCH-04** | The `limit` query parameter restricts the maximum number of records returned (default is 25, max is 100). | `GET /birds` (Search for a Bird) |
| **REQ-GET-01** | A single bird record can be retrieved using a valid UUID v4. | `GET /birds/{uuid}` (Retrieve a Bird by ID) |
| **REQ-GET-02** | Retrieving a bird with a non-existent UUID returns a 404 Not Found error. | `GET /birds/{uuid}` (Retrieve a Bird by ID) |
| **REQ-POST-01** | A new bird can be added by providing valid required fields (`name`, `scientificName`, `description`, `primaryColor`, `size`). A UUID is generated. | `POST /birds` (Add a New Bird) |
| **REQ-POST-02** | The system rejects creating a new bird if an existing record shares the same common `name` AND `scientificName` (returns 409 Conflict). | `POST /birds` (Add a New Bird) |
| **REQ-PATCH-01** | An existing bird's fields can be updated partially without affecting unmentioned fields. UUID cannot be updated. | `PATCH /birds/{uuid}` (Update a Bird) |
| **REQ-PATCH-02** | The system rejects updating a bird if the new name combination conflicts with an existing bird's common and scientific names (returns 409 Conflict). | `PATCH /birds/{uuid}` (Update a Bird) |
| **REQ-DEL-01** | An existing bird record can be permanently deleted using its UUID (returns 204 No Content). | `DELETE /birds/{uuid}` (Delete a Bird) |
| **REQ-VAL-01** | The system validates parameters strictly against the Bird Data Model (string lengths, enum values, array maximum sizes). Invalid data returns 400 Bad Request. | All Endpoints (Bird Data Model) |
| **REQ-VAL-02** | The system rejects malformed or invalid UUID formats (returns 400 Bad Request). | `GET /birds/{uuid}`, `PATCH`, `DELETE` |
| **REQ-AUTH-01** | Requests missing or providing an incorrect `X-API-Key` header are rejected (returns 401 Unauthorized). | All Endpoints |

---

## Stage 2: Test Suites

| Name | Requirements | Description |
| :--- | :--- | :--- |
| **Search and Retrieval Suite** | REQ-SRCH-01, REQ-SRCH-02, REQ-SRCH-03, REQ-SRCH-04, REQ-GET-01, REQ-GET-02 | This suite verifies the ability to query the bird catalogue effectively. It checks if the search logic (AND for most fields, OR for areas), pagination (limit), duplicate key handling, and direct UUID retrieval work as specified. |
| **Bird Lifecycle (CRUD) Suite** | REQ-POST-01, REQ-POST-02, REQ-PATCH-01, REQ-PATCH-02, REQ-DEL-01 | This suite covers the creation, modification, and deletion of bird records. It ensures that birds can be safely added and updated, tests the duplicate prevention mechanism (409 Conflict), and confirms successful deletion. |
| **Data Validation Suite** | REQ-VAL-01, REQ-VAL-02 | This suite tests the boundary conditions and constraints defined in the Bird Data Model. It ensures the API catches invalid enum values, strings exceeding character limits, arrays exceeding size limits, and invalid UUIDs. |
| **Security and Auth Suite** | REQ-AUTH-01 | This suite ensures that the API is securely protected by the `X-API-Key` header across all exposed endpoints and correctly blocks unauthorized access. |

---

## Stage 3: Functional Test Scenarios

| # | Suite | Requirements | Name | Description | Preconditions | Steps | Expected results |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| 1 | Search and Retrieval Suite | REQ-SRCH-01, REQ-SRCH-02 | Search with AND/OR logic | Verify that searching applies AND logic for normal attributes and OR logic for the `area` parameter. | At least 3 birds exist: Bird A (`size=large`, `area=["US Texas"]`), Bird B (`size=large`, `area=["Mexico"]`), Bird C (`size=small`, `area=["Mexico"]`). API Key is valid. | 1. Send `GET /birds?size=large&area=US%20Texas&area=Mexico` with `X-API-Key`. | 1. Status: `200 OK`. Response `total` is 2. `data` array contains Bird A and Bird B, but not Bird C (because it fails the AND condition for `size=large`). |
| 2 | Search and Retrieval Suite | REQ-SRCH-03 | Duplicate array parameter handling | Verify that supplying duplicate values in query arrays (e.g., `time`) ignores the duplicates safely without error. | Bird A exists with `time=["morning", "evening"]`. API Key is valid. | 1. Send `GET /birds?time=morning&time=morning` with `X-API-Key`. | 1. Status: `200 OK`. Response does not throw a 400 error. The `data` array returns birds observable in the morning. |
| 3 | Search and Retrieval Suite | REQ-SRCH-04 | Search limit enforcement | Verify that the `limit` query parameter successfully caps the maximum number of returned items. | At least 5 birds exist in the catalogue. API Key is valid. | 1. Send `GET /birds?limit=3` with `X-API-Key`. | 1. Status: `200 OK`. Response `total` is 3. The `data` array contains exactly 3 bird objects. |
| 4 | Search and Retrieval Suite | REQ-GET-01, REQ-GET-02 | Retrieve bird by UUID | Verify behavior when retrieving a bird with valid and non-existent UUIDs. | Bird A exists with a known valid UUID. API Key is valid. | 1. Send `GET /birds/{valid_uuid}`. <br>2. Send `GET /birds/ffffffff-ffff-4fff-8fff-ffffffffffff` (non-existent). | 1. Status: `200 OK`. Body contains Bird A's JSON object. <br>2. Status: `404 Not Found`. Body contains standard error JSON. |
| 5 | Bird Lifecycle Suite | REQ-POST-01 | Add a new bird (Happy Path) | Verify a new bird can be added with all required and optional fields correctly formatted. | No bird exists with name "Test Falcon" and scientific "Testus falco". API Key is valid. | 1. Send `POST /birds` with JSON body containing valid required fields (name, scientificName, description, primaryColor, size) and some optional fields (e.g., flightPattern="linear"). | 1. Status: `201 Created`. Body returns the created bird object including a generated UUIDv4 and matching submitted data. |
| 6 | Bird Lifecycle Suite | REQ-POST-02 | Add a duplicate bird | Verify that adding a bird with the exact same common name and scientific name as an existing record fails. | Bird A exists with name="Bald Eagle" and scientificName="Haliaeetus leucocephalus". API Key is valid. | 1. Send `POST /birds` with JSON body using name="Bald Eagle" and scientificName="Haliaeetus leucocephalus". | 1. Status: `409 Conflict`. Body contains error: "Duplicate bird record with the given common and scientific names is found". |
| 7 | Bird Lifecycle Suite | REQ-PATCH-01 | Partially update a bird | Verify that providing a subset of fields updates the bird without clearing unspecified fields. | Bird A exists with `flightPattern="linear"`, `size="large"`, `song="Screech"`. API Key is valid. | 1. Send `PATCH /birds/{uuid}` with JSON body `{"song": "New Song"}`. | 1. Status: `200 OK`. Response body shows updated bird with `song="New Song"`, while `flightPattern` and `size` remain unchanged. |
| 8 | Bird Lifecycle Suite | REQ-PATCH-02 | Update resulting in a duplicate | Verify that patching a bird's names to match an existing bird's names is blocked. | Bird A ("Robin", "Erithacus") and Bird B ("Eagle", "Aquila") exist. API Key is valid. | 1. Send `PATCH /birds/{uuid_of_B}` with JSON body `{"name": "Robin", "scientificName": "Erithacus"}`. | 1. Status: `409 Conflict`. The bird record is not updated. |
| 9 | Bird Lifecycle Suite | REQ-DEL-01 | Delete an existing bird | Verify that an existing bird can be permanently removed from the catalogue. | Bird A exists with a known valid UUID. API Key is valid. | 1. Send `DELETE /birds/{uuid}`. <br>2. Send `GET /birds/{uuid}` to verify. | 1. Status: `204 No Content`. Body is empty. <br>2. Status: `404 Not Found`. |
| 10 | Data Validation Suite | REQ-VAL-01 | Validation of enumeration fields | Verify that passing a value outside the allowed enum list results in a bad request. | API Key is valid. | 1. Send `POST /birds` with all required fields but `primaryColor="neon"`. | 1. Status: `400 Bad Request`. Body contains detailed error message about the incorrect `primaryColor` value. |
| 11 | Data Validation Suite | REQ-VAL-01 | Validation of array maximum limits | Verify that sending an array exceeding maximum length limits is rejected. | API Key is valid. | 1. Send `POST /birds` with valid required fields and `secondaryColor` array containing 11 valid colors. | 1. Status: `400 Bad Request`. Body contains detailed error about exceeding the maximum 10 values for `secondaryColor`. |
| 12 | Data Validation Suite | REQ-VAL-02 | Validation of malformed UUID | Verify that using an improperly formatted UUID in the path returns a validation error. | API Key is valid. | 1. Send `GET /birds/invalid-uuid-string`. | 1. Status: `400 Bad Request`. Body contains standard error JSON. |
| 13 | Security and Auth Suite | REQ-AUTH-01 | Endpoints require authentication | Verify that omitting the `X-API-Key` header results in unauthorized access. | No specific data setup required. | 1. Send `GET /birds` without `X-API-Key` header. <br>2. Send `POST /birds` without `X-API-Key` header. | 1. Status: `401 Unauthorized` for GET. <br>2. Status: `401 Unauthorized` for POST. |
