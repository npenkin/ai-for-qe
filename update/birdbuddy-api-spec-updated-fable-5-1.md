Prepared by claude-fable-5-1.

# BirdBuddy API

## Overview

The BirdBuddy API is an essential tool for any developer or company aiming at cataloging and identifying birds. The community supported catalogue of existing and extinct birds is the biggest in the world. The API fully supports REST architecture.

The API is used in the BirdBuddy service deployed to Global Cloud and provides 99.999% uptime. You can find the current availability statistics at https://www.birdbuddy.org/sla. A network of regional datacenters minimizes latency. The service may be used for personal and commercial purposes subject to the authentication requirements and rate limits described below.

The API's source is published to [GitHub](https://www.github.com/birdbuddy) and has a vibrant and active community of contributors.

## API Versions

This document describes **v3** of the BirdBuddy API.

v2 is deprecated and remains available in both environments for backward compatibility only; it does not receive new features and will be retired with a 6-month notice. v3 introduces the `X-RateLimit-*` response headers, the `offset` pagination parameter for the search endpoint, and the `409 Conflict` duplicate-record check. New integrations must target v3.

## Environments and Base URLs

### QA

The latest release candidate. The data is purged on the 1st of each month. A new version is deployed when necessary with a 24-hour notice.

* v2: https://www.qa.birdbuddy.org/v2
* v3: https://www.qa.birdbuddy.org/v3

### Production

* v2: https://www.birdbuddy.org/v2
* v3: https://www.birdbuddy.org/v3

## Authentication and Authorization

Every request to the API must be authenticated with an API token passed in the `X-API-Key` HTTP header:

```
X-API-Key: <token>
```

* A token is obtained by registering an application in the BirdBuddy Developer Portal at https://www.birdbuddy.org/developers. Separate tokens are issued for the QA and Production environments.
* Tokens are secrets and must not be embedded in client-side code or shared publicly. A compromised token can be revoked and re-issued in the Developer Portal.
* If the `X-API-Key` header is missing, malformed, revoked, or does not match any registered application, the API responds with `401 Unauthorized` (see [Error Responses](#error-responses)).
* All tokens are granted the same permissions: read access (search, retrieve) and write access (add, update, delete) to the catalogue.
* Rate limits are applied per token (see [Rate Limiting](#rate-limiting)).

## Rate Limiting

The rate limit for each endpoint is set per API token (see [Authentication and Authorization](#authentication-and-authorization)).

| Endpoint              | Limit | Window Duration (sec) |
| --------------------- | :---: | :-------------------: |
| Search for a Bird     |  60   |          60           |
| Retrieve a Bird by ID |  60   |          60           |
| Add a New Bird        |  10   |          60           |
| Update a Bird         |  10   |          60           |
| Delete a Bird         |  10   |          60           |

Every response (successful or not) includes the following rate-limiting headers:

| Header                  | Description                                                                                            | Example                          |
| ----------------------- | ------------------------------------------------------------------------------------------------------ | -------------------------------- |
| `X-RateLimit-Limit`     | The maximum number of requests allowed for the endpoint within the current window.                     | `X-RateLimit-Limit: 60`          |
| `X-RateLimit-Remaining` | The number of requests remaining in the current window.                                                | `X-RateLimit-Remaining: 42`      |
| `X-RateLimit-Reset`     | The time at which the current window resets, as a Unix timestamp in seconds (UTC).                     | `X-RateLimit-Reset: 1735689600`  |
| `Retry-After`           | Returned only with `429 Too Many Requests`. The number of seconds to wait before retrying the request. | `Retry-After: 17`                |

When a client exceeds the current limit, the API returns `429 Too Many Requests` with the `Retry-After` header, which specifies the cooldown period in seconds.

It's recommended to implement exponential backoff to avoid exceeding the rate limit.

## Error Responses

All error responses share the same JSON schema:

| Attribute    |  Type  | Description                                                                                                                          |
| ------------ | :----: | ------------------------------------------------------------------------------------------------------------------------------------ |
| `error`      | string | A human-readable message. For `400 Bad Request` it contains a detailed description of the problem; for other codes it is the standard HTTP reason phrase prefixed with the status code. |
| `statusCode` | number | The HTTP status code of the response, as a number.                                                                                   |

Example:

```json
{
  "error": "Parameter 'time' has an incorrect value: 'supper'",
  "statusCode": 400
}
```

The following status codes are shared by all endpoints and are not repeated in the per-endpoint tables:

| Scenario                                                                                                      | Status Code                | Body                                                                      |
| ------------------------------------------------------------------------------------------------------------- | -------------------------- | ------------------------------------------------------------------------- |
| Incorrect or missing `X-API-Key` header                                                                       | 401 Unauthorized           | {<br>  "error": "401 Unauthorized",<br>  "statusCode": 401<br>}           |
| The HTTP method is not supported for the requested path (e.g. `PUT /birds`)                                   | 405 Method Not Allowed     | {<br>  "error": "405 Method Not Allowed",<br>  "statusCode": 405<br>}     |
| The `Accept` header does not allow `application/json`                                                         | 406 Not Acceptable         | {<br>  "error": "406 Not Acceptable",<br>  "statusCode": 406<br>}         |
| The `Content-Type` header of a request with a body is not `application/json`                                  | 415 Unsupported Media Type | {<br>  "error": "415 Unsupported Media Type",<br>  "statusCode": 415<br>} |
| The rate limit is exceeded. Check the [rate limiting headers](#rate-limiting) in the response for extra information. | 429 Too Many Requests      | {<br>  "error": "429 Too Many Requests",<br>  "statusCode": 429<br>}      |
| The server is not able to process the request due to internal issues                                          | 500 Internal Server Error  | {<br>  "error": "500 Internal Server Error",<br>  "statusCode": 500<br>}  |
| The server is currently unable to handle the request                                                          | 503 Service Unavailable    | {<br>  "error": "503 Service Unavailable",<br>  "statusCode": 503<br>}    |

For completeness, the 401, 429, 500 and 503 codes are also listed in each endpoint's Status Codes table.

## Bird Data Model

| Parameter      |        Type        |                                                        Values                                                         | Restrictions                  |               Example                |                                                                         Description                                                                          |
| -------------- | :----------------: | :-------------------------------------------------------------------------------------------------------------------: | ----------------------------- | :----------------------------------: | :----------------------------------------------------------------------------------------------------------------------------------------------------------: |
| uuid           |       string       |                                                        UUID v4                                                        | UUID v4 format                | f47ac10b-58cc-4372-a567-0e02b2c3d479 |                              A unique UUID v4 identifier of the bird. It's generated for every new bird added to the catalogue.                              |
| name           |       string       |                                                       A string                                                        | Min 1 and max 100 characters  |              Bald Eagle              |                                                                  The common name of a bird                                                                   |
| scientificName |       string       |                                                       A string                                                        | Min 1 and max 200 characters  |       Haliaeetus leucocephalus       |                                                            The scientific latin name of the bird                                                             |
| description    |       string       |                                                       A string                                                        | Min 1 and max 5000 characters |    An iconic American alpha bird.    |                    A description providing you with the general information about the bird. Includes interesting facts and observations.                     |
| primaryColor   |       string       | red, yellow, blue, orange, green, purple, pistachio, teal, indigo, magenta, scarlet, amber, grey, black, white, brown | Only 1 value                  |               magenta                |                                                      The main color of the bird that covers most of it                                                       |
| secondaryColor |  Array of strings  | red, yellow, blue, orange, green, purple, pistachio, teal, indigo, magenta, scarlet, amber, grey, black, white, brown | Maximum 10 values             |        `["white", "orange"]`         |                                                              A list of minor colors of the bird                                                              |
| flightPattern  |       string       |                                            linear, sine, circle, grounded                                             | Only 1 value                  |                circle                |                                                           The observed flight pattern of the bird                                                            |
| size           |       string       |                                                 small, medium, large                                                  | Only 1 value                  |                large                 | The approximate size of the bird. `small` is for birds less than 4", `medium` for 4" to 12" (inclusive), and `large` for birds greater than 12" |
| song           |       string       |                                                       A string                                                        | Min 1 and max 200 characters  |             Rat-tat-tat              |                                                                       The bird's song                                                                        |
| area           |  Array of strings  |                                                  Names of locations                                                   | Maximum 50 values             |       `["US Texas", "Mexico"]`       |                                                       A list of locations where the bird is observed                                                        |
| time           |  Array of strings  |                                           morning, afternoon, evening, night                                           | Maximum 4 distinct values     |      `["morning", "afternoon"]`      |                                                           A list of times when the bird is observed                                                          |
| migratory      | boolean (nullable) |                                                   true, false, null                                                   | Only 1 value                  |                 true                 |                       Identifies whether the bird is migratory. `null` means that the migratory status of the bird is unknown.                        |

### Presence of fields in responses

Every bird object returned by the API always contains all 12 fields listed above; no field is ever omitted.

* `uuid`, `name`, `scientificName`, `description`, `primaryColor` and `size` are always present with a non-null value.
* Optional scalar fields that were not supplied on creation (`flightPattern`, `song`) are returned as `null`.
* Optional array fields that were not supplied on creation (`secondaryColor`, `area`, `time`) are returned as an empty array `[]`.
* `migratory` is returned as `null` when it was not supplied on creation or was explicitly set to `null`.

## Endpoints

### Search for a Bird

Returns a list of birds satisfying the given search criteria.

The search applies the AND logic between all supplied parameters, i.e. a bird is returned only if it satisfies every parameter present in the query. Matching semantics for individual parameters:

* `name` and `scientificName`: exact, case-insensitive match of the whole value.
* `song`: exact, case-insensitive match of the whole value.
* `size`, `primaryColor`, `flightPattern`: exact match of the enumerated value.
* `secondaryColor` and `time` (multi-value): ALL logic – the bird's list must contain every supplied value (it may contain additional values).
* `area` (multi-value): ANY (OR) logic – the bird matches if at least one of its locations matches at least one of the supplied values. Keyword search is used to identify the most probable match.

#### Request Specification

* Method: `GET`
* Path: `/birds`
* Headers: `X-API-Key: <token>`, `Accept: application/json`
* Restrictions on the query parameters' values: in accordance with [Bird Data Model](#bird-data-model).

| Query parameter |       Type       |     Required      |                                                        Values                                                         |                 Example                 |                                                                                                       Description                                                                                                       |
| --------------- | :--------------: | :---------------: | :-------------------------------------------------------------------------------------------------------------------: | :-------------------------------------: | :---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------: |
| name            |      string      |        No         |                                                       A string                                                        |            name=Bald%20Eagle            |                                                                          The common name of a bird. Exact, case-insensitive match is applied.                                                                           |
| scientificName  |      string      |        No         |                                                       A string                                                        |     scientificName=Strix%20nebulosa     |                                                                    The scientific latin name of the bird. Exact, case-insensitive match is applied.                                                                     |
| size            |      string      |        No         |                                                 small, medium, large                                                  |               size=small                |                               The approximate size of the bird. `small` is for birds less than 4", `medium` for 4" to 12" (inclusive), and `large` for birds greater than 12"                                |
| primaryColor    |      string      |        No         | red, yellow, blue, orange, green, purple, pistachio, teal, indigo, magenta, scarlet, amber, grey, black, white, brown |           primaryColor=indigo           |                                                                                    The main color of the bird that covers most of it                                                                                    |
| secondaryColor  | Array of strings |        No         | red, yellow, blue, orange, green, purple, pistachio, teal, indigo, magenta, scarlet, amber, grey, black, white, brown | secondaryColor=teal&secondaryColor=blue |            A list of minor colors of the bird. Repeated keys can be used to specify multiple values. Duplicate values are ignored. The ALL logic is used: the bird must have every supplied color.            |
| song            |      string      |        No         |                                                       A string                                                        |            song=Rat-tat-tat             |                                                                                The bird's song. Exact, case-insensitive match is applied.                                                                                |
| flightPattern   |      string      |        No         |                                            linear, sine, circle, grounded                                             |           flightPattern=sine            |                                                                                         The observed flight pattern of the bird                                                                                         |
| area            | Array of strings |        No         |                                                       A string                                                        |  area=Mexico%20Cozumel&area=New%20York  | A list of locations where the bird is observed. The API uses keyword search to identify the most probable match. Repeated keys can be used to specify multiple values. Duplicate values are ignored. The OR logic is used. |
| time            | Array of strings |        No         |                                          morning, afternoon, evening, night                                           |         time=morning&time=night         |                A list of times when the bird is observed. Repeated keys can be used to specify multiple values. Duplicate values are ignored. The ALL logic is used: the bird must be observed at every supplied time.                |
| limit           |      number      | No (default = 25) |                                                        1 - 100                                                        |                limit=50                 |                                                                                      The maximum number of records to be returned.                                                                                      |
| offset          |      number      | No (default = 0)  |                                                     0 or greater                                                      |                offset=50                |                                    The number of matching records to skip before returning results. Use together with `limit` to page through results larger than `limit`.                                    |

#### Response Specification

The top-level response is a JSON object with 4 attributes:
* `total` - the total number of records matching the search criteria, regardless of `limit` and `offset`.
* `limit` - the `limit` value applied to the request (the supplied value or the default of 25).
* `offset` - the `offset` value applied to the request (the supplied value or the default of 0).
* `data` - an array of JSON [bird objects](#bird-data-model). Contains at most `limit` records. The number of records in `data` may be less than `total` when `total` exceeds `limit`; request the next page by increasing `offset` by `limit`.

A sample with the list of bird objects omitted:

```json
{
  "total": 3,
  "limit": 25,
  "offset": 0,
  "data": []
}
```

#### Status Codes

See also the shared [Error Responses](#error-responses).

| Scenario                                                                                           | Status Code               | Body                                                                     |
| -------------------------------------------------------------------------------------------------- | ------------------------- | ------------------------------------------------------------------------ |
| Successful search with 0 or more results                                                           | 200 OK                    | In accordance with the response specification                            |
| One of the query parameters has an incorrect value                                                 | 400 Bad Request           | {<br>  "error": "Detailed message",<br>  "statusCode": 400<br>}          |
| Incorrect or missing `X-API-Key` header                                                            | 401 Unauthorized          | {<br>  "error": "401 Unauthorized",<br>  "statusCode": 401<br>}          |
| The rate limit is exceeded. Check the rate limiting headers in the response for extra information. | 429 Too Many Requests     | {<br>  "error": "429 Too Many Requests",<br>  "statusCode": 429<br>}     |
| The server is not able to process the request due to internal issues                               | 500 Internal Server Error | {<br>  "error": "500 Internal Server Error",<br>  "statusCode": 500<br>} |
| The server is currently unable to handle the request                                               | 503 Service Unavailable   | {<br>  "error": "503 Service Unavailable",<br>  "statusCode": 503<br>}   |

#### Examples

##### Find all large birds with the primary color white and singing "Rat-tat-tat".

Request:
* GET `/birds?size=large&primaryColor=white&song=Rat-tat-tat`

Response (200 OK):

```json
{
  "total": 2,
  "limit": 25,
  "offset": 0,
  "data": [
    {
      "uuid": "f47ac10b-58cc-4372-a567-0e02b2c3d479",
      "name": "Great Egret",
      "scientificName": "Ardea alba",
      "description": "The Old World population is often referred to as the 'great white egret'. This species is sometimes confused with the great white heron of the Caribbean, which is a white morph of the closely related great blue heron.",
      "primaryColor": "white",
      "secondaryColor": [],
      "flightPattern": "linear",
      "size": "large",
      "song": "Rat-tat-tat",
      "area": [
        "US Texas"
      ],
      "time": [
        "afternoon"
      ],
      "migratory": true
    },
    {
      "uuid": "a259bc50-aa0e-11f1-a5b5-878489497956",
      "name": "American White Pelican",
      "scientificName": "Pelecanus erythrorhynchos",
      "description": "A large aquatic soaring bird from the order Pelecaniformes. It breeds in interior North America, moving south and to the coasts, as far as Costa Rica, in winter",
      "primaryColor": "white",
      "secondaryColor": [
        "brown"
      ],
      "flightPattern": "linear",
      "size": "large",
      "song": "Rat-tat-tat",
      "area": [
        "US Texas"
      ],
      "time": [
        "morning",
        "afternoon"
      ],
      "migratory": true
    }
  ]
}
```

##### Try finding a non-existing bird.

Request:
* GET `/birds?area=NO_SUCH_PLACE_EXISTS`

Response (200 OK):

```json
{
  "total": 0,
  "limit": 25,
  "offset": 0,
  "data": []
}
```

##### Incorrect observation time

Request:
* GET `/birds?time=supper`

Response (400 Bad Request):

```json
{
  "error": "Parameter 'time' has an incorrect value: 'supper'",
  "statusCode": 400
}
```

---

### Retrieve a Bird by ID

This endpoint retrieves a bird record by the bird's UUID received from the response of the search endpoint.

#### Request Specification

* Method: `GET`
* Path: `/birds/{uuid}`
* Headers: `X-API-Key: <token>`, `Accept: application/json`

#### Response Specification

A single JSON [bird object](#bird-data-model).

#### Status Codes

See also the shared [Error Responses](#error-responses).

| Scenario                                                                                           | Status Code               | Body                                                                     |
| -------------------------------------------------------------------------------------------------- | ------------------------- | ------------------------------------------------------------------------ |
| Successful retrieval                                                                               | 200 OK                    | In accordance with the response specification                            |
| Invalid UUID is specified                                                                          | 400 Bad Request           | {<br>  "error": "Detailed message",<br>  "statusCode": 400<br>}          |
| Incorrect or missing `X-API-Key` header                                                            | 401 Unauthorized          | {<br>  "error": "401 Unauthorized",<br>  "statusCode": 401<br>}          |
| There is no bird with the `uuid` in the catalogue                                                  | 404 Not Found             | {<br>  "error": "404 Not Found",<br>  "statusCode": 404<br>}             |
| The rate limit is exceeded. Check the rate limiting headers in the response for extra information. | 429 Too Many Requests     | {<br>  "error": "429 Too Many Requests",<br>  "statusCode": 429<br>}     |
| The server is not able to process the request due to internal issues                               | 500 Internal Server Error | {<br>  "error": "500 Internal Server Error",<br>  "statusCode": 500<br>} |
| The server is currently unable to handle the request                                               | 503 Service Unavailable   | {<br>  "error": "503 Service Unavailable",<br>  "statusCode": 503<br>}   |

#### Examples

##### Retrieve the Great Egret record

Request:
* GET `/birds/f47ac10b-58cc-4372-a567-0e02b2c3d479`

Response (200 OK):

```json
{
  "uuid": "f47ac10b-58cc-4372-a567-0e02b2c3d479",
  "name": "Great Egret",
  "scientificName": "Ardea alba",
  "description": "The Old World population is often referred to as the 'great white egret'. This species is sometimes confused with the great white heron of the Caribbean, which is a white morph of the closely related great blue heron.",
  "primaryColor": "white",
  "secondaryColor": [],
  "flightPattern": "linear",
  "size": "large",
  "song": "Rat-tat-tat",
  "area": [
    "US Texas"
  ],
  "time": [
    "afternoon"
  ],
  "migratory": true
}
```

---

### Add a New Bird

This endpoint allows you to add a new bird to the catalogue. If there is an existing record with the given common and scientific names, then the request is going to be rejected to avoid adding duplicates.

The `uuid` field is generated by the server. If the request body contains a `uuid` field, the request is rejected with `400 Bad Request`.

#### Request Specification

* Method: `POST`
* Path: `/birds`
* Headers: `X-API-Key: <token>`, `Content-Type: application/json`, `Accept: application/json`
* Body: A JSON object in the format described below.
* Restrictions on parameters' values: in accordance with [Bird Data Model](#bird-data-model).

| Parameter      |        Type        | Required |                                                        Values                                                         |             Example             |                                                                     Description                                                                     |
| -------------- | :----------------: | :------: | :-------------------------------------------------------------------------------------------------------------------: | :-----------------------------: | :-------------------------------------------------------------------------------------------------------------------------------------------------: |
| name           |       string       |   Yes    |                                                        string                                                         |           Bald Eagle            |                                                              The common name of a bird                                                              |
| scientificName |       string       |   Yes    |                                                        string                                                         |    Haliaeetus leucocephalus     |                                                        The scientific latin name of the bird                                                        |
| description    |       string       |   Yes    |                                                        string                                                         | Everyone knows this iconic bird |                A description providing you with the general information about the bird. Includes interesting facts and observations.                |
| primaryColor   |       string       |   Yes    | red, yellow, blue, orange, green, purple, pistachio, teal, indigo, magenta, scarlet, amber, grey, black, white, brown |             magenta             |                                                  The main color of the bird that covers most of it                                                  |
| secondaryColor |  Array of strings  |    No    |                                   An array of strings from the primary color range                                    |      `["white", "orange"]`      |                                                         A list of minor colors of the bird                                                          |
| flightPattern  |       string       |    No    |                                            linear, sine, circle, grounded                                             |             circle              |                                                       The observed flight pattern of the bird                                                       |
| size           |       string       |   Yes    |                                                 small, medium, large                                                  |             medium              | The approximate size of the bird. `small` is for birds less than 4", `medium` for 4" to 12" (inclusive), and `large` for birds greater than 12" |
| song           |       string       |    No    |                                                        string                                                         |           Rat-tat-tat           |                                                                   The bird's song                                                                   |
| area           |  Array of strings  |    No    |                                                  An array of strings                                                  |    `["US Texas", "Mexico"]`     |                                                   A list of locations where the bird is observed                                                    |
| time           |  Array of strings  |    No    |                                An array of values: morning, afternoon, evening, night                                 |   `["morning", "afternoon"]`    |                                                      A list of times when the bird is observed                                                      |
| migratory      | boolean (nullable) |    No    |                                                   true, false, null                                                   |              null               |                              Identifies whether the bird is migratory. `null` means that the migratory status is unknown.                              |

#### Response Specification

A single JSON [bird object](#bird-data-model) of the newly created record, including the server-generated `uuid`.

#### Status Codes

See also the shared [Error Responses](#error-responses).

| Scenario                                                                                                                                                                | Status Code               | Body                                                                                                                     |
| ----------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------- | ------------------------------------------------------------------------------------------------------------------------ |
| A new bird is successfully added                                                                                                                                        | 201 Created               | In accordance with the response specification                                                                            |
| The body is not valid JSON, a required parameter is missing, a parameter has an invalid value, an unknown parameter is present, or the body contains a `uuid` field | 400 Bad Request           | {<br>  "error": "Detailed message",<br>  "statusCode": 400<br>}                                                          |
| Incorrect or missing `X-API-Key` header                                                                                                                                 | 401 Unauthorized          | {<br>  "error": "401 Unauthorized",<br>  "statusCode": 401<br>}                                                          |
| An existing record with the given common and scientific names is found                                                                                                  | 409 Conflict              | {<br>  "error": "Duplicate bird record with the given common and scientific names is found",<br>  "statusCode": 409<br>} |
| The rate limit is exceeded. Check the rate limiting headers in the response for extra information.                                                                      | 429 Too Many Requests     | {<br>  "error": "429 Too Many Requests",<br>  "statusCode": 429<br>}                                                     |
| The server is not able to process the request due to internal issues                                                                                                    | 500 Internal Server Error | {<br>  "error": "500 Internal Server Error",<br>  "statusCode": 500<br>}                                                 |
| The server is currently unable to handle the request                                                                                                                    | 503 Service Unavailable   | {<br>  "error": "503 Service Unavailable",<br>  "statusCode": 503<br>}                                                   |

#### Examples

##### Add the Bald Eagle

Request:
* POST `/birds`

Request body:

```json
{
  "name": "Bald Eagle",
  "scientificName": "Haliaeetus leucocephalus",
  "description": "A bird of prey found in North America. It is the national bird of the United States.",
  "primaryColor": "brown",
  "secondaryColor": [
    "white",
    "yellow"
  ],
  "flightPattern": "circle",
  "size": "large",
  "area": [
    "US Alaska",
    "Canada"
  ],
  "time": [
    "morning",
    "afternoon"
  ],
  "migratory": true
}
```

Response (201 Created):

```json
{
  "uuid": "3f2504e0-4f89-41d3-9a0c-0305e82c3301",
  "name": "Bald Eagle",
  "scientificName": "Haliaeetus leucocephalus",
  "description": "A bird of prey found in North America. It is the national bird of the United States.",
  "primaryColor": "brown",
  "secondaryColor": [
    "white",
    "yellow"
  ],
  "flightPattern": "circle",
  "size": "large",
  "song": null,
  "area": [
    "US Alaska",
    "Canada"
  ],
  "time": [
    "morning",
    "afternoon"
  ],
  "migratory": true
}
```

##### Try adding a duplicate of the Bald Eagle

Request:
* POST `/birds`

Request body:

```json
{
  "name": "Bald Eagle",
  "scientificName": "Haliaeetus leucocephalus",
  "description": "Another description of the same bird.",
  "primaryColor": "brown",
  "size": "large"
}
```

Response (409 Conflict):

```json
{
  "error": "Duplicate bird record with the given common and scientific names is found",
  "statusCode": 409
}
```

##### Try adding a bird with a missing required parameter

Request:
* POST `/birds`

Request body:

```json
{
  "name": "Mystery Bird",
  "primaryColor": "grey",
  "size": "small"
}
```

Response (400 Bad Request):

```json
{
  "error": "Required parameters are missing: 'scientificName', 'description'",
  "statusCode": 400
}
```

---

### Update a Bird

This endpoint allows you to update any field, except `uuid`, of an existing bird. Only the fields present in the request body are modified; all other fields keep their current values. If another record (with a different `uuid`) has the same common and scientific names as the record would have after the update, then the request is rejected with `409 Conflict` to avoid creating duplicates. Resending the record's own `name` and `scientificName` values does not trigger the conflict.

The `uuid` field cannot be changed. If the request body contains a `uuid` field, the request is rejected with `400 Bad Request`.

#### Request Specification

* Method: `PATCH`
* Path: `/birds/{uuid}`
* Headers: `X-API-Key: <token>`, `Content-Type: application/json`, `Accept: application/json`
* Body: A JSON object with some or all fields from the "Add a New Bird" request specification. At least one field must be present.
* Restrictions on parameters' values: in accordance with [Bird Data Model](#bird-data-model).

#### Response Specification

A single JSON [bird object](#bird-data-model) of the updated record.

#### Status Codes

See also the shared [Error Responses](#error-responses).

| Scenario                                                                                                                                                                                  | Status Code               | Body                                                                                                                     |
| ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------- | ------------------------------------------------------------------------------------------------------------------------ |
| The bird is successfully updated                                                                                                                                                          | 200 OK                    | In accordance with the response specification                                                                            |
| Invalid UUID is specified in the path, the body is not valid JSON, the body is empty, a parameter has an invalid value, an unknown parameter is present, or the body contains a `uuid` field | 400 Bad Request           | {<br>  "error": "Detailed message",<br>  "statusCode": 400<br>}                                                          |
| Incorrect or missing `X-API-Key` header                                                                                                                                                   | 401 Unauthorized          | {<br>  "error": "401 Unauthorized",<br>  "statusCode": 401<br>}                                                          |
| There is no bird with the `uuid` in the catalogue                                                                                                                                         | 404 Not Found             | {<br>  "error": "404 Not Found",<br>  "statusCode": 404<br>}                                                             |
| Another record (with a different `uuid`) with the resulting common and scientific names is found                                                                                          | 409 Conflict              | {<br>  "error": "Duplicate bird record with the given common and scientific names is found",<br>  "statusCode": 409<br>} |
| The rate limit is exceeded. Check the rate limiting headers in the response for extra information.                                                                                        | 429 Too Many Requests     | {<br>  "error": "429 Too Many Requests",<br>  "statusCode": 429<br>}                                                     |
| The server is not able to process the request due to internal issues                                                                                                                      | 500 Internal Server Error | {<br>  "error": "500 Internal Server Error",<br>  "statusCode": 500<br>}                                                 |
| The server is currently unable to handle the request                                                                                                                                      | 503 Service Unavailable   | {<br>  "error": "503 Service Unavailable",<br>  "statusCode": 503<br>}                                                   |

#### Examples

##### Update the flight pattern and song of Great Egret.

Request:
* PATCH `/birds/f47ac10b-58cc-4372-a567-0e02b2c3d479`

Request body:

```json
{
  "flightPattern": "sine",
  "song": "Sunny day"
}
```

Response (200 OK):

```json
{
  "uuid": "f47ac10b-58cc-4372-a567-0e02b2c3d479",
  "name": "Great Egret",
  "scientificName": "Ardea alba",
  "description": "The Old World population is often referred to as the 'great white egret'. This species is sometimes confused with the great white heron of the Caribbean, which is a white morph of the closely related great blue heron.",
  "primaryColor": "white",
  "secondaryColor": [],
  "flightPattern": "sine",
  "size": "large",
  "song": "Sunny day",
  "area": [
    "US Texas"
  ],
  "time": [
    "afternoon"
  ],
  "migratory": true
}
```

---

### Delete a Bird

This endpoint removes a bird from the catalogue by the bird's UUID. The operation is irreversible.

#### Request Specification

* Method: `DELETE`
* Path: `/birds/{uuid}`
* Headers: `X-API-Key: <token>`, `Accept: application/json`
* Body: None

#### Response Specification

On success the API returns `204 No Content` with an empty body. Error responses follow the [Error Responses](#error-responses) schema.

#### Status Codes

See also the shared [Error Responses](#error-responses).

| Scenario                                                                                           | Status Code               | Body                                                                     |
| -------------------------------------------------------------------------------------------------- | ------------------------- | ------------------------------------------------------------------------ |
| The bird is successfully removed                                                                   | 204 No Content            | Empty                                                                    |
| Invalid UUID is specified                                                                          | 400 Bad Request           | {<br>  "error": "Detailed message",<br>  "statusCode": 400<br>}          |
| Incorrect or missing `X-API-Key` header                                                            | 401 Unauthorized          | {<br>  "error": "401 Unauthorized",<br>  "statusCode": 401<br>}          |
| There is no bird with the `uuid` in the catalogue                                                  | 404 Not Found             | {<br>  "error": "404 Not Found",<br>  "statusCode": 404<br>}             |
| The rate limit is exceeded. Check the rate limiting headers in the response for extra information. | 429 Too Many Requests     | {<br>  "error": "429 Too Many Requests",<br>  "statusCode": 429<br>}     |
| The server is not able to process the request due to internal issues                               | 500 Internal Server Error | {<br>  "error": "500 Internal Server Error",<br>  "statusCode": 500<br>} |
| The server is currently unable to handle the request                                               | 503 Service Unavailable   | {<br>  "error": "503 Service Unavailable",<br>  "statusCode": 503<br>}   |

#### Examples

##### Delete the Great Egret record

Request:
* DELETE `/birds/f47ac10b-58cc-4372-a567-0e02b2c3d479`

Response (204 No Content): empty body.

##### Try deleting a bird that does not exist

Request:
* DELETE `/birds/00000000-0000-4000-8000-000000000000`

Response (404 Not Found):

```json
{
  "error": "404 Not Found",
  "statusCode": 404
}
```
