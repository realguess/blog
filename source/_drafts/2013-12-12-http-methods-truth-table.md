title: HTTP Methods Truth Table
tags:
---

Progress:

1. Review the HTTP method documentation.
2. Post it to StackOverflow or quora.


```
+-----------------------------------------------------------+
| # | Request-URI              | Method         | RE  | RNE |
+-----------------------------------------------------------+
| 0 | GET    /resources        | list           | 200 | 200 |
| 1 | GET    /resources/entity | load/insert    | 200 | 404 |
|-----------------------------------------------------------|
| 2 | POST   /resources        | create         | 201 | 409 |
| 3 | POST   /resources/entity | N/A            | N/A | N/A |
|-----------------------------------------------------------|
| 4 | PUT    /resources        | (batch)        | 200 | 200 |
| 5 | PUT    /resources/entity | replace/save   | 204 | 201 |
|-----------------------------------------------------------|
| 6 | PATCH  /resources        | (batch)        | 200 | 200 |
| 7 | PATCH  /resources/entity | update         | 204 | 404 |
|-----------------------------------------------------------|
| 8 | DELETE /resources        | (batch)        | 200 | 200 |
| 9 | DELETE /resources/entity | remove/delete  | 204 | 404 |
+-----------------------------------------------------------+
```

Notes:

1. RE: resource exists
2. RNE: resource not exists
3. For batch request, whether resource/entity exists or not, the resulting HTTP
   status code is always `200`, because the code is used to indicate the status
   of the operation. The actual status code of each entity is enclosed in the
   response array. When there are no matching entities, the response is an empty
   array, therefore, status code `204` is not used.
4. There are two situation, a new resource is being created, then the `Location`
   header must indicate the fully qualified resource URI.

Questions:

1. Are naming of resource/entity correct? Resource is a collection of entitites.
2. Is not implementing #3 `POST /resources/entity` bad?
3. Anything wrong?
