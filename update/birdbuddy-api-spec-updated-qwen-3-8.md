Prepared by qwen-3.8.

# BirdBuddy API

## Overview

The BirdBuddy API is an essential tool for any developer or company aiming at cataloging and identifying birds. The community supported catalogue of existing and extinct birds is the biggest in the world. The API fully supports REST architecture.

The API is used in the BirdBuddy service deployed to Global Cloud and provides 99.999% uptime. You can find the current availability statistics at https://www.birdbuddy.org/sla. A network of regional datacenters minimizes latency. The service has no restrictions on personal or commercial use.

The API's source is published to [GitHub](https://www.github.com/birdbuddy) and has a vibrant and active community.

## Environments and Base URLs

### QA

The QA environment hosts the latest release candidate. The data is purged on the 1st of each month. A new version is deployed when necessary with a 24-hour notice.

* v3: https://www.qa.birdbuddy.org/v3
* v2: https://www.qa.birdbuddy.org/v2

### Production

* v3: https://www.birdbuddy.org/v3
* v2: https://www.birdbuddy.org/v2

## Rate Limiting

The rate limit for each endpoint is set per API token (see the [Authentication and Authorization](#authentication-and-authorization) section).

| Endpoint              | Limit | Window Duration (sec) |
| --------------------- | :---: | :-------------------: |
| Search for a Bird     |  60   |          60           |
| Retrieve a Bird by ID |  60   |          60           |
| Add a New Bird        |  10   |          60           |
| Update a Bird         |  10   |          60           |
| Delete a Bird         |  60   |          60           |

Every response includes the following rate-limiting headers that describe the token's current limits:

* `X-RateLimit-Limit` — the maximum number of requests allowed for the endpoint within the current window.
* `X-RateLimit-Remaining` — the number of requests remaining for the endpoint within the current window.
* `X-RateLimit-Reset` — the number of seconds until the current window resets.

When a client exceeds the current limit, the API returns `429 Too Many Requests` with the `Retry-After` header, which specifies the cooldown period in seconds.

It's recommended to implement exponential backoff to avoid exceeding the rate limit.

## Authentication and Authorization

All API requests are authenticated with a personal API token, which must be sent in the `X-API-Key` header of every request:

```
X-API-Key: <API token>
```

* Tokens are issued to individual developer accounts and can be created and managed in the BirdBuddy developer portal at https://www.birdbuddy.org/developers.
* Requests with an incorrect, expired, or missing `X-API-Key` header are rejected with `401 Unauthorized`.
* The rate limits described in the [Rate Limiting](#rate-limiting) section are enforced per API token.

## Bird Data Model

| Parameter      |       Type       |                                                        Values                                                         | Restrictions                  |               Example                |                                                         Description                                                         |
| -------------- | :--------------: | :-------------------------------------------------------------------------------------------------------------------: | ----------------------------- | :----------------------------------: | :-------------------------------------------------------------------------------------------------------------------------: |
| uuid           |      string      |                                                        UUID v4                                                        | UUID v4 format                | f47ac10b-58cc-4372-a567-0e02b2c3d479 |             A unique UUID v4 identifier of the bird. It's generated for every new bird added to the catalogue.              |
| name           |      string      |                                                       A string                                                        | Min 1 and max 100 characters  |              Bald Eagle              |                                                  The common name of a bird                                                  |
| scientificName |      string      |                                                       A string                                                        | Min 1 and max 200 characters  |       Haliaeetus leucocephalus       |                                            The scientific latin name of the bird                                            |
| description    |      string      |                                                       A string                                                        | Min 1 and max 5000 characters |    An iconic American alpha bird.    |    A description providing you with the general Information about the bird. Includes interesting facts and observations.    |
| primaryColor   |      string      | red, yellow, blue, orange, green, purple, pistachio, teal, indigo, magenta, scarlet, amber, grey, black, white, brown | Only 1 value                  |               magenta                |                                      The main color of the bird that covers most of it                                      |
| secondaryColor | Array of strings |                                                                                    | Maximum 10 values             |        `["white", "orange"]`         |                                             A list of minor colors of the bird                                              |
| flightPattern  |      string      |                                            linear, sine, circle, grounded                                             | Only 1 value                  |                circle                |                                           The observed flight pattern of the bird                                           |
| size           |      string      |                                                 small, medium, large                                                  | Only 1 value                  |                large                | The approximate size of the bird. `small` is for birds less than 4", `medium` for 4 - 12", and `large` for 12 inches and above |
| song           |      string      |                                                       A string                                                        | Min 1 and max 200 characters  |             Rat-tat-tat              |                                                       The bird's song                                                       |
| area           | Array of strings |                                                  Names of locations                                                   | Maximum 50 values             |       `["US Texas", "Mexico"]`       |                                       A list of locations where the bird  is observed                                       |
| time           | Array of strings |                                       morning, afternoon, evening, night                                           | Maximum 4 distinct values     |      `["morning", "afternoon"]`      |                                          A list of times when the bird is observed                                          |
| migratory      |     boolean      |                                                   true, false, null                                                   | Only 1 value                  |                 true                 |                                          Identifies whether the bird is migratory. `null` means the value is not specified.                                           |

## Endpoints

### Search for a Bird

Returns a list of birds satisfying the given search criteria.

The search applies the AND logic for all parameters except `area`.

#### Request specification

* Method: `GET`
* Path: `/birds`
* Headers: `Accept: application/json`
* Restrictions on the query parameter values: in accordance with the [Bird Data Model](#bird-data-model).

| Query parameter |       Type       |     Required      |                                                        Values                                                         |                 Example                 |                                                                                                       Description                                                                                                       |
| --------------- | :--------------: | :---------------: | :-------------------------------------------------------------------------------------------------------------------: | :-------------------------------------: | :---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------: |
| name            |      string      |        No         |                                                       A string                                                        |            name=Bald%20Eagle            |                                                                                   The common name of a bird. Exact match is applied.                                                                                    |
| scientificName  |      string      |        No         |                                                       A string                                                        |     scientificName=Strix%20nebulosa     |                                                                             The scientific latin name of the bird. Exact match is applied.                                                                              |
| size            |      string      |        No         |                                                 small, medium, large                                                  |               size=small                |                                                The approximate size of the bird. `small` is for birds less than 4", `medium` for 4 - 12", and `large` for 12 inches and above                                                 |
| primaryColor    |      string      |        No         | red, yellow, blue, orange, green, purple, pistachio, teal, indigo, magenta, scarlet, amber, grey, black, white, brown |           primaryColor=indigo           |                                                                                    The main color of the bird that covers most of it                                                                                    |
| secondaryColor  | Array of strings |        No         | red, yellow, blue, orange, green, purple, pistachio, teal, indigo, magenta, scarlet, amber, grey, black, white, brown | secondaryColor=teal&secondaryColor=blue |                                                 A list of minor colors of the bird. Repeated keys can be used to specify multiple values. Duplicate values are ignored.                                                 |
| song            |      string      |        No         |                                                        String                                                         |            song=Rat-tat-tat             |                                                                                                     The bird's song                                                                                                     |
| flightPattern   |      string      |        No         |                                            linear, sine, circle, grounded                                             |           flightPattern=sine            |                                                                                         The observed flight pattern of the bird                                                                                         |
| area            | Array of strings |        No         |                                                        String                                                         |  area=Mexico%20Cozumel&area=New%20York  | A list of locations where the bird  is observed. The API uses keyword search to identify most probable match. Repeated keys can be used to specify multiple values. Duplicate values are ignored. The OR logic is used. |
| time            | Array of strings |        No         |                                       morning, afternoon, evening, night                                           |         time=morning&time=night         |                                             A list of times when the bird is observed. Repeated keys can be used to specify multiple values. Duplicate values are ignored.                                              |
| limit           |      number      | No (default = 25) |                                                        1 - 100                                                        |                limit=50                 |                                                                                      The maximum number of records to be returned.                                                                                      |

#### Response Specification

The top-level response is a JSON object with 2 attributes:
* `total` - the total number of matching records found in the catalogue.
* `data` - an array of JSON [bird objects](#bird-data-model) matching the search criteria (up to the `limit` value).

Sample response (bird objects omitted):

```json
{
  "total": 3,
  "data": []
}
```

#### Status Codes

| Scenario                                                                                           | Status Code               | Body                                                                     |
| -------------------------------------------------------------------------------------------------- | ------------------------- | ------------------------------------------------------------------------ |
| Successful search with 0 or more results                                                           | 200 OK                    | In accordance to the response specification                              |
| One of the query parameters has an incorrect value                                                 | 400 Bad Request           | {<br>  "error": "Detailed message",<br>  "statusCode": 400<br>}          |
| Incorrect or missing `X-API-Key` header                                                            | 401 Unauthorized          | {<br>  "error": "401 Unauthorized",<br>  "statusCode": 401<br>}          |
| The rate limit is exceeded. Check the rate limiting headers in the response for extra information. | 429 Too Many Requests     | {<br>  "error": "429 Too Many Requests",<br>  "statusCode": 429<br>}     |
| The server is not able to process the request due to internal issues                               | 500 Internal Server Error | {<br>  "error": "500 Internal Server Error",<br>  "statusCode": 500<br>} |
| The server is currently unable to handle the request                                               | 503 Service Unavailable   | {<br>  "error": "503 Service Unavailable",<br>  "statusCode": 503<br>}   |

#### Examples

##### Find all large birds with the primary color white and singing "Rat-tat-tat".

Request:
* GET `/birds?size=large&primaryColor=white&song=Rat-tat-tat`

Response:

```json
{
  "total": 2,
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

Response:

```json
{
  "total": 0,
  "data": []
}
```

##### Incorrect observation time:

Request:
* GET `/birds?time=supper`

Response:

```json
{
  "error": "Parameter 'time' has an incorrect value: 'supper'",
  "statusCode": 400
}
```

### Retrieve a Bird by ID

This endpoint retrieves a bird record by the bird's UUID received from the response of the search endpoint.

#### Request Specification

* Method: `GET`
* Path: `/birds/${UUID}`
* Headers: `Accept: application/json`

#### Response Specification

A single JSON [bird object](#bird-data-model).

#### Status Codes

| Scenario                                                                                           | Status Code               | Body                                                                     |
| -------------------------------------------------------------------------------------------------- | ------------------------- | ------------------------------------------------------------------------ |
| Successful retrieval                                                                               | 200 OK                    | In accordance to the response specification                              |
| Invalid UUID is specified                                                                          | 400 Bad Request           | {<br>  "error": "400 Bad Request",<br>  "statusCode": 400<br>}           |
| Incorrect or missing `X-API-Key` header                                                            | 401 Unauthorized          | {<br>  "error": "401 Unauthorized",<br>  "statusCode": 401<br>}          |
| There is no bird with the `uuid` in the catalogue                                                  | 404 Not Found             | {<br>  "error": "404 Not Found",<br>  "statusCode": 404<br>}             |
| The rate limit is exceeded. Check the rate limiting headers in the response for extra information. | 429 Too Many Requests     | {<br>  "error": "429 Too Many Requests",<br>  "statusCode": 429<br>}     |
| The server is not able to process the request due to internal issues                               | 500 Internal Server Error | {<br>  "error": "500 Internal Server Error",<br>  "statusCode": 500<br>} |
| The server is currently unable to handle the request                                               | 503 Service Unavailable   | {<br>  "error": "503 Service Unavailable",<br>  "statusCode": 503<br>}   |

#### Examples

##### Retrieve the Great Egret record:

Request:
* GET `/birds/f47ac10b-58cc-4372-a567-0e02b2c3d479`

Response:

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

### Add a New Bird

This endpoint allows you to add a new bird to the catalogue. If there is an existing record with the given common and scientific names, then the request is going to be rejected to avoid adding duplicates.

#### Request Specification

* Method: `POST`
* Path: `/birds`
* Headers: `Content-Type: application/json`, `Accept: application/json`
* Body: A JSON object in the format described below.
* Restrictions on parameters' values: in accordance with the [Bird Data Model](#bird-data-model).

| Parameter      |       Type       | Required |                                                        Values                                                         |             Example             |                                                         Description                                                         |
| -------------- | :--------------: | :------: | :-------------------------------------------------------------------------------------------------------------------: | :-----------------------------: | :-------------------------------------------------------------------------------------------------------------------------: |
| name           |      string      |   Yes    |                                                        string                                                         |           Bald Eagle            |                                                  The common name of a bird                                                  |
| scientificName |      string      |   Yes    |                                                        string                                                         |    Haliaeetus leucocephalus     |                                            The scientific latin name of the bird                                            |
| description    |      string      |   Yes    |                                                        string                                                         | Everyone knows this iconic bird |    A description providing you with the general information about the bird. Includes interesting facts and observations.    |
| primaryColor   |      string      |   Yes    | red, yellow, blue, orange, green, purple, pistachio, teal, indigo, magenta, scarlet, amber, grey, black, white, brown |             magenta             |                                      The main color of the bird that covers most of it                                      |
| secondaryColor | Array of strings |    No    |                                   An array of strings from the primary color range                                    |      `["white", "orange"]`      |                                             A list of minor colors of the bird                                              |
| flightPattern  |      string      |    No    |                                            linear, sine, circle, grounded                                             |             circle              |                                           The observed flight pattern of the bird                                           |
| size           |      string      |   Yes    |                                                 small, medium, large                                                  |             medium              | The approximate size of the bird. `small` is for birds less than 4", `medium` for 4 - 12", and `large` for 12 inches and above |
| song           |      string      |    No    |                                                        string                                                         |           Rat-tat-tat           |                                                       The bird's song                                                       |
| area           | Array of strings |    No    |                                                  An array of strings                                                  |    `["US Texas", "Mexico"]`     |                                       A list of locations where the bird  is observed                                       |
| time           | Array of strings |    No    |                                An array of values: morning, afternoon, evening, night                                 |   `["morning", "afternoon"]`    |                                          A list of times when the bird is observed                                          |
| migratory      |    boolean       |    No    |                                                   true, false, null                                                   |              null               |                                          Identifies whether the bird is migratory                                           |

#### Response Specification

A single JSON [bird object](#bird-data-model).

#### Status Codes

| Scenario                                                                                           | Status Code               | Body                                                                                                                     |
| -------------------------------------------------------------------------------------------------- | ------------------------- | ------------------------------------------------------------------------------------------------------------------------ |
| A new bird is successfully added                                                                   | 201 Created               | In accordance to the response specification                                                                              |
| One of the parameters is invalid                                                                   | 400 Bad Request           | {<br>  "error": "Detailed message",<br>  "statusCode": 400<br>}                                                          |
| Incorrect or missing `X-API-Key` header                                                            | 401 Unauthorized          | {<br>  "error": "401 Unauthorized",<br>  "statusCode": 401<br>}                                                          |
| An existing record with the given common and scientific names is found                             | 409 Conflict              | {<br>  "error": "Duplicate bird record with the given common and scientific names is found",<br>  "statusCode": 409<br>} |
| The rate limit is exceeded. Check the rate limiting headers in the response for extra information. | 429 Too Many Requests     | {<br>  "error": "429 Too Many Requests",<br>  "statusCode": 429<br>}                                                     |
| The server is not able to process the request due to internal issues                               | 500 Internal Server Error | {<br>  "error": "500 Internal Server Error",<br>  "statusCode": 500<br>}                                                 |
| The server is currently unable to handle the request                                               | 503 Service Unavailable   | {<br>  "error": "503 Service Unavailable",<br>  "statusCode": 503<br>}                                                   |

#### Examples

##### Add a Bald Eagle record:

Request:
* POST `/birds`

Request body:

```json
{
  "name": "Bald Eagle",
  "scientificName": "Haliaeetus leucocephalus",
  "description": "Everyone knows this iconic bird",
  "primaryColor": "brown",
  "secondaryColor": ["white"],
  "flightPattern": "sine",
  "size": "large",
  "song": "Kraa",
  "area": ["US Texas", "Mexico"],
  "time": ["morning", "afternoon"],
  "migratory": false
}
```

Response (201 Created):

```json
{
  "uuid": "b3f1e6a7-9c02-4d15-8e3f-2c6b8a4d5e70",
  "name": "Bald Eagle",
  "scientificName": "Haliaeetus leucocephalus",
  "description": "Everyone knows this iconic bird",
  "primaryColor": "brown",
  "secondaryColor": [
    "white"
  ],
  "flightPattern": "sine",
  "size": "large",
  "song": "Kraa",
  "area": [
    "US Texas",
    "Mexico"
  ],
  "time": [
    "morning",
    "afternoon"
  ],
  "migratory": false
}
```

##### Add a duplicate Bald Eagle record:

Request:
* POST `/birds`

Request body:

```json
{
  "name": "Bald Eagle",
  "scientificName": "Haliaeetus leucocephalus",
  "description": "Everyone knows this iconic bird",
  "primaryColor": "brown",
  "secondaryColor": ["white"],
  "flightPattern": "sine",
  "size": "large",
  "song": "Kraa",
  "area": ["US Texas", "Mexico"],
  "time": ["morning", "afternoon"],
  "migratory": false
}
```

Response (409 Conflict):

```json
{
  "error": "Duplicate bird record with the given common and scientific names is found",
  "statusCode": 409
}
```

### Update a Bird

This endpoint allows you to update any field, except UUID, of an existing bird. If there is an existing record with the given common and scientific names, then the request is going to be rejected to avoid adding duplicates.

#### Request Specification

* Method: `PATCH`
* Path: `/birds/{uuid}`
* Headers: `Content-Type: application/json`, `Accept: application/json`
* Body: A JSON object with some or all fields from the "Add a New Bird" request specification.
* Restrictions on parameters' values: in accordance with the [Bird Data Model](#bird-data-model).

#### Response Specification

A single JSON [bird object](#bird-data-model) of the updated record.

#### Status Codes

| Scenario                                                                                           | Status Code               | Body                                                                                                                     |
| -------------------------------------------------------------------------------------------------- | ------------------------- | ------------------------------------------------------------------------------------------------------------------------ |
| The bird is successfully updated                                                                   | 200 OK                    | In accordance to the response specification                                                                              |
| One of the parameters is invalid                                                                   | 400 Bad Request           | {<br>  "error": "Detailed message",<br>  "statusCode": 400<br>}                                                          |
| Incorrect or missing `X-API-Key` header                                                            | 401 Unauthorized          | {<br>  "error": "401 Unauthorized",<br>  "statusCode": 401<br>}                                                          |
| There is no bird with the `uuid` in the catalogue                                                  | 404 Not Found             | {<br>  "error": "404 Not Found",<br>  "statusCode": 404<br>}                                                             |
| An existing record with the given common and scientific names is found                             | 409 Conflict              | {<br>  "error": "Duplicate bird record with the given common and scientific names is found",<br>  "statusCode": 409<br>} |
| The rate limit is exceeded. Check the rate limiting headers in the response for extra information. | 429 Too Many Requests     | {<br>  "error": "429 Too Many Requests",<br>  "statusCode": 429<br>}                                                     |
| The server is not able to process the request due to internal issues                               | 500 Internal Server Error | {<br>  "error": "500 Internal Server Error",<br>  "statusCode": 500<br>}                                                 |
| The server is currently unable to handle the request                                               | 503 Service Unavailable   | {<br>  "error": "503 Service Unavailable",<br>  "statusCode": 503<br>}                                                   |

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

Response body:

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

### Delete a Bird

This endpoint permanently removes a bird record from the catalogue by its UUID.

#### Request Specification

* Method: `DELETE`
* Path: `/birds/{uuid}`

#### Status Codes

| Scenario                                                                                           | Status Code               | Body                                                                     |
| -------------------------------------------------------------------------------------------------- | ------------------------- | ------------------------------------------------------------------------ |
| The bird is successfully removed                                                                   | 204 No Content            | Empty                                                                    |
| UUID is invalid                                                                                    | 400 Bad Request           | {<br>  "error": "Detailed message",<br>  "statusCode": 400<br>}          |
| Incorrect or missing `X-API-Key` header                                                            | 401 Unauthorized          | {<br>  "error": "401 Unauthorized",<br>  "statusCode": 401<br>}          |
| There is no bird with the `uuid` in the catalogue                                                  | 404 Not Found             | {<br>  "error": "404 Not Found",<br>  "statusCode": 404<br>}             |
| The rate limit is exceeded. Check the rate limiting headers in the response for extra information. | 429 Too Many Requests     | {<br>  "error": "429 Too Many Requests",<br>  "statusCode": 429<br>}     |
| The server is not able to process the request due to internal issues                               | 500 Internal Server Error | {<br>  "error": "500 Internal Server Error",<br>  "statusCode": 500<br>} |
| The server is currently unable to handle the request                                               | 503 Service Unavailable   | {<br>  "error": "503 Service Unavailable",<br>  "statusCode": 503<br>}   |
