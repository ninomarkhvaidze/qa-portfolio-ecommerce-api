# Postman Collection (Draft — Not Yet Run)

A scaffolded set of Postman requests covering the highest-value manual test cases from [`/test-cases`](../test-cases), including dedicated regression checks for both confirmed bugs.

**Status: this collection has not been executed against the live API yet.** The requests and assertions were written based on the documented manual test results, but haven't been run or verified in Postman. Treat it as a starting point, not a validated test suite.

## Files

- `ECommerce-API.postman_collection.json` — requests grouped by resource (Products, Cart, Orders), each with `pm.test()` assertions.
- `ECommerce-API.postman_environment.json` — environment variables (`baseUrl`, `sessionId`, `authToken`, test product IDs).

## Running in Postman

1. Import both files into Postman (**File → Import**).
2. Select the **E-Commerce API - QA** environment.
3. Set `authToken` to a valid token for an authenticated test user (not committed — see below).
4. Run the collection, or an individual folder, via the Collection Runner.

## Notes on regression coverage

- **`PT-02 Get existing product by ID (BUG-01 regression)`** — written to fail until [BUG-01](../bugs/BUG-01-product-detail-endpoint-not-found.md) is fixed. Intended to catch a fix (or a future regression of the same bug) once the collection is actually run.
- **`BUG-02 regression - whitespace-only first name`** — checks whether the whitespace-name issue also exists at the API level, not just in the UI where [BUG-02](../bugs/BUG-02-whitespace-only-names-accepted.md) was originally found.
- **`TC-20`** — asserts on the documented (if imprecise) `400` behavior rather than a specific error code, since the correct behavior is still an open question — see the [test execution report](../test-reports/test-execution-report-2026-09-06.md#32-open-question--not-yet-filed-as-a-bug).

## Auth token

No real credentials are committed to this repo. Set `authToken` locally via the environment when you run the collection.
