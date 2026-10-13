# Gemma 4

Missed functional requirements:

    1. Environments availability for “Environments and Base URLs” section.
    2. Status codes for each endpoint. There FR_SERVER_ERRORS that covers only 500 and 503.
    3. Search response specification.

Functional requirements not covered by test scenarios:

    1. Have no tests: 
        1. FR_SEARCH_SCIENTIFIC.
        2. FR_SEARCH_SIZE.
        3. FR_SEARCH_PRIMARY_COLOR.
        4. FR_SEARCH_SECONDARY_COLOR.
        5. FR_SEARCH_SECONDARY_COLOR.
        6. FR_SEARCH_FLIGHT_PATTERN.
        7. FR_SEARCH_TIME.
    2. The required minimal set of input combinations is missing:
        1. FR_SEARCH_NAME.
        2. FR_SEARCH_LOGIC.
        3. FR_SEARCH_LIMIT.
        4. FR_SEARCH_AREA.
        5. FR_RETRIEVE_ID.
        6. FR_ADD_BIRD.
        7. FR_UPDATE_BIRD.
        8. FR_DELETE_BIRD.
        9. FR_VALIDATION.
        10. FR_AUTH_KEY.
        11. FR_RATE_LIMIT.
        12. FR_SERVER_ERRORS.

Errors:

    1. Requirement FR_SEARCH_SECONDARY_COLOR has an incorrect statement "Search for birds using secondary colors (OR logic).". This parameter supports the AND logic.
    2. The number of steps in "Expected results" doesn't match the expected number of steps in 13 test scenarios. Scenarios: 1-7, 10-13, 23-26.
    3. The expected results uses "<=10" instead of exact match in scenario 5.
    4. A value that is not a UUID is used in scenario 8. Should use a valid UUID v4 that is not the present in the database.
    5. A value that is not a UUID is used in scenario 16. Should use a valid UUID v4 that is not the present in the database.

# Meta Muse Glimmer

Missed functional requirements:

    1. Environments availability for “Environments and Base URLs” section.

Functional requirements not covered by test scenarios:

    1. Have no tests:
        1. REQ_BIRD_MODEL_SCIENTIFIC_NAME.
        2. REQ_BIRD_MODEL_MIGRATORY.
        3. REQ_BIRD_MODEL_DESCRIPTION.
        4. REQ_BIRD_MODEL_FLIGHT_PATTERN.
        5. REQ_BIRD_MODEL_SIZE.
        6. REQ_BIRD_MODEL_SONG.
        7. REQ_BIRD_MODEL_AREA.
        8. REQ_BIRD_MODEL_UUID.
        9. REQ_ADD_STATUS_CODES.
    2. The required minimal set of input combinations is missing:
        1. REQ_AUTH_X_API_KEY.
        2. REQ_RATE_LIMIT.
        3. REQ_BIRD_MODEL_NAME.
        4. REQ_ADD_REQUIRED_FIELDS.
        5. REQ_BIRD_MODEL_PRIMARY_COLOR.
        6. REQ_BIRD_MODEL_SECONDARY_COLOR.
        7. REQ_BIRD_MODEL_TIME.
        8. REQ_SEARCH_ENDPOINT.
        9. REQ_SEARCH_LIMIT.
        10. REQ_SEARCH_STATUS_CODES.
        11. REQ_RETRIEVE_ENDPOINT.
        12. REQ_RETRIEVE_STATUS_CODES.
        13. REQ_ADD_ENDPOINT.
        14. REQ_ADD_RESPONSE_FORMAT.
        15. REQ_UPDATE_ENDPOINT.
        16. REQ_DELETE_STATUS_CODES.
        17. REQ_SEARCH_LOGIC_AND_OR.

Errors:

    1. The number of steps in "Expected results" doesn't match the expected number of steps in 18 test scenarios. Scenarios: 2-4, 6-10, 15, 18-19, 21-22, 24-27.
    2. REQ_SEARCH_LOGIC_AND_OR is not specified for scenario 11.
    3. The expected results uses "<=5" instead of exact match in scenario 14.

# Qwen 3.8

Missed functional requirements:

    1. Required headers for the search endpoint.
    2. Required headers for the retrieve endpoint.

I could have omitted these two requirements, but the model specified it for some end-points, so it’s expected that it should be consistent across all of them.

Functional requirements not covered by test scenarios:

    1. Have no tests:
        1. FR-RL-02.
        2. FR-RL-03.
        3. FR-RL-04.
        4. FR-RL-05.
    2. The required minimal set of input combinations is missing:
        1. FR-AUTH-02.
        2. FR-SRCH-15.
        3. FR-AUTH-02.
        4. FR-UPD-05.
        5. FR-AUTH-03.
        6. FR-BM-03.
        7. FR-BM-04.
        8. FR-BM-06.
        9. FR-BM-07.
        10. FR-BM-09.
        11. FR-BM-10.
        12. FR-BM-11.
        13. FR-BM-12.
        14. FR-SRCH-04.
        15. FR-SRCH-05.
        16. FR-SRCH-06.
        17. FR-SRCH-07.
        18. FR-SRCH-08.
        19. FR-SRCH-10.
        20. FR-SRCH-01.
        21. FR-SRCH-14.
        22. FR-ADD-01.
        23. FR-ADD-02.
        24. FR-ADD-03.
        25. FR-ADD-04.
        26. FR-UPD-01.
        27. FR-UPD-02.
        28. FR-UPD-03.
        29. FR-UPD-04.

Errors:

    1. There is an unnecessary verification of rate limiting headers in scenario 1.
    2. FR-SRCH-15 is specified in scenario 41, though it's an authentication requirement.
    3. Scenario 63 is a duplicate. Tested in scenario 5.
    4. Scenario 7 tests FR-RL-06. This requirement is tested separately in scenario 12.
    5. Scenario 10 uses 11 birds to verify rate limiting. Updating a single bird 11 times is more efficient.
    6. FR-SRCH-12 is not specified in scenarios 28 - 33, though it's tested in them. It's only present in scenario 27. An example of an inconsistency.
    7. Inconsistent breakdown of scenarios for 500 and 503 status codes. Sometimes separate, but sometimes both in one. For instance, scenario 58 and scenarios 44, 45.
    8. Scenario 61 has an extra check that is not necessary. Updating a UUID can be tested with a single bird record. There is no need to have two.
    9. Scenario 66 expects 11 existing birds thought the update operation can be tested with a single record.
    10. Scenario 68 and 69 should be combined into a single one. Otherwise it's duplication.
    11. Scenario 77 is a duplicate. Should be part of 52.
    12. Scenario 78 is not a valid test and requires clarifying the expected behavior first.
    13. A part of scenario 79 is already tested in scenario 31.
    14. Scenario 80 is a duplicate of scenario 62.

# Google Gemini 3.1 Pro Preview

Missed functional requirements:

    1. Rate limiting section.
    2. Environments availability for “Environments and Base URLs” section.
    3. 500 responses.
    4. 503 responses.
    5. 400 for the DELETE endpoint.
    6. 404 for the DELETE endpoint.

Functional requirements not covered by test scenarios:

    1. The required minimal set of input combinations is missing:
        1. REQ-SRCH-01.
        2. REQ-SRCH-02.
        3. REQ-SRCH-03.
        4. REQ-SRCH-04.
        5. REQ-GET-01.
        6. REQ-POST-01.
        7. REQ-PATCH-01.
        8. REQ-VAL-01.
        9. REQ-AUTH-01.

Errors:

    1. Scenario 5 has a contradicting description and steps. The description reads "all" optional fields, when the steps read "some".

# Claude Haiku 4.5

Missed functional requirements:

    1. Environments availability for “Environments and Base URLs” section.

Functional requirements not covered by test scenarios:

    1. Have no tests:
        1. REQ-DATA-001.
        2. REQ-DATA-002.
        3. REQ-DATA-003.
        4. REQ-DATA-004.
        5. REQ-DATA-005.
        6. REQ-DATA-006.
        7. REQ-DATA-007.
        8. REQ-DATA-008.
        9. REQ-DATA-009.
        10. REQ-DATA-010.
        11. REQ-DATA-011 - PATCH.
    2. The required minimal set of input combinations is missing:
        1. REQ-AUTH-002.
        2. REQ-AUTH-001.
        3. REQ-RATE-001.
        4. REQ-RATE-002.
        5. REQ-RATE-003.
        6. REQ-RATE-004.
        7. REQ-RATE-005.
        8. REQ-RATE-006.
        9. REQ-SEARCH-001.
        10. REQ-SEARCH-002.
        11. REQ-SEARCH-003.
        12. REQ-SEARCH-004.
        13. REQ-SEARCH-005.
        14. REQ-SEARCH-006.
        15. REQ-SEARCH-009.
        16. REQ-SEARCH-010.
        17. REQ-SEARCH-010.
        18. REQ-SEARCH-011.
        19. REQ-SEARCH-015.
        20. REQ-RETRIEVE-005.
        21. REQ-ADD-003.
        22. REQ-ADD-002.
        23. REQ-ADD-004.
        24. REQ-ADD-005.
        25. REQ-ADD-010.
        26. REQ-ADD-011.
        27. REQ-UPDATE-001.
        28. REQ-UPDATE-002.
        29. REQ-UPDATE-004.
        30. REQ-UPDATE-005.
        31. REQ-UPDATE-006.
        32. REQ-UPDATE-007.
        33. REQ-UPDATE-008.
        34. REQ-UPDATE-009.
        35. REQ-UPDATE-010.
        36. REQ-UPDATE-012.
        37. REQ-UPDATE-013.
        38. REQ-UPDATE-014.
        39. REQ-UPDATE-017.
        40. REQ-DELETE-004.

Errors:

    1. REQ-DATA-004 - reads 15 colors, when the enum has 16.
    2. Scenario 14 should be included into 9 - 13. No reason to have it separated.
    3. Scenario 39 should be part of other TS-SEARCH-BASIC tests, not a separate one.
    4. Scenario 41 should be part of other search scenarios.
    5. Scenario 42 is a duplicate of 40.
    6. Scenario 53 is part of 52 (duplicate).
    7. Scenario 57 has an invalid v4 UUID.
    8. Inconsistent usage of /birds/{valid_uuid} and /birds/{uuid}. Scenarios 58 - 59.
    9. Scenarios 60, 64, 67, 70 should be part of a single test scenario.
    10. Scenario 107 should be part of other ADD scenarios.
    11. Scenario 108 is a duplicate of 61.
    12. Scenario 143 should be part of other scenarios.
    13. Scenario 144 should be part of other scenarios.
    14. Scenario 145 has an invalid v4 UUID.
    15. Scenarios 152 and 151 should be a single scenario.
    16. Scenario 156 has an invalid v4 UUID.
    17. Scenario 200 is a duplicate of 79 - 80.
    18. Identified 87 functional requirements, not 100.
    19. The number of steps in "Expected results" doesn't match the expected number of steps in 131 scenarios.

# Claude Fable 5.1

Functional requirements not covered by test scenarios:

    1. The required minimal set of input combinations is missing:
        1. RL-02.
        2. RL-03.
        3. RL-06.
        4. RL-07.
        5. ERR-04.
        6. ERR-06.
        7. SRCH-03.
        8. SRCH-04.
        9. SRCH-05.
        10. SRCH-02.
        11. SRCH-06.
        12. SRCH-07.
        13. SRCH-02.
        14. SRCH-08.
        15. ADD-01.
        16. ADD-03.
        17. ADD-02.
        18. DM-13.
        19. DM-14.
        20. UPD-08.

Errors:

    1. Scenario 54 is included into 55 (duplicate).
    2. Scenario 71 doesn’t have ADD-02 in the list of mapped requirements.
    3. Scenario 107 is a duplicate of scenario 105.
    4. Scenario 125 is a duplicate of scenario 129.
    5. Scenario 133 is covered by multiple scenarios (duplicate).

# ChatGPT 5 Mini

Missed functional requirements:

    1. Environments availability for “Environments and Base URLs” section.

Functional requirements not covered by test scenarios:

    1. The required minimal set of input combinations is missing:
        1. AUTH_BEARER.
        2. AUTH_API_KEY.
        3. AUTH_REQUIRED.
        4. AUTH_401.
        5. HEADERS_REQUIREMENT.
        6. RATE_LIMIT_PER_TOKEN.
        7. RATE_LIMIT_429.
        8. RATE_LIMIT_RESP_HEADERS.
        9. RATE_LIMIT_ENDPOINT_DEFAULTS.
        10. SEARCH_FILTERS_AND.
        11. SEARCH_FILTERS_MULTI_OR.
        12. SEARCH_REPEATED_DUPLICATES_IGNORED.
        13. SEARCH_INVALID_PARAM_400.
        14. SEARCH_LIMIT_OFFSET_DEFAULTS.
        15. RETRIEVE_BY_ID_ENDPOINT.
        16. RETRIEVE_INVALID_UUID_400.
        17. CREATE_BIRD_ENDPOINT.
        18. CREATE_RESPONSE_201.
        19. BIRD_UUID_SERVER_GEN.
        20. CREATE_VALIDATION_400.
        21. BIRD_SECONDARY_COLOR.
        22. BIRD_TIME.
        23. BIRD_SCI_NAME.
        24. BIRD_DESCRIPTION.
        25. BIRD_SONG.
        26. BIRD_MIGRATORY.
        27. UPDATE_BIRD_ENDPOINT.
        28. UPDATE_RESPONSE_200.
        29. BIRD_FLIGHT_PATTERN.
        30. DELETE_NOT_FOUND_404.

Errors:

    1. The value of the reference of functional requirement ERROR_SCHEMA is “object”. It’s hard to understand what “object” it refers to.
    2. The "Error Responses & Status Codes" suite is missing some necessary requirements.
    3. The "Duplicate Detection & Conflict Handling" suite is not required. Already tested in the corresponding endpoints' suites.
    4. PAGINATION_FIELDS_RETURNED is a made up requirement mapped to scenario 10.
    5. The expected result of scenario 10 uses “>=1” instead of "matches the number of birds with the given search criteria".
    6. ENUM_ALLOWED_VALUES_ERRORS is a made up requirement mapped to scenario 14.
    7. Step 2 of scenario 16 has an incorrect expected result.
    8. RETRIEVE_NOT_FOUND_404 is incorrectly mapped to scenario 17.
    9. Scenario 19 uses a v1 UUID for 404, when a non-existing v4 UUID is required.
    10. CREATE_VALIDATION_400 should be mapped to scenario 25.
    11. Scenario 27 mixes two different use cases in a single test, so it's impossible to determine what causes the test to fail: an incorrect enum value, or more than 4 enum values.
    12. The list of scenarios is unordered. Could have been grouped by the test suite.
    13. DELETE_BIRD_SUCCESS_204 is a made up requirement mapped to scenario 35.
    14. Scenario 39 is a duplicate of scenario 13.
    15. Scenario 40 is a duplicate of scenario 16.
    16. The number of steps in "Expected results" doesn't match the expected number of steps in 39 scenarios.

# ChatGPT 5.6 Sol

Functional requirements not covered by test scenarios:

    1. The required minimal set of input combinations is missing:
        1. BIRD-03.
        2. BIRD-04.
        3. SEA-02.
        4. SEA-03.
        5. RET-01.
        6. BIRD-01.
        7. CRT-01.
        8. UPD-01.
        9. UPD-03.
        10. DEL-01.

Errors:

    1. Functional requirement BIRD-02 defines name, scientificName as required fields. There is no optionality in the referenced section of the API specification.
    2. Scenario 7 is a duplicate of scenario 55.
    3. The "Endpoint failure matrix" suite duplicates 500 and 503 coverage of "Protocol and error contract".
