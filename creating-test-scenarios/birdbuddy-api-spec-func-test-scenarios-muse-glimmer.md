Prepared by muse-glimmer.

# BirdBuddy API Functional Test Scenarios

## Stage 1: Functional Requirements

| Name | Description | Reference |
|---|---|---|
| REQ_AUTH_X_API_KEY | All endpoints require a valid `X-API-Key` header. Missing or incorrect key returns 401 Unauthorized. | Endpoints - Search for a Bird / Retrieve a Bird by ID / Add a New Bird / Update a Bird / Delete a Bird - Status Codes |
| REQ_RATE_LIMIT | Each endpoint is rate limited per API token. Exceeding limit returns 429 Too Many Requests with `Retry-After` header. | Rate Limiting |
| REQ_BIRD_MODEL_UUID | `uuid` is a UUID v4 string generated for each new bird. | Bird Data Model |
| REQ_BIRD_MODEL_NAME | `name` is string 1-100 characters. | Bird Data Model |
| REQ_BIRD_MODEL_SCIENTIFIC_NAME | `scientificName` is string 1-200 characters. | Bird Data Model |
| REQ_BIRD_MODEL_DESCRIPTION | `description` is string 1-5000 characters. | Bird Data Model |
| REQ_BIRD_MODEL_PRIMARY_COLOR | `primaryColor` is single value from allowed enum. | Bird Data Model |
| REQ_BIRD_MODEL_SECONDARY_COLOR | `secondaryColor` is array of strings from allowed enum, max 10 values. | Bird Data Model |
| REQ_BIRD_MODEL_FLIGHT_PATTERN | `flightPattern` is single value from linear, sine, circle, grounded. | Bird Data Model |
| REQ_BIRD_MODEL_SIZE | `size` is single value from small, medium, large. | Bird Data Model |
| REQ_BIRD_MODEL_SONG | `song` is string 1-200 characters. | Bird Data Model |
| REQ_BIRD_MODEL_AREA | `area` is array of strings, max 50 values. | Bird Data Model |
| REQ_BIRD_MODEL_TIME | `time` is array of strings from morning, afternoon, evening, night, max 4 distinct values. | Bird Data Model |
| REQ_BIRD_MODEL_MIGRATORY | `migratory` is boolean or null. | Bird Data Model |
| REQ_SEARCH_ENDPOINT | GET `/birds` with query parameters returns list of birds. Supports name, scientificName, size, primaryColor, secondaryColor, song, flightPattern, area, time, limit. | Search for a Bird - Request specification |
| REQ_SEARCH_LOGIC_AND_OR | Search applies AND logic for all parameters except `area` which uses OR logic. | Search for a Bird - Request specification |
| REQ_SEARCH_DUPLICATE_IGNORE | Repeated query keys for secondaryColor, area, time ignore duplicate values. | Search for a Bird - Request specification |
| REQ_SEARCH_LIMIT | `limit` query parameter defaults to 25, allowed 1-100. | Search for a Bird - Request specification |
| REQ_SEARCH_RESPONSE_FORMAT | Response is JSON object with `total` and `data` array of bird objects. | Search for a Bird - Response Specification |
| REQ_SEARCH_STATUS_CODES | Returns 200, 400, 401, 429, 500, 503 as specified. | Search for a Bird - Status Codes |
| REQ_RETRIEVE_ENDPOINT | GET `/birds/{UUID}` returns single bird object. | Retrieve a Bird by ID - Request Specification |
| REQ_RETRIEVE_STATUS_CODES | Returns 200, 400 invalid UUID, 401, 404 not found, 429, 500, 503. | Retrieve a Bird by ID - Status Codes |
| REQ_ADD_ENDPOINT | POST `/birds` creates new bird. Body follows Bird Data Model. | Add a New Bird - Request Specification |
| REQ_ADD_REQUIRED_FIELDS | `name`, `scientificName`, `description`, `primaryColor`, `size` are required. | Add a New Bird - Request Specification |
| REQ_ADD_DUPLICATE_REJECT | Duplicate common + scientific name returns 409 Conflict. | Add a New Bird - Status Codes |
| REQ_ADD_RESPONSE_FORMAT | Successful creation returns 201 with bird object including generated uuid. | Add a New Bird - Response Specification |
| REQ_ADD_STATUS_CODES | Returns 201, 400, 401, 409, 429, 500, 503. | Add a New Bird - Status Codes |
| REQ_UPDATE_ENDPOINT | PATCH `/birds/{uuid}` updates existing bird fields except uuid. | Update a Bird - Request Specification |
| REQ_UPDATE_DUPLICATE_REJECT | Update that would cause duplicate name + scientific name is rejected. | Update a Bird - Description |
| REQ_DELETE_ENDPOINT | DELETE `/birds/{uuid}` removes bird. | Delete a Bird - Request Specification |
| REQ_DELETE_STATUS_CODES | Returns 204, 400 invalid UUID, 401, 404 not found, 429, 500, 503. | Delete a Bird - Status Codes |

## Stage 2: Test Suites

| Name | Requirements | Description |
|---|---|---|
| Authentication Suite | REQ_AUTH_X_API_KEY | Verifies that all endpoints enforce X-API-Key authentication and return 401 when missing or invalid. |
| Rate Limiting Suite | REQ_RATE_LIMIT | Verifies rate limits per endpoint and correct 429 response with Retry-After header. |
| Bird Data Model Validation Suite | REQ_BIRD_MODEL_UUID, REQ_BIRD_MODEL_NAME, REQ_BIRD_MODEL_SCIENTIFIC_NAME, REQ_BIRD_MODEL_DESCRIPTION, REQ_BIRD_MODEL_PRIMARY_COLOR, REQ_BIRD_MODEL_SECONDARY_COLOR, REQ_BIRD_MODEL_FLIGHT_PATTERN, REQ_BIRD_MODEL_SIZE, REQ_BIRD_MODEL_SONG, REQ_BIRD_MODEL_AREA, REQ_BIRD_MODEL_TIME, REQ_BIRD_MODEL_MIGRATORY | Validates field constraints, enums, lengths and array limits for bird objects on create and update. |
| Search Suite | REQ_SEARCH_ENDPOINT, REQ_SEARCH_LOGIC_AND_OR, REQ_SEARCH_DUPLICATE_IGNORE, REQ_SEARCH_LIMIT, REQ_SEARCH_RESPONSE_FORMAT, REQ_SEARCH_STATUS_CODES | Tests search query handling, AND/OR logic, duplicate handling, limit, response format and error statuses. |
| Retrieve Suite | REQ_RETRIEVE_ENDPOINT, REQ_RETRIEVE_STATUS_CODES | Tests retrieval by UUID including success, invalid UUID and not found cases. |
| Add Bird Suite | REQ_ADD_ENDPOINT, REQ_ADD_REQUIRED_FIELDS, REQ_ADD_DUPLICATE_REJECT, REQ_ADD_RESPONSE_FORMAT, REQ_ADD_STATUS_CODES | Tests creation of birds with validation, duplicate rejection and response format. |
| Update Bird Suite | REQ_UPDATE_ENDPOINT, REQ_UPDATE_DUPLICATE_REJECT | Tests partial updates, field validation and duplicate name/scientific name rejection. |
| Delete Bird Suite | REQ_DELETE_ENDPOINT, REQ_DELETE_STATUS_CODES | Tests deletion success, invalid UUID and not found cases. |

## Stage 3: Functional Test Scenarios

| # | Suite | Requirements | Name | Description | Preconditions | Steps | Expected results |
|---|---|---|---|---|---|---|---|
| 1 | Authentication Suite | REQ_AUTH_X_API_KEY | AUTH_SEARCH_NO_KEY | Search without X-API-Key returns 401 | Valid API token exists | 1. Send GET /birds with no X-API-Key header<br>2. Observe response | 1. Status 401 Unauthorized<br>2. Body contains error 401 |
| 2 | Authentication Suite | REQ_AUTH_X_API_KEY | AUTH_RETRIEVE_NO_KEY | Retrieve without X-API-Key returns 401 | Valid bird UUID exists | 1. Send GET /birds/{uuid} without X-API-Key<br>2. Observe response | 1. Status 401 Unauthorized |
| 3 | Authentication Suite | REQ_AUTH_X_API_KEY | AUTH_ADD_NO_KEY | Add without X-API-Key returns 401 | Valid payload prepared | 1. Send POST /birds without X-API-Key<br>2. Observe response | 1. Status 401 Unauthorized |
| 4 | Authentication Suite | REQ_AUTH_X_API_KEY | AUTH_DELETE_NO_KEY | Delete without X-API-Key returns 401 | Valid bird UUID exists | 1. Send DELETE /birds/{uuid} without X-API-Key<br>2. Observe response | 1. Status 401 Unauthorized |
| 5 | Rate Limiting Suite | REQ_RATE_LIMIT | RATE_SEARCH_EXCEEDED | Search rate limit exceeded returns 429 | Token with fresh rate limit | 1. Execute 61 GET /birds requests within 60 sec<br>2. Observe 61st response | 1. Status 429 Too Many Requests<br>2. Retry-After header present |
| 6 | Rate Limiting Suite | REQ_RATE_LIMIT | RATE_ADD_EXCEEDED | Add rate limit exceeded returns 429 | Token with fresh rate limit | 1. Execute 11 POST /birds requests within 60 sec<br>2. Observe 11th response | 1. Status 429 Too Many Requests |
| 7 | Bird Data Model Validation Suite | REQ_BIRD_MODEL_NAME, REQ_ADD_REQUIRED_FIELDS | VALID_NAME_LENGTH | Name exceeding 100 chars rejected | Valid token | 1. POST /birds with name 101 chars<br>2. Observe response | 1. Status 400 Bad Request |
| 8 | Bird Data Model Validation Suite | REQ_BIRD_MODEL_PRIMARY_COLOR | VALID_PRIMARY_COLOR_ENUM | Invalid primaryColor rejected | Valid token | 1. POST /birds with primaryColor=pink<br>2. Observe response | 1. Status 400 Bad Request |
| 9 | Bird Data Model Validation Suite | REQ_BIRD_MODEL_SECONDARY_COLOR | VALID_SECONDARY_COLOR_MAX | secondaryColor >10 values rejected | Valid token | 1. POST /birds with secondaryColor array of 11 items<br>2. Observe response | 1. Status 400 Bad Request |
| 10 | Bird Data Model Validation Suite | REQ_BIRD_MODEL_TIME | VALID_TIME_MAX_DISTINCT | time >4 distinct values rejected | Valid token | 1. POST /birds with time containing 5 distinct values<br>2. Observe response | 1. Status 400 Bad Request |
| 11 | Search Suite | REQ_SEARCH_ENDPOINT, REQ_SEARCH_RESPONSE_FORMAT | SEARCH_VALID_PARAMS | Search with valid params returns matching birds | Birds exist in catalogue | 1. GET /birds?size=large&primaryColor=white&song=Rat-tat-tat<br>2. Observe response | 1. Status 200 OK<br>2. total >=0 and data array present |
| 12 | Search Suite | REQ_SEARCH_LOGIC_AND_OR | SEARCH_AREA_OR_LOGIC | Area uses OR logic | Birds in different areas | 1. GET /birds?area=Mexico&area=New%20York<br>2. Observe response | 1. Status 200<br>2. Results include birds from either area |
| 13 | Search Suite | REQ_SEARCH_DUPLICATE_IGNORE | SEARCH_DUPLICATE_VALUES | Duplicate query values ignored | Birds exist | 1. GET /birds?secondaryColor=teal&secondaryColor=teal<br>2. Observe response | 1. Status 200<br>2. No error, results consistent |
| 14 | Search Suite | REQ_SEARCH_LIMIT | SEARCH_LIMIT_PARAM | Limit parameter caps results | Many matching birds | 1. GET /birds?size=small&limit=5<br>2. Observe response | 1. Status 200<br>2. data length <=5 |
| 15 | Search Suite | REQ_SEARCH_STATUS_CODES | SEARCH_INVALID_PARAM | Invalid time value returns 400 | Valid token | 1. GET /birds?time=supper<br>2. Observe response | 1. Status 400 Bad Request |
| 16 | Search Suite | REQ_SEARCH_ENDPOINT | SEARCH_NO_RESULTS | Non-existing search returns empty | Valid token | 1. GET /birds?area=NO_SUCH_PLACE_EXISTS<br>2. Observe response | 1. Status 200<br>2. total 0 and data [] |
| 17 | Retrieve Suite | REQ_RETRIEVE_ENDPOINT | RETRIEVE_EXISTING | Retrieve existing bird by UUID | Bird exists with known UUID | 1. GET /birds/{uuid}<br>2. Observe response | 1. Status 200<br>2. Body contains matching uuid and fields |
| 18 | Retrieve Suite | REQ_RETRIEVE_STATUS_CODES | RETRIEVE_INVALID_UUID | Invalid UUID format returns 400 | Valid token | 1. GET /birds/invalid-uuid<br>2. Observe response | 1. Status 400 Bad Request |
| 19 | Retrieve Suite | REQ_RETRIEVE_STATUS_CODES | RETRIEVE_NOT_FOUND | Non-existing UUID returns 404 | Valid token | 1. GET /birds/f47ac10b-58cc-4372-a567-0e02b2c3d999<br>2. Observe response | 1. Status 404 Not Found |
| 20 | Add Bird Suite | REQ_ADD_ENDPOINT, REQ_ADD_RESPONSE_FORMAT | ADD_VALID_BIRD | Add valid bird returns 201 with uuid | Valid token, unique name/scientific | 1. POST /birds with required fields<br>2. Observe response | 1. Status 201 Created<br>2. Response contains uuid v4 and submitted fields |
| 21 | Add Bird Suite | REQ_ADD_DUPLICATE_REJECT | ADD_DUPLICATE | Duplicate name+scientific rejects | Bird already exists | 1. POST /birds with same name/scientific as existing<br>2. Observe response | 1. Status 409 Conflict |
| 22 | Add Bird Suite | REQ_ADD_REQUIRED_FIELDS | ADD_MISSING_REQUIRED | Missing required field returns 400 | Valid token | 1. POST /birds without name<br>2. Observe response | 1. Status 400 Bad Request |
| 23 | Update Bird Suite | REQ_UPDATE_ENDPOINT | UPDATE_PARTIAL | Partial update succeeds | Bird exists | 1. PATCH /birds/{uuid} with flightPattern and song<br>2. Observe response | 1. Status 200 OK<br>2. Updated fields changed, others unchanged |
| 24 | Update Bird Suite | REQ_UPDATE_DUPLICATE_REJECT | UPDATE_DUPLICATE_NAME | Update causing duplicate name rejected | Two birds exist | 1. PATCH /birds/{uuid} to match another bird's name+scientific<br>2. Observe response | 1. Status 409 Conflict |
| 25 | Delete Bird Suite | REQ_DELETE_ENDPOINT | DELETE_EXISTING | Delete existing bird returns 204 | Bird exists | 1. DELETE /birds/{uuid}<br>2. Observe response | 1. Status 204 No Content |
| 26 | Delete Bird Suite | REQ_DELETE_STATUS_CODES | DELETE_NOT_FOUND | Delete non-existing bird returns 404 | Valid token | 1. DELETE /birds/non-existing-uuid<br>2. Observe response | 1. Status 404 Not Found |
| 27 | Delete Bird Suite | REQ_DELETE_STATUS_CODES | DELETE_INVALID_UUID | Delete with invalid UUID returns 400 | Valid token | 1. DELETE /birds/bad-uuid<br>2. Observe response | 1. Status 400 Bad Request |
