# HTTP Methods, Status Codes and Idempotency

GET retrieves a representation (safe, idempotent). POST submits/processes data (not generally idempotent). PUT replaces a resource at a known URI (intended idempotent). PATCH partially modifies; idempotency depends on the patch. DELETE is intended idempotent. HEAD returns GET-like headers without body. OPTIONS reports communication options and is used for CORS preflight.

An operation is idempotent when repeating it has the same intended effect. Payment APIs often use idempotency keys to prevent duplicate charges after retries.

Know status codes: 200, 201, 202, 204, 301/302, 400, 401, 403, 404, 409, 422, 429, 500, 502, 503, 504.

**Interview:** 401 vs 403? Authentication missing/invalid vs identity known but access refused. PUT vs PATCH? Replace vs partial change.
