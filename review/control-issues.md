# A list of issues introduced into the original Bird Buddy API specification.

## Semantics

| **Location** | **Change**                                        |
| ------------ | ------------------------------------------------- |
| 5            | qwerty                                            |
| 61           | 1newr                                             |
| 97           | age of dinosaurs                                  |
| 269          | the surface of the ocean                          |
| 9            | community                                         |
| 41           | overloading                                       |
| 76           | vaues                                             |
| 274          | nae                                               |
| 43           | ## Hyperspace and the Future of Space Exploration |
| 11           | NEW LINE SEPARATOR                                |

## Logic

| **Issue**                                                                                          | **Location** |
| -------------------------------------------------------------------------------------------------- | ------------ |
| UUID v1 instead of v4                                                                              | 149          |
| A reference to a section that doesn’t exist                                                        | 29           |
| Incorrect prefix for v2 and v3 environments (the order is not consistent)                          | 19-20        |
| The production environment’s link has “qa”                                                         | 24           |
| Rate limit has a negative integer                                                                  | 37           |
| A field from “Bird Data Model” has an incorrect example                                            | 57           |
| A field from “Bird Data Model” has an incorrect data type                                          | 52           |
| A field from “Bird Data Model” has an incorrect list of values                                     | 60           |
| A field from “Bird Data Model” has a missing data type                                             | 56           |
| A field from “Bird Data Model” has values missing                                                  | 55           |
| A field from “Bird Data Model” has no parameter name                                               | 61           |
| A field from “Bird Data Model” has an incorrect restriction                                        | 56           |
| A field from the “Search for a Bird” request specification has an incorrect data type              | 82           |
| A field from the “Search for a Bird” request specification has the data type missing               | 86           |
| Irrelevant header added to the headers of “Search for a Bird”                                      | 75           |
| Incorrect number of fields in JSON – “object with 3 attributes” instead “object with 2 attributes” | 93           |
| Incorrect status code number                                                                       | 217          |
| Incorrect status code message                                                                      | 221          |
| Error message body doesn’t conform to the type of error                                            | 222          |
| The “total” field in the search example has an incorrect number                                    | 181          |

## Orthography

| **Issue**                                                           | **Category**   | **Change**         | **Location** |
| ------------------------------------------------------------------- | -------------- | ------------------ | ------------ |
| Missing period                                                      | Punctuation    | month A new        | 17           |
| Missing period                                                      | Punctuation    | bird If            | 308          |
| Missing comma                                                       | Punctuation    | morning afternoon  | 88           |
| Missing colon                                                       | Punctuation    |                    | 313          |
| Missing space                                                       | Punctuation    |                    | 316          |
| Missing space                                                       | Punctuation    | sla.A              | 7            |
| Extra period                                                        | Punctuation    | active..           | 9            |
| Extra period                                                        | Punctuation    | area..             | 69           |
| Extra comma                                                         | Punctuation    | scientific names,, | 259          |
| Extra comma                                                         | Punctuation    | field,,            | 308          |
| Extra colon                                                         | Punctuation    | Method:: POST      | 263          |
| Extra space                                                         | Punctuation    | duplicates .       | 259          |
| A new sentence starts with a lower-case letter                      | Capitalization | this endpoint      | 200          |
| A new sentence starts with a lower-case letter                      | Capitalization | latest release     | 17           |
| A word in the middle of a sentence starts with an upper-case letter | Capitalization |                    | 53           |
| A word in the middle of a sentence starts with an upper-case letter | Capitalization |                    | 76           |
| Inconsistent capitalization                                         | Capitalization |                    | 71           |
| Missing hyphen                                                      | Hyphenation    |                    | 5            |
| Double hyphen                                                       | Hyphenation    |                    | 93           |

## Syntax

| **Issue**                            | **Location** |
| ------------------------------------ | ------------ |
| An article is missing                | 200          |
| An extra article is present          | 259          |
| Incorrect word order                 | 269          |
| Missing comma in JSON                | 333          |
| No quotes for a string value in JSON | 348          |
| Quotes around a number value in JSON | 192          |

## Formatting

| **Issue**                                                   | **Location**          |
| ----------------------------------------------------------- | --------------------- |
| Incorrect heading level                                     | 174                   |
| Missing backtick                                            | 264                   |
| Missing backtick                                            | 327                   |
| Broken table                                                | 372                   |
| Unwrapped code block in “Search for a Bird”                 | 190                   |
| Leaked code block                                           | 331-335               |
| Broken link                                                 | 76                    |
| Inconsistent indentation                                    | Multiple JSON objects |
| Inconsistent placeholder template format: ${UUID} vs {uuid} | 205                   |
| A list is split by multiple new lines in “Add a New Bird”   | 265 – 268             |
| Inconsistent length of horizontal separators                | 197                   |

## Technical Writing

| **Issue**                                               | **Location** |
| ------------------------------------------------------- | ------------ |
| Missing version information                             | N/A          |
| Missing “Authentication and Authorization” section      | N/A          |
| Missing examples in “Add a New Bird”                    | 302          |
| Missing status codes in “Update a Bird”                 | N/A          |
| Missing information about rate limiting headers         | N/A          |
| Missing HTTP verb for “Update a Bird”                   | 312          |
| No headers for the POST operation                       | N/A          |
| Missing information about the merger strategy for PATCH | N/A          |
| Missing description for “Delete a Bird”                 | N/A          |
| Missing path for “Delete a Bird”                        | N/A          |
