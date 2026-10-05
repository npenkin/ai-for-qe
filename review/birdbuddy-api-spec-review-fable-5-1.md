Prepared by claude-fable-5-1.

# BirdBuddy API Specification – Review Report

## Summary

The BirdBuddy API specification (`birdbuddy-api-spec.md`) was reviewed section by section: Overview, Environments and Base URLs, Rate Limiting, Bird Data Model, and the five endpoints (Search, Retrieve by ID, Add, Update, Delete).

The document is not in a releasable state. The most severe problems are:

- **Missing sections / content**: There is no "Authentication and Authorization" section even though it is referenced and the `X-API-Key` header is used in every error table; the "Update a Bird" endpoint has no HTTP method and no Status Codes table; the "Delete a Bird" endpoint has no description, path, headers, or examples; the "Add a New Bird" Examples section is empty; the Search endpoint has a `limit` parameter but no pagination offset/cursor.
- **Incorrect content**: The Production v3 base URL points to the QA host; the Delete rate-limit window is `-60`; the "Retrieve a Bird by ID" status code table contains wrong bodies for 400, 500 and 503; wrong data types (`scientificName` is `number`, `size` is `boolean`); wrong examples (`size` example is `true`, "not found" example returns `total: 1`, `statusCode` returned as a string).
- **Data model defects**: missing parameter name for `migratory`, missing types for `flightPattern`, missing values for `secondaryColor` and `time`, wrong restriction "Maximum 100 value" for `flightPattern`.
- **Broken / invalid JSON and Markdown**: unclosed code blocks, missing commas, unquoted string value (`"size": large`), a malformed table separator row, broken anchor link, unclosed backticks, an H1 heading used inside an example.
- **Stray / irrelevant text**: "qwerty", "NEW LINE SEPARATOR", "Hyperspace and the Future of Space Exploration", "in the age of dinosaurs", "below the surface of the ocean", "1newr".
- **Numerous typos, grammar and punctuation issues** throughout.

A total of **78 issues** are listed below, grouped roughly in document order.

## Issues

| # | Issue | Suggested Correction | Category |
|---|-------|----------------------|----------|
| 1 | Overview, first paragraph ends with stray text: "The API fully supports REST architecture. qwerty" | Remove "qwerty". | Irrelevant Content |
| 2 | Overview, second paragraph: missing space after the URL/period: "https://www.birdbuddy.org/sla.A network of regional datacenters" | "https://www.birdbuddy.org/sla. A network of regional datacenters…" | Typo / Grammar |
| 3 | Overview, third paragraph is an unfinished sentence with a double period: "has a vibrant and active.." | Complete the sentence, e.g. "…and has a vibrant and active community of contributors." | Incomplete Content |
| 4 | Overview: stray placeholder text on its own line: "NEW LINE SEPARATOR" | Remove the line. | Irrelevant Content |
| 5 | Overview claims "The service has no restrictions on personal or commercial use", yet the API requires an API key and enforces rate limits. | Reword, e.g. "The service may be used for personal and commercial purposes subject to the rate limits described below." | Inconsistency |
| 6 | Environments / QA: description starts in lower case and is missing a period between sentences: "latest release candidate. The data is purged on the 1st of each month A new version is deployed…" | "The latest release candidate. The data is purged on the 1st of each month. A new version is deployed when necessary with a 24-hour notice." | Typo / Grammar |
| 7 | Environments / Production: the v3 base URL points to the QA host: "v3: https://www.qa.birdbuddy.org/v3" | "v3: https://www.birdbuddy.org/v3" | Incorrect Content |
| 8 | Environments: versions are listed in a different order for QA (v2, v3) and Production (v3, v2). | Use the same order (e.g. v2 then v3) in both lists. | Inconsistency |
| 9 | Environments: the document never states which API version (v2 or v3) it describes, nor the differences between versions or deprecation status of v2. | Add a statement such as "This document describes v3 of the API" and a short note on v2 status/differences. | Missing Content |
| 10 | Rate Limiting: references a section that does not exist: "(see \"Authentication and Authorization\" section)". The `X-API-Key` header is used in all status-code tables but is never documented. | Add an "Authentication and Authorization" section describing the `X-API-Key` header, how to obtain a token, and the 401 behaviour. | Missing Content |
| 11 | Rate Limiting table: "Delete a Bird" window duration is negative: "-60" | "60" | Incorrect Content |
| 12 | Rate Limiting: "It's recommended to implement exponential backoff to avoid the service." – sentence is nonsensical. | "It's recommended to implement exponential backoff to avoid exceeding the rate limit." | Typo / Grammar |
| 13 | Rate Limiting: status-code tables say "Check the rate limiting headers in the response for extra information", but only `Retry-After` is documented; no `X-RateLimit-*` headers are defined. | Document all rate-limiting response headers (e.g. `X-RateLimit-Limit`, `X-RateLimit-Remaining`, `X-RateLimit-Reset`, `Retry-After`). | Missing Content |
| 14 | Rate Limiting: the destructive "Delete a Bird" operation has a higher limit (60/min) than "Add" and "Update" (10/min), which is questionable. | Confirm the intended value; likely 10 per 60 sec to match other write operations. | Design / Clarity |
| 15 | Empty, irrelevant section: "## Hyperspace and the Future of Space Exploration" | Remove the section. | Irrelevant Content |
| 16 | Bird Data Model: `scientificName` Type is "number" while Values says "A string" and the example is "Haliaeetus leucocephalus". | Type: "string". | Incorrect Content |
| 17 | Bird Data Model: `secondaryColor` Values cell is empty. | "red, yellow, blue, orange, green, purple, pistachio, teal, indigo, magenta, scarlet, amber, grey, black, white, brown" (same as `primaryColor`). | Missing Content |
| 18 | Bird Data Model: `flightPattern` Type cell is empty. | Type: "string". | Missing Content |
| 19 | Bird Data Model: `flightPattern` Restrictions "Maximum 100 value" contradicts the single-value enum (and the example `circle`). | "Only 1 value". | Incorrect Content |
| 20 | Bird Data Model: `size` Example is "true", which is not one of "small, medium, large". | Example: "large". | Incorrect Content |
| 21 | Bird Data Model: `size` description says `large` is "greater than 12"", while the Search endpoint says "12" and above"; also the boundary between `small` ("less than 4"") and `medium` ("4 - 12"") leaves 12" ambiguous. | Use one definition everywhere, e.g. `small` < 4", `medium` 4"–12" (inclusive), `large` > 12". | Inconsistency |
| 22 | Bird Data Model: `time` Values lists only "night". | "morning, afternoon, evening, night". | Missing Content |
| 23 | Bird Data Model: last row has an empty Parameter name (the row describes the migratory flag). | Parameter: "migratory". | Missing Content |
| 24 | Bird Data Model: last row description contains stray text: "Identifies 1newr whether the bird is migratory" | "Identifies whether the bird is migratory". | Typo / Grammar |
| 25 | Bird Data Model: `migratory` Type is "boolean" but Values include "null"; nullability is not explained. | Type: "boolean (nullable)" and add a note that `null` means "unknown". | Design / Clarity |
| 26 | Bird Data Model: `description` – inconsistent capitalisation: "general Information". | "general information". | Typo / Grammar |
| 27 | Bird Data Model: no indication of which fields are always present / nullable in responses (e.g. can `song`, `flightPattern` be `null` or omitted when not supplied on creation?). | Add a "Nullable / Always present" column or a note describing response behaviour for optional fields. | Missing Content |
| 28 | Search for a Bird: double period: "except `area`.." | "except `area`." | Typo / Grammar |
| 29 | Search for a Bird / Request: `Retry-After: 100` is listed as a request header. `Retry-After` is a response header returned with 429. | Remove `Retry-After` from request headers; list `X-API-Key: <token>` instead. | Incorrect Content |
| 30 | Search for a Bird / Request: typo "vaues" and odd capitalisation "in Accordance". | "Restrictions on the query parameters' values: in accordance with …" | Typo / Grammar |
| 31 | Search for a Bird / Request: broken anchor link "[Bird Data Model](#bird-dat)". | "[Bird Data Model](#bird-data-model)". | Formatting / Markdown |
| 32 | Search for a Bird query table: `size` Type is "boolean" while Values are "small, medium, large". | Type: "string". | Incorrect Content |
| 33 | Search for a Bird query table: `flightPattern` Type cell is empty. | Type: "string". | Missing Content |
| 34 | Search for a Bird query table: `time` Values missing a comma: "morning afternoon, evening, night". | "morning, afternoon, evening, night". | Typo / Grammar |
| 35 | Search for a Bird: only `limit` is offered; there is no `offset`/`page`/cursor parameter, so results beyond the first 100 can never be retrieved. | Add an `offset` (or `page`/`cursor`) query parameter and document it in the response (e.g. `offset`, `limit`). | Missing Content |
| 36 | Search for a Bird: the spec states the AND logic applies to all parameters except `area`, but does not say whether array parameters like `secondaryColor` and `time` must match all supplied values or any of them. | Clarify matching semantics for multi-value parameters (ALL vs ANY). | Design / Clarity |
| 37 | Search for a Bird: "Exact match is applied" for `name`/`scientificName` does not specify case sensitivity. | State whether matching is case-sensitive. | Design / Clarity |
| 38 | Search for a Bird / Response: "The top--level response" – double hyphen. | "The top-level response". | Typo / Grammar |
| 39 | Search for a Bird / Response: says the object has "3 attributes" but only two (`total`, `data`) are listed and shown in the sample. | Change to "2 attributes" or add the missing attribute. | Inconsistency |
| 40 | Search for a Bird / Response: `total` is defined as "the number of records returned", which is redundant with `data.length` given `limit`; it is unclear whether it is the total number of matches. | Define `total` as "the total number of records matching the criteria (regardless of `limit`)" or clarify otherwise. | Design / Clarity |
| 41 | Search for a Bird / Response: nonsensical sample caption: "A sample with a list bird objects omitted in the age of dinosaurs:" | "A sample with the list of bird objects omitted:" | Irrelevant Content |
| 42 | Search for a Bird / Examples: second example uses a top-level H1 heading "# Try finding a non-existing bird." inside an H4 section, and lacks a "Response:" label. | Use "##### Try finding a non-existing bird." and add "Response:" before the JSON. | Formatting / Markdown |
| 43 | Search for a Bird / Examples: the non-existing bird response shows `"total": 1` with an empty `data` array. | `"total": 0`. | Incorrect Content |
| 44 | Search for a Bird / Examples: "Incorrect observation time" example is not formatted as a heading, has no "Response:" label, and the JSON is not inside a code block. | Use "##### Incorrect observation time" heading, add "Request:"/"Response:" labels and wrap the JSON in a ```json block. | Formatting / Markdown |
| 45 | Search for a Bird / Examples: `"statusCode": "400"` is a string, while the status-code table defines it as a number (`"statusCode": 400`). | `"statusCode": 400`. | Inconsistency |
| 46 | Search for a Bird / Examples: JSON samples mix tabs and spaces for indentation. | Use consistent 2-space indentation. | Formatting / Markdown |
| 47 | Section separator after Search uses "----" while other sections use "---". | Use "---" consistently. | Formatting / Markdown |
| 48 | Retrieve a Bird by ID: description starts with lower case and is missing an article: "this endpoint retrieves bird record by the bird's UUID" | "This endpoint retrieves a bird record by the bird's UUID…" | Typo / Grammar |
| 49 | Retrieve a Bird by ID: path placeholder "/birds/${UUID}" differs from the "/birds/{uuid}" style used in Update. | Use "/birds/{uuid}" consistently across all endpoints. | Inconsistency |
| 50 | Retrieve a Bird by ID: request headers omit `X-API-Key` although 401 is returned for a missing key (same for all endpoints). | Add `X-API-Key: <token>` to the headers list of every endpoint (or document it once in the Authentication section). | Missing Content |
| 51 | Retrieve a Bird by ID / Status Codes: scenario for 200 is "Successful search" although this is a retrieval by ID. | "Successful retrieval". | Typo / Grammar |
| 52 | Retrieve a Bird by ID / Status Codes: 400 body says `"error": "403 Bad Request"`. | `"error": "400 Bad Request"` (or "Detailed message" as in other endpoints). | Incorrect Content |
| 53 | Retrieve a Bird by ID / Status Codes: 500 body says `"error": "500 Not Found"`. | `"error": "500 Internal Server Error"`. | Incorrect Content |
| 54 | Retrieve a Bird by ID / Status Codes: 503 body is `{"error": "404 Not Found", "statusCode": 404}`. | `{"error": "503 Service Unavailable", "statusCode": 503}`. | Incorrect Content |
| 55 | Add a New Bird: description typos: "an an existing record", "scientific names,,", "duplicates ." | "an existing record", "scientific names,", "duplicates." | Typo / Grammar |
| 56 | Add a New Bird / Request: "Method:: `POST`" – double colon. | "Method: `POST`". | Typo / Grammar |
| 57 | Add a New Bird / Request: path has an unclosed backtick and is followed by several blank lines: "Path: `/birds" | "Path: `/birds`" and remove the blank lines. | Formatting / Markdown |
| 58 | Add a New Bird / Request: no Headers line, unlike the Update endpoint which lists `Content-Type` and `Accept`. | Add "Headers: `Content-Type: application/json`, `Accept: application/json`". | Missing Content |
| 59 | Add a New Bird / Request: garbled sentence: "Body: JSON object the in format described below the surface of the ocean." | "Body: A JSON object in the format described below." | Irrelevant Content |
| 60 | Add a New Bird / parameter table: typo "The common nae of a bird". | "The common name of a bird". | Typo / Grammar |
| 61 | Add a New Bird / parameter table: `migratory` Type is "bool" whereas the data model uses "boolean". | "boolean". | Inconsistency |
| 62 | Add a New Bird: not specified whether a client-supplied `uuid` in the body is ignored or rejected. | Add a note, e.g. "`uuid` is server-generated; if present in the body the request is rejected with 400 (or ignored)". | Missing Content |
| 63 | Add a New Bird / Status Codes: scenario for a missing required parameter or malformed JSON body is not explicitly covered ("One of the parameters is invalid" only). | Extend the 400 scenario to "The body is not valid JSON, a required parameter is missing, or a parameter is invalid". | Missing Content |
| 64 | Add a New Bird: "#### Examples" section is empty. | Add at least one request/response example (201) and ideally a 409 example. | Missing Content |
| 65 | Update a Bird: description punctuation: "update any field,, except UUID, of an existing bird If there is…" | "update any field, except UUID, of an existing bird. If there is…" | Typo / Grammar |
| 66 | Update a Bird: duplicate-check statement does not exclude the record being updated; a PATCH that resends the bird's own `name`/`scientificName` would appear to be rejected. | Clarify: "…if another record (with a different UUID) has the same common and scientific names, the request is rejected with 409 Conflict." | Design / Clarity |
| 67 | Update a Bird / Request: "Method:" is empty. | "Method: `PATCH`" (as used in the example). | Missing Content |
| 68 | Update a Bird / Request: "Path `/birds/{uuid}`" missing colon after "Path". | "Path: `/birds/{uuid}`". | Typo / Grammar |
| 69 | Update a Bird / Request: missing space: "Restrictions on parameters' values:in accordance". | "…values: in accordance…". | Typo / Grammar |
| 70 | Update a Bird: behaviour when `uuid` is included in the body is not defined ("any field, except UUID"). | State whether `uuid` in the body is ignored or returns 400. | Missing Content |
| 71 | Update a Bird: the entire "Status Codes" section is missing. | Add a table with 200 OK, 400, 401, 404, 409, 429, 500, 503 consistent with other endpoints. | Missing Content |
| 72 | Update a Bird / Example: unclosed backtick in "PATCH `/birds/f47ac10b-58cc-4372-a567-0e02b2c3d479". | "PATCH `/birds/f47ac10b-58cc-4372-a567-0e02b2c3d479`". | Formatting / Markdown |
| 73 | Update a Bird / Example request body: missing comma between properties: `"flightPattern": "sine"` newline `"song": "Sunny day"` – invalid JSON. | `"flightPattern": "sine",` | Incorrect Content |
| 74 | Update a Bird / Example: the request-body code block is never closed (missing ```), so "Response body:" and the following block are swallowed into it. | Add a closing ``` after the request body JSON. | Formatting / Markdown |
| 75 | Update a Bird / Example response body: `"size": large` – unquoted string, invalid JSON. | `"size": "large"`. | Incorrect Content |
| 76 | Delete a Bird: no description, no Path, no Headers, no Response Specification and no Examples section. | Add description ("Removes a bird from the catalogue"), "Path: `/birds/{uuid}`", headers (`X-API-Key`), a note that 204 has an empty body, and an example. | Missing Content |
| 77 | Delete a Bird / Status Codes: table separator row has only two columns while the header has three, producing a malformed Markdown table. | Add the third separator cell: `\| --- \| --- \| --- \|`. | Formatting / Markdown |
| 78 | General: the error response schema (`error`, `statusCode`) is not defined centrally, and 400 bodies are inconsistent ("Detailed message" vs. "403 Bad Request"). Common error codes such as 405 Method Not Allowed, 406 Not Acceptable, and 415 Unsupported Media Type are not documented. | Add an "Error Responses" section defining the schema and shared codes; reference it from each endpoint. | Missing Content |
