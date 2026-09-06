# BUG-01 — `GET /api/products/{id}` returns `NOT_FOUND` for a valid, existing product ID

| | |
|---|---|
| **Area** | API / Products |
| **Severity** | High |
| **Priority** | High |
| **Status** | Confirmed |
| **Found by** | Manual API testing (Postman) |
| **Related test case** | [PT-02](../test-cases/ecommerce-api-test-cases.md#5-product-api-test-cases) |

## Summary

Requesting product details by ID for a product known to exist (confirmed via `GET /api/products`, which lists 26 products including ID 1) returns a `404 Product not found` response instead of the product's details.

## Steps to Reproduce

1. `GET /api/products` — confirm the response includes a product with `id: 1`.
2. `GET /api/products/1`.

## Expected Result

`200 OK` with the full details of product ID 1.

## Actual Result

`404 NOT_FOUND` — `Product not found`.

## Evidence

- PT-01: `GET /api/products` returns 26 products, including product ID 1.
- PT-02: `GET /api/products/1` returns `NOT_FOUND` for that same product.
- PT-04 (`GET /api/products/{slug}`) succeeds for the equivalent product, confirming the product itself is not missing or deleted — only the ID-based lookup route is affected.

## Impact

Any client or integration relying on ID-based product lookups (deep links, cart references resolving to a product page, admin tooling, etc.) can fail for products that clearly exist. This is a functional/data-integrity bug, not just an edge case, since ID lookups are a primary API contract.

## Root Cause (Suspected)

Likely a mismatch between the identifier used internally by the lookup handler (e.g. expecting a different ID type/format, or an off-by-one/reserved-ID issue) versus the identifiers returned by the list endpoint. Needs source-level investigation — not confirmed from black-box testing alone.

## Recommendation

1. Compare the ID field/type used in the `GET /api/products` list response against what the `GET /api/products/:id` handler queries against.
2. Add a regression test asserting that every ID returned by the list endpoint is independently resolvable via the detail endpoint.
3. Investigate whether this affects only product ID `1` (e.g. a reserved/edge value) or a broader range — recommend a quick sweep across all 26 product IDs to scope the blast radius.

## Regression Test

- `GET /api/products/{id}` for every ID present in the `GET /api/products` response should return `200` with matching product data.
