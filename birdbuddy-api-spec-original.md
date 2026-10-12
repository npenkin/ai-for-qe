# BirdBuddy API

## Overview

The BirdBuddy API is an essential tool for any developer or company aiming at cataloging and identifying birds. The community-supported catalogue of existing and extinct birds is the biggest in the world. The API fully supports REST architecture.

The API is used in the BirdBuddy service deployed to Global Cloud and provides 99.999% uptime. You can find the current availability statistics at https://www.birdbuddy.org/sla. A network of regional datacenters minimizes latency. The service has no restrictions on personal or commercial use.

The API's source is published to [GitHub](https://www.github.com/) and has a vibrant and active community.

## Version Information

* v3: Current stable.
* v2: Deprecated. This version is still available, but it will not get any future updates.

## Environments and Base URLs

### QA

Latest release candidate. The data is purged on the 1st of each month. A new version is deployed when necessary with a 24-hour notice.

* v3: https://www.qa.birdbuddy.org/v3 
* v2: https://www.qa.birdbuddy.org/v2

### Production

* v3: https://www.birdbuddy.org/v3
* v2: https://www.birdbuddy.org/v2

## Authentication and Authorization

All endpoints of the API require an API token. You can generate it in your BirdBuddy account at https://www.birdbuddy.org/dev. The API token must be specified in the `X-API-Key` header of each request. If the client is authenticated with the token, then the client has access to all of the available resources.

## Rate Limiting

The rate limit for each endpoint is set per API token.

| Endpoint              | Limit | Window Duration (sec) |
| --------------------- | :---: | :-------------------: |
| Search for a Bird     |  60   |          60           |
| Retrieve a Bird by ID |  60   |          60           |
| Add a New Bird        |  10   |          60           |
| Update a Bird         |  10   |          60           |
| Delete a Bird         |  60   |          60           |

All responses include the following headers:
* **X-RateLimit-Limit**: Total requests allowed per window.
* **X-RateLimit-Remaining**: Remaining requests in the current window.
* **X-RateLimit-Reset**: Unix Epoch timestamp when the limit resets.

When a client exceeds the current limit, the API returns `429 Too Many Requests` with the `Retry-After` header, which specifies the cooldown period in seconds.

It's recommended to implement exponential backoff to avoid overloading the service.

## Bird Data Model

| Parameter      |       Type       |                                                        Values                                                         | Restrictions                  |               Example                |                                                         Description                                                         |
| -------------- | :--------------: | :-------------------------------------------------------------------------------------------------------------------: | ----------------------------- | :----------------------------------: | :-------------------------------------------------------------------------------------------------------------------------: |
| uuid           |      string      |                                                        UUID v4                                                        | UUID v4 format                | f47ac10b-58cc-4372-a567-0e02b2c3d479 |             A unique UUID v4 identifier of the bird. It's generated for every new bird added to the catalogue.              |
| name           |      string      |                                                       A string                                                        | Min 1 and max 100 characters  |              Bald Eagle              |                                                  The common name of a bird                                                  |
| scientificName |      string      |                                                       A string                                                        | Min 1 and max 200 characters  |       Haliaeetus leucocephalus       |                                            The scientific latin name of the bird                                            |
| description    |      string      |                                                       A string                                                        | Min 1 and max 5000 characters |    An iconic American alpha bird.    |    A description providing you with the general information about the bird. Includes interesting facts and observations.    |
| primaryColor   |      string      | red, yellow, blue, orange, green, purple, pistachio, teal, indigo, magenta, scarlet, amber, grey, black, white, brown | Only 1 value                  |               magenta                |                                      The main color of the bird that covers most of it                                      |
| secondaryColor | Array of strings |                                          Values from the primary color range                                          | Maximum 10 values             |        `["white", "orange"]`         |                                             A list of minor colors of the bird                                              |
| flightPattern  |      string      |                                            linear, sine, circle, grounded                                             | Maximum 1 value               |                circle                |                                           The observed flight pattern of the bird                                           |
| size           |      string      |                                                 small, medium, large                                                  | Only 1 value                  |                medium                | The approximate size of the bird. `small` is for birds less than 4", `medium` for 4 - 12", and `large` for greater than 12" |
| song           |      string      |                                                       A string                                                        | Min 1 and max 200 characters  |             Rat-tat-tat              |                                                       The bird's song                                                       |
| area           | Array of strings |                                                  Names of locations                                                   | Maximum 50 values             |       `["US Texas", "Mexico"]`       |                                       A list of locations where the bird  is observed                                       |
| time           | Array of strings |                                          morning, afternoon, evening, night                                           | Maximum 4 distinct values     |      `["morning", "afternoon"]`      |                                          A list of times when the bird is observed                                          |
| migratory      |     boolean      |                                                   true, false, null                                                   | Only 1 value                  |                 true                 |                                          Identifies whether the bird is migratory                                           |

## Endpoints

### Search for a Bird

Returns a list of birds satisfying the given search criteria.

The search applies the AND logic for all parameters except `area`.

#### Request Specification

* Method: `GET`
* Path: `/birds`
* Headers: `Accept: application/json`
* Restrictions on the query parameters' values: in accordance to [Bird Data Model](#bird-data-model).

| Query parameter |       Type       |     Required      |                                                        Values                                                         |                 Example                 |                                                                                                       Description                                                                                                       |
| --------------- | :--------------: | :---------------: | :-------------------------------------------------------------------------------------------------------------------: | :-------------------------------------: | :---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------: |
| name            |      string      |        No         |                                                       A string                                                        |            name=Bald%20Eagle            |                                                                                   The common name of a bird. Exact match is applied.                                                                                    |
| scientificName  |      string      |        No         |                                                       A string                                                        |     scientificName=Strix%20nebulosa     |                                                                             The scientific latin name of the bird. Exact match is applied.                                                                              |
| size            |      string      |        No         |                                                 small, medium, large                                                  |               size=small                |                                                The approximate size of the bird. `small` is for birds less than 4", `medium` for 4 - 12", and `large` for 12" and above                                                 |
| primaryColor    |      string      |        No         | red, yellow, blue, orange, green, purple, pistachio, teal, indigo, magenta, scarlet, amber, grey, black, white, brown |           primaryColor=indigo           |                                                                                    The main color of the bird that covers most of it                                                                                    |
| secondaryColor  | Array of strings |        No         | red, yellow, blue, orange, green, purple, pistachio, teal, indigo, magenta, scarlet, amber, grey, black, white, brown | secondaryColor=teal&secondaryColor=blue |                                                 A list of minor colors of the bird. Repeated keys can be used to specify multiple values. Duplicate values are ignored.                                                 |
| song            |      string      |        No         |                                                        String                                                         |            song=Rat-tat-tat             |                                                                                                     The bird's song                                                                                                     |
| flightPattern   |      string      |        No         |                                            linear, sine, circle, grounded                                             |           flightPattern=sine            |                                                                                         The observed flight pattern of the bird                                                                                         |
| area            | Array of strings |        No         |                                                        String                                                         |  area=Mexico%20Cozumel&area=New%20York  | A list of locations where the bird  is observed. The API uses keyword search to identify most probable match. Repeated keys can be used to specify multiple values. Duplicate values are ignored. The OR logic is used. |
| time            | Array of strings |        No         |                                          morning, afternoon, evening, night                                           |         time=morning&time=night         |                                             A list of times when the bird is observed. Repeated keys can be used to specify multiple values. Duplicate values are ignored.                                              |
| limit           |      number      | No (default = 25) |                                                        1 - 100                                                        |                limit=50                 |                                                                                      The maximum number of records to be returned.                                                                                      |

#### Response Specification

The top-level response is a JSON object with 2 attributes:
* `total` - the number of records returned.
* `data` - an array of JSON [bird objects](#bird-data-model).

A sample with a list bird objects omitted:

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
      "uuid": "6ba7b810-9dad-41d1-80b4-00c04fd430c8",
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
	    "morning", "afternoon"
	  ],
	  "migratory": true
    }
  ]
}
```



##### Try finding a non-existing bird.

Request:
* GET `/birds?area=NO_SUCH_PLACE_EXISTS`

```json
{
  "total": 0,
  "data": []
}
```

Incorrect observation time:
* GET `/birds?time=supper`

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
* Headers: `Accept: application/json`

#### Response Specification

A single JSON [bird object](#bird-data-model).

#### Status Codes

| Scenario                                                                                           | Status Code               | Body                                                                     |
| -------------------------------------------------------------------------------------------------- | ------------------------- | ------------------------------------------------------------------------ |
| Successful search                                                                                  | 200 OK                    | In accordance to the response specification                              |
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


---

### Add a New Bird

This endpoint allows you to add a new bird to the catalogue. If there is an existing record with the given common and scientific names, then the request is going to be rejected to avoid adding duplicates.

#### Request Specification

* Method: `POST`
* Path: `/birds`
* Headers: `Content-Type: application/json`, `Accept: application/json`
* Body: JSON object in the format described below.
* Restrictions on parameters' values: in accordance to [Bird Data Model](#bird-data-model).

| Parameter      |       Type       | Required |                                                        Values                                                         |             Example             |                                                         Description                                                         |
| -------------- | :--------------: | :------: | :-------------------------------------------------------------------------------------------------------------------: | :-----------------------------: | :-------------------------------------------------------------------------------------------------------------------------: |
| name           |      string      |   Yes    |                                                        string                                                         |           Bald Eagle            |                                                  The common name of a bird                                                  |
| scientificName |      string      |   Yes    |                                                        string                                                         |    Haliaeetus leucocephalus     |                                            The scientific latin name of the bird                                            |
| description    |      string      |   Yes    |                                                        string                                                         | Everyone knows this iconic bird |    A description providing you with the general information about the bird. Includes interesting facts and observations.    |
| primaryColor   |      string      |   Yes    | red, yellow, blue, orange, green, purple, pistachio, teal, indigo, magenta, scarlet, amber, grey, black, white, brown |             magenta             |                                      The main color of the bird that covers most of it                                      |
| secondaryColor | Array of strings |    No    |                                   An array of strings from the primary color range                                    |      `["white", "orange"]`      |                                             A list of minor colors of the bird                                              |
| flightPattern  |      string      |    No    |                                            linear, sine, circle, grounded                                             |             circle              |                                           The observed flight pattern of the bird                                           |
| size           |      string      |   Yes    |                                                 small, medium, large                                                  |             medium              | The approximate size of the bird. `small` is for birds less than 4", `medium` for 4 - 12", and `large` for greater than 12" |
| song           |      string      |    No    |                                                        string                                                         |           Rat-tat-tat           |                                                       The bird's song                                                       |
| area           | Array of strings |    No    |                                                  An array of strings                                                  |    `["US Texas", "Mexico"]`     |                                       A list of locations where the bird  is observed                                       |
| time           | Array of strings |    No    |                                An array of values: morning, afternoon, evening, night                                 |   `["morning", "afternoon"]`    |                                          A list of times when the bird is observed                                          |
| migratory      |       bool       |    No    |                                                   true, false, null                                                   |              null               |                                          Identifies whether the bird is migratory                                           |

#### Response Specification

Headers:
* `Location`: A path to the new bird record.

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

##### Add a new bird to the catalogue.

Request:
* POST `/birds`

Request body:

```json
{
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

Location header `Location: https://www.birdbuddy.org/v3/birds/f47ac10b-58cc-4372-a567-0e02b2c3d479` and the response body:

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

##### Try creating a new record with an invalid primary color:

Request:
* POST `/birds`

Request body:

```json
{
  "name": "Great Egret",
  "scientificName": "Ardea alba",
  "description": "The Old World population is often referred to as the 'great white egret'. This species is sometimes confused with the great white heron of the Caribbean, which is a white morph of the closely related great blue heron.",
  "primaryColor": "silver",
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

Response body:

```json
{
  "error": "Parameter 'primaryColor' has an incorrect value: 'silver'",
  "statusCode": 400
}
```

---

### Update a Bird

This endpoint allows you to update any field, except UUID, of an existing bird. If there is an existing record with the given common and scientific names, then the request is going to be rejected to avoid adding duplicates.

#### Request Specification

* Method: `PATCH`
* Path: `/birds/{uuid}`
* Headers: `Content-Type: application/json`, `Accept: application/json`
* Body: A JSON object with some or all fields from the "Add a New Bird" request specification.
* Restrictions on parameters' values: in accordance to [Bird Data Model](#bird-data-model).

Merge strategy:
* Old values are replaced with the new ones.
* Omitted fields remain unchanged.

#### Response Specification

A single JSON [bird object](#bird-data-model) of the updated record.

#### Status Codes

| Scenario                                                                                           | Status Code               | Body                                                                                                                     |
| -------------------------------------------------------------------------------------------------- | ------------------------- | ------------------------------------------------------------------------------------------------------------------------ |
| An existing bird is successfully updated                                                           | 200 OK                    | In accordance to the response specification                                                                              |
| One of the fields or UUID is invalid                                                               | 400 Bad Request           | {<br>  "error": "Detailed message",<br>  "statusCode": 400<br>}                                                          |
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


---

### Delete a Bird

This endpoint allows to delete a bird by its UUID.

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
