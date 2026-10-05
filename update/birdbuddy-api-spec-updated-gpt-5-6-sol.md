Prepared by gpt-5.6-sol.

# BirdBuddy API

## Overview

The BirdBuddy API enables developers and organizations to catalogue, identify, retrieve, update, and delete records for existing and extinct birds. The API uses resource-oriented HTTP endpoints and JSON representations.

The API is used by the BirdBuddy service deployed to Global Cloud and provides a target availability of 99.999%. Current availability is published in the [availability statistics](https://www.birdbuddy.org/sla). A network of regional data centers minimizes latency. Personal and commercial use is permitted, subject to the applicable service terms, authentication requirements, and rate limits.

BirdBuddy source projects are published through the [BirdBuddy GitHub organization](https://www.github.com/birdbuddy), which contains the available project repositories and contribution information.

## Environments and Base URLs

A client selects an API major version by using its corresponding base URL. Paths in this document are relative to that base URL.

### QA

QA runs the latest release candidate for each listed major version. QA data is purged at 00:00 UTC on the first day of each month. Releases are deployed as needed. Planned-deployment notices are published through the [BirdBuddy service-status page](https://www.birdbuddy.org/sla) at least 24 hours before deployment; emergency maintenance may occur without advance notice.

- v2: `https://www.qa.birdbuddy.org/v2`
- v3: `https://www.qa.birdbuddy.org/v3`

QA API keys are valid only in QA.

### Production

- v2: `https://www.birdbuddy.org/v2`
- v3: `https://www.birdbuddy.org/v3`

Production API keys are valid only in Production.

## Authentication and Authorization

Every endpoint in this specification is protected by an API key.

### Sending an API key

Send the key in the following required request header:

```http
X-API-Key: <token>
```

API keys are issued and managed through the BirdBuddy developer account associated with the client application. A key is scoped to one environment and has one or both of the following permissions:

- `birds:read` — permits Search and Retrieve operations.
- `birds:write` — permits Add, Update, and Delete operations. Write keys also include `birds:read` so the resulting resource can be returned.

Do not place keys in URLs, logs, or client-side public source code. Keys can be rotated by creating a replacement key, deploying it, and then revoking the previous key in the developer account. Revocation takes effect immediately. QA and Production keys are not interchangeable.

Authentication and authorization failures have these meanings:

- `401 Unauthorized` — the `X-API-Key` header is missing, malformed, revoked, expired, or not valid for the selected environment.
- `403 Forbidden` — the key is valid but lacks the permission required for the operation.

## Common HTTP Conventions

### Media types

Requests with a JSON body must send `Content-Type: application/json`. Clients request JSON responses with `Accept: application/json`. Every non-empty success or error response in this specification uses `Content-Type: application/json; charset=utf-8`. A `204 No Content` response has no body and no response `Content-Type` header.

### Error object

All JSON error responses use this schema:

| Field | Type | Required | Description |
|---|---|:---:|---|
| `error` | string | Yes | Human-readable summary or validation message. |
| `statusCode` | integer | Yes | HTTP status code, represented as a JSON number. |
| `details` | array of strings | No | Field-specific or request-specific validation details. |
| `requestId` | string | No | Correlation identifier for support and diagnostics. |

Example:

```json
{
  "error": "Parameter 'time' has an incorrect value: 'supper'",
  "statusCode": 400,
  "details": [
    "time must be one of: morning, afternoon, evening, night"
  ],
  "requestId": "req-01HTY8ZQ6F7M9K2C3D4E5F6G7H"
}
```

### UUID path parameters

Every `{uuid}` path parameter is required and must be a canonical UUID v4 string, such as `f47ac10b-58cc-4372-a567-0e02b2c3d479`. A malformed value or a UUID of another version produces `400 Bad Request`. A valid UUID v4 that does not identify a record produces `404 Not Found`.

## Rate Limiting

Limits are applied independently per API key and per endpoint. The fixed window is measured in seconds.

| Endpoint | Limit | Window Duration (seconds) |
|---|:---:|:---:|
| Search for a Bird | 60 | 60 |
| Retrieve a Bird by ID | 60 | 60 |
| Add a New Bird | 10 | 60 |
| Update a Bird | 10 | 60 |
| Delete a Bird | 60 | 60 |

The following response headers are returned for rate-limited endpoints:

| Header | Type and unit | Semantics |
|---|---|---|
| `RateLimit-Limit` | integer requests | Maximum requests allowed in the current window for the API key and endpoint. |
| `RateLimit-Remaining` | integer requests | Requests remaining in the current window; never less than `0`. |
| `RateLimit-Reset` | integer seconds | Seconds until the current window resets. |
| `Retry-After` | integer seconds | Returned with `429 Too Many Requests`; minimum delay before the client should retry. This API uses delay-seconds, not an HTTP date. |

When the limit is exceeded, the API returns `429 Too Many Requests` and an Error object. Clients must honor `Retry-After`. For retryable failures that do not include `Retry-After`, clients should use exponential backoff with jitter and a bounded retry count.

## Bird Data Model

A Bird object has the following canonical JSON representation. All listed fields are present in success responses. `uuid` is generated by the service and is read-only.

The color enum used by `primaryColor` and `secondaryColor` is: `red`, `yellow`, `blue`, `orange`, `green`, `purple`, `pistachio`, `teal`, `indigo`, `magenta`, `scarlet`, `amber`, `grey`, `black`, `white`, and `brown`.

| Parameter | Type | Values / restrictions | Example | Description |
|---|---|---|---|---|
| `uuid` | string | Canonical UUID v4; generated by the service; immutable | `f47ac10b-58cc-4372-a567-0e02b2c3d479` | Unique bird identifier. |
| `name` | string | 1–100 characters after trimming | `Bald Eagle` | Common name of the bird. |
| `scientificName` | string | 1–200 characters after trimming | `Haliaeetus leucocephalus` | Scientific Latin name of the bird. |
| `description` | string | 1–5000 characters after trimming | `An iconic American bird.` | General information, facts, and observations about the bird. |
| `primaryColor` | string | Exactly one value from the color enum | `white` | Main color covering most of the bird. |
| `secondaryColor` | array of strings | 0–10 distinct color-enum values; a value cannot equal `primaryColor` | `["white", "orange"]` | Minor colors of the bird. |
| `flightPattern` | string or null | One of `linear`, `sine`, `circle`, `grounded`, or `null` | `circle` | Observed flight pattern; `null` means unknown or not recorded. |
| `size` | string | Exactly one of `small`, `medium`, or `large` | `large` | Approximate length category in inches: `small` is less than 4 inches; `medium` is at least 4 but less than 12 inches; `large` is at least 12 inches. |
| `song` | string or null | `null` or a string of 1–200 characters after trimming | `Rat-tat-tat` | Bird song; `null` means unknown or not recorded. |
| `area` | array of strings | 0–50 distinct location names; each is 1–200 characters after trimming | `["US Texas", "Mexico"]` | Locations where the bird is observed. |
| `time` | array of strings | 0–4 distinct values from `morning`, `afternoon`, `evening`, and `night` | `["morning", "afternoon"]` | Times when the bird is observed. |
| `migratory` | boolean or null | `true`, `false`, or `null` | `true` | Whether the bird is migratory; `null` means unknown or not recorded. |

For create requests, omission of an optional array sets it to `[]`; omission of an optional nullable scalar sets it to `null`. In responses, an unknown scalar is represented by `null`, not by omission. `false` explicitly means that the bird is not migratory and is different from `null`.

## Duplicate Detection and Text Normalization

A duplicate is another record whose normalized `name` **and** normalized `scientificName` both match. The pair is the uniqueness key; a match on only one name is not a duplicate.

For duplicate detection and exact text search, the service percent-decodes query values, normalizes text to Unicode NFC, trims leading and trailing whitespace, collapses each internal whitespace run to one space, and compares using Unicode case folding. Stored display text retains its submitted capitalization after trimming and whitespace collapsing. Duplicate checks are atomic. During Update, the record being updated is excluded from its own uniqueness check.

## Endpoints

### Search for a Bird

Returns a cursor-paginated list of birds satisfying the search criteria.

#### Request specification

- Method: `GET`
- Path: `/birds`
- Required headers: `X-API-Key: <token>`, `Accept: application/json`
- Required permission: `birds:read`
- Restrictions on query parameter values: in accordance with the [Bird Data Model](#bird-data-model).

| Query parameter | Type | Required | Values / default | Example | Description |
|---|---|:---:|---|---|---|
| `name` | string | No | String | `name=Bald%20Eagle` | Exact normalized common-name match. |
| `scientificName` | string | No | String | `scientificName=Strix%20nebulosa` | Exact normalized scientific-name match. |
| `size` | string | No | `small`, `medium`, `large` | `size=small` | Size category. |
| `primaryColor` | string | No | Color enum | `primaryColor=indigo` | Main color. |
| `secondaryColor` | repeated string | No | Color enum | `secondaryColor=teal&secondaryColor=blue` | Required minor colors. Duplicate values are ignored. |
| `song` | string | No | String | `song=Rat-tat-tat` | Exact normalized song match. |
| `flightPattern` | string | No | `linear`, `sine`, `circle`, `grounded` | `flightPattern=sine` | Flight pattern. |
| `area` | repeated string | No | String | `area=Mexico&area=New%20York` | Location terms. Duplicate normalized values are ignored. |
| `time` | repeated string | No | `morning`, `afternoon`, `evening`, `night` | `time=morning&time=night` | Observation times. Duplicate values are ignored. |
| `limit` | integer | No | Minimum `1`, maximum `100`, default `25` | `limit=50` | Maximum records in this page. |
| `cursor` | string | No | Opaque cursor returned by the preceding page | `cursor=eyJzbmFwc2hvdCI6IjEyMyJ9` | Continues a search result snapshot. |

A scalar query parameter (`name`, `scientificName`, `size`, `primaryColor`, `song`, `flightPattern`, `limit`, or `cursor`) must not be repeated. Repetition, a decimal or nonnumeric `limit`, an unknown query parameter, or any invalid value produces `400 Bad Request`.

Search logic is deterministic:

- Different parameter names are combined with AND.
- Repeated `secondaryColor` values are combined with AND: every supplied color must occur in the bird's `secondaryColor` array.
- Repeated `time` values are combined with OR: at least one supplied time must occur in the bird's `time` array.
- Repeated `area` values are combined with OR.
- An `area` term matches a stored location when the normalized term is a contiguous substring of the normalized location. Area normalization uses Unicode NFC, trimming, whitespace collapsing, and Unicode case folding. No probabilistic ranking is used.
- Exact matches for `name`, `scientificName`, and `song` use the normalization rules in [Duplicate Detection and Text Normalization](#duplicate-detection-and-text-normalization).

Results are ordered by normalized `name` ascending and then by `uuid` ascending. The first request creates a result snapshot retained for 15 minutes. `cursor` is opaque, pins the same snapshot and filters, and must not be modified. When using a cursor, all filters and `limit` must be identical to the first request. An invalid, expired, or mismatched cursor produces `400 Bad Request`.

#### Response specification

The top-level response is a JSON object with four attributes:

- `total` — integer count of all matching records in the snapshot, before `limit` is applied.
- `count` — integer count of records in this page; always equal to `data.length`.
- `data` — array of Bird objects.
- `nextCursor` — opaque string for the next page, or `null` when no later page exists.

```json
{
  "total": 0,
  "count": 0,
  "data": [],
  "nextCursor": null
}
```

#### Status codes

| Scenario | Status Code | Body |
|---|---|---|
| Search completed with zero or more results | `200 OK` | Search response object |
| Query value, repetition, or cursor is invalid | `400 Bad Request` | Error object |
| API key is missing or invalid | `401 Unauthorized` | Error object |
| API key lacks `birds:read` | `403 Forbidden` | Error object |
| Rate limit is exceeded | `429 Too Many Requests` | Error object; see rate-limit response headers |
| Internal processing failure | `500 Internal Server Error` | Error object |
| Service is temporarily unable to handle the request | `503 Service Unavailable` | Error object |

#### Examples

##### Find all large white birds with the song “Rat-tat-tat”

```http
GET /birds?size=large&primaryColor=white&song=Rat-tat-tat
Accept: application/json
X-API-Key: <token>
```

`HTTP 200 OK`

```json
{
  "total": 2,
  "count": 2,
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
      "area": ["US Texas"],
      "time": ["afternoon"],
      "migratory": true
    },
    {
      "uuid": "a259bc50-aa0e-41f1-a5b5-878489497956",
      "name": "American White Pelican",
      "scientificName": "Pelecanus erythrorhynchos",
      "description": "A large aquatic soaring bird from the order Pelecaniformes. It breeds in interior North America, moving south and to the coasts as far as Costa Rica in winter.",
      "primaryColor": "white",
      "secondaryColor": ["brown"],
      "flightPattern": "linear",
      "size": "large",
      "song": "Rat-tat-tat",
      "area": ["US Texas"],
      "time": ["morning", "afternoon"],
      "migratory": true
    }
  ],
  "nextCursor": null
}
```

##### Find a bird in a non-existing area

```http
GET /birds?area=NO_SUCH_PLACE_EXISTS
Accept: application/json
X-API-Key: <token>
```

`HTTP 200 OK`

```json
{
  "total": 0,
  "count": 0,
  "data": [],
  "nextCursor": null
}
```

##### Reject an incorrect observation time

```http
GET /birds?time=supper
Accept: application/json
X-API-Key: <token>
```

`HTTP 400 Bad Request`

```json
{
  "error": "Parameter 'time' has an incorrect value: 'supper'",
  "statusCode": 400,
  "details": [
    "time must be one of: morning, afternoon, evening, night"
  ]
}
```

---

### Retrieve a Bird by ID

This endpoint retrieves a bird record by the bird's UUID.

#### Request specification

- Method: `GET`
- Path: `/birds/{uuid}`
- Path parameter: `uuid` — required UUID v4
- Required headers: `X-API-Key: <token>`, `Accept: application/json`
- Required permission: `birds:read`

#### Response specification

A single Bird object.

#### Status codes

| Scenario | Status Code | Body |
|---|---|---|
| Bird successfully retrieved | `200 OK` | Bird object |
| UUID is malformed or is not UUID v4 | `400 Bad Request` | Error object |
| API key is missing or invalid | `401 Unauthorized` | Error object |
| API key lacks `birds:read` | `403 Forbidden` | Error object |
| No bird has the specified `uuid` | `404 Not Found` | Error object |
| Rate limit is exceeded | `429 Too Many Requests` | Error object; see rate-limit response headers |
| Internal processing failure | `500 Internal Server Error` | Error object |
| Service is temporarily unable to handle the request | `503 Service Unavailable` | Error object |

#### Example

```http
GET /birds/f47ac10b-58cc-4372-a567-0e02b2c3d479
Accept: application/json
X-API-Key: <token>
```

`HTTP 200 OK`

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
  "area": ["US Texas"],
  "time": ["afternoon"],
  "migratory": true
}
```

---

### Add a New Bird

This endpoint adds a bird to the catalogue. The service rejects a request when another record has the same normalized common-name and scientific-name pair.

#### Request specification

- Method: `POST`
- Path: `/birds`
- Required headers: `X-API-Key: <token>`, `Content-Type: application/json`, `Accept: application/json`
- Required permission: `birds:write`
- Body: A JSON object in the format below.
- Value restrictions: in accordance with the [Bird Data Model](#bird-data-model).

| Parameter | Type | Required | Values / behavior |
|---|---|:---:|---|
| `name` | string | Yes | 1–100 characters |
| `scientificName` | string | Yes | 1–200 characters |
| `description` | string | Yes | 1–5000 characters |
| `primaryColor` | string | Yes | Color enum |
| `secondaryColor` | array of strings | No | Defaults to `[]`; maximum 10 distinct color-enum values |
| `flightPattern` | string or null | No | Enum or `null`; defaults to `null` |
| `size` | string | Yes | `small`, `medium`, or `large` |
| `song` | string or null | No | 1–200 characters or `null`; defaults to `null` |
| `area` | array of strings | No | Defaults to `[]`; maximum 50 distinct values |
| `time` | array of strings | No | Defaults to `[]`; values from the time enum |
| `migratory` | boolean or null | No | Defaults to `null`; `false` is a known non-migratory value |

`uuid` must not be supplied; the service generates it. Unknown body properties produce `400 Bad Request`.

#### Response specification

A successful response contains the created Bird object. The `Location` response header contains the relative resource URI, `/birds/{uuid}`.

#### Status codes

| Scenario | Status Code | Body |
|---|---|---|
| Bird successfully added | `201 Created` | Created Bird object; includes `Location` header |
| Body or parameter is invalid | `400 Bad Request` | Error object |
| API key is missing or invalid | `401 Unauthorized` | Error object |
| API key lacks `birds:write` | `403 Forbidden` | Error object |
| Another record has the same normalized name pair | `409 Conflict` | Error object |
| Rate limit is exceeded | `429 Too Many Requests` | Error object; see rate-limit response headers |
| Internal processing failure | `500 Internal Server Error` | Error object |
| Service is temporarily unable to handle the request | `503 Service Unavailable` | Error object |

#### Examples

##### Create a bird

```http
POST /birds
Content-Type: application/json
Accept: application/json
X-API-Key: <token>
```

```json
{
  "name": "Great Horned Owl",
  "scientificName": "Bubo virginianus",
  "description": "A large owl native to the Americas.",
  "primaryColor": "brown",
  "secondaryColor": ["grey", "white"],
  "flightPattern": "linear",
  "size": "large",
  "song": "Hoo-h'HOO-hoo-hoo",
  "area": ["North America"],
  "time": ["evening", "night"],
  "migratory": false
}
```

`HTTP 201 Created`

```http
Location: /birds/3b241101-e2bb-4255-8caf-4136c566a962
Content-Type: application/json; charset=utf-8
```

```json
{
  "uuid": "3b241101-e2bb-4255-8caf-4136c566a962",
  "name": "Great Horned Owl",
  "scientificName": "Bubo virginianus",
  "description": "A large owl native to the Americas.",
  "primaryColor": "brown",
  "secondaryColor": ["grey", "white"],
  "flightPattern": "linear",
  "size": "large",
  "song": "Hoo-h'HOO-hoo-hoo",
  "area": ["North America"],
  "time": ["evening", "night"],
  "migratory": false
}
```

##### Reject a duplicate

A second request whose normalized pair is `great horned owl` and `bubo virginianus` returns `HTTP 409 Conflict`:

```json
{
  "error": "A bird with the given common and scientific names already exists",
  "statusCode": 409
}
```

---

### Update a Bird

This endpoint updates one or more mutable fields of an existing bird. The `uuid` field cannot be updated.

#### Request specification

- Method: `PATCH`
- Path: `/birds/{uuid}`
- Path parameter: `uuid` — required UUID v4
- Required headers: `X-API-Key: <token>`, `Content-Type: application/json`, `Accept: application/json`
- Required permission: `birds:write`
- Body: A partial JSON object containing one or more mutable fields from the Add request.
- Value restrictions: in accordance with the [Bird Data Model](#bird-data-model).

#### Partial-update semantics

- Omitted properties remain unchanged.
- Supplied arrays replace their stored arrays; arrays are not merged.
- `null` is accepted only for `flightPattern`, `song`, and `migratory`, where it clears the known value to “unknown or not recorded.”
- `null` is invalid for `name`, `scientificName`, `description`, `primaryColor`, `secondaryColor`, `size`, `area`, and `time`.
- Create-time required flags do not require those properties to appear in a PATCH, but every supplied value must satisfy the canonical model.
- `{}` is invalid because it requests no change.
- `uuid` and unknown properties are invalid.
- If `primaryColor` or `secondaryColor` is changed, the resulting complete resource must still satisfy the rule that the primary color does not occur among secondary colors.
- The uniqueness check excludes the target record and returns `409 Conflict` only if another record has the same normalized `name` and `scientificName` pair.

#### Response specification

A single Bird object representing the complete updated record.

#### Status codes

| Scenario | Status Code | Body |
|---|---|---|
| Bird successfully updated | `200 OK` | Updated Bird object |
| UUID, body, property, or value is invalid | `400 Bad Request` | Error object |
| API key is missing or invalid | `401 Unauthorized` | Error object |
| API key lacks `birds:write` | `403 Forbidden` | Error object |
| No bird has the specified `uuid` | `404 Not Found` | Error object |
| Another record has the resulting normalized name pair | `409 Conflict` | Error object |
| Rate limit is exceeded | `429 Too Many Requests` | Error object; see rate-limit response headers |
| Internal processing failure | `500 Internal Server Error` | Error object |
| Service is temporarily unable to handle the request | `503 Service Unavailable` | Error object |

#### Example

##### Update the flight pattern and song of the Great Egret

```http
PATCH /birds/f47ac10b-58cc-4372-a567-0e02b2c3d479
Content-Type: application/json
Accept: application/json
X-API-Key: <token>
```

```json
{
  "flightPattern": "sine",
  "song": "Sunny day"
}
```

`HTTP 200 OK`

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
  "area": ["US Texas"],
  "time": ["afternoon"],
  "migratory": true
}
```

---

### Delete a Bird

This endpoint permanently removes an existing bird. DELETE is idempotent with respect to resource state: after a successful deletion the resource remains absent. The first successful request returns `204 No Content`; a later request for the same UUID returns `404 Not Found` because no record remains.

#### Request specification

- Method: `DELETE`
- Path: `/birds/{uuid}`
- Path parameter: `uuid` — required UUID v4
- Required headers: `X-API-Key: <token>`, `Accept: application/json`
- Required permission: `birds:write`
- Request body: none

#### Status codes

| Scenario | Status Code | Body |
|---|---|---|
| Bird successfully removed | `204 No Content` | Empty |
| UUID is malformed or is not UUID v4 | `400 Bad Request` | Error object |
| API key is missing or invalid | `401 Unauthorized` | Error object |
| API key lacks `birds:write` | `403 Forbidden` | Error object |
| No bird has the specified `uuid` | `404 Not Found` | Error object |
| Rate limit is exceeded | `429 Too Many Requests` | Error object; see rate-limit response headers |
| Internal processing failure | `500 Internal Server Error` | Error object |
| Service is temporarily unable to handle the request | `503 Service Unavailable` | Error object |

#### Example

```http
DELETE /birds/f47ac10b-58cc-4372-a567-0e02b2c3d479
Accept: application/json
X-API-Key: <token>
```

`HTTP 204 No Content`
