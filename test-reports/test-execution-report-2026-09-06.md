# Test Execution Report — E-Commerce REST API

| | |
|---|---|
| **Project** | E-Commerce REST API |
| **Environment** | `https://qademo.com` |
| **Test date** | September 6, 2026 |
| **Tester** | QA Tester |
| **Test type** | Manual API testing (Postman) + exploratory UI testing |
| **Reference** | [Test cases](../test-cases/ecommerce-api-test-cases.md) |

## 1. Summary

| Metric | Result |
|---|---:|
| Total test scenarios executed | 30 |
| Passed | 28 |
| Failed | 2 |
| Pass rate | 93.3% |
| Confirmed functional bugs | 2 |
| Findings needing further investigation | 1 |
| Critical security issues | 0 |

## 2. Results by Area

| Area | Scenarios | Pass | Fail |
|---|---:|---:|---:|
| Cart API | 15 | 15 | 0 |
| Order API | 15 | 14 | 1 |
| Product API | 7 | 6 | 1 |
| Stock boundary / total calculation | 2 | 2 | 0 |
| Auth & authorization | 6 | 6 | 0 |
| **Exploratory UI (checkout)** | — | — | 1 bug found |

> Note: some scenarios overlap across sections in the source test cases (e.g. auth scenarios are also counted under Order API); totals reflect the source document's own scenario count of 30 rather than a strict deduplicated sum.

## 3. Findings

### 3.1 Confirmed Bugs

| ID | Title | Severity | Area |
|---|---|---|---|
| [BUG-01](../bugs/BUG-01-product-detail-endpoint-not-found.md) | `GET /api/products/{id}` returns `NOT_FOUND` for a valid, existing product | High | API / Products |
| [BUG-02](../bugs/BUG-02-whitespace-only-names-accepted.md) | Checkout accepts whitespace-only First/Last Name | Medium | UI / Checkout |

### 3.2 Open Question — Not Yet Filed as a Bug

**TC-20 — Missing `X-Session-ID` on order creation.** The API returns `CART_EMPTY` rather than an explicit header-validation error when `X-Session-ID` is omitted on order creation. Documentation lists this header as required, so the current behavior may be masking the real cause of the failure for API consumers. Recommend confirming intended behavior with the API owner before filing as a defect, since `CART_EMPTY` may be an acceptable (if imprecise) side effect of session resolution failing silently.

## 4. Coverage Notes

**What was covered well:**
- Cart CRUD operations and session isolation
- Stock enforcement at and beyond boundary values
- Cart total calculation accuracy
- End-to-end order creation, retrieval, and access control
- Authentication and authorization on all order-related endpoints
- Field-level validation on checkout payment details

**What would extend coverage further:**
- Executing and validating the draft Postman collection (see [`/postman`](../postman)) to make these checks repeatable
- Broader sweep of product IDs to determine the scope of BUG-01 (currently confirmed on ID `1` only)
- Concurrency testing on stock decrement (two carts competing for the last unit)
- Input validation fuzzing on shipping address and card fields (currently only boundary/empty cases covered)

## 5. Conclusion

The API is functionally solid across its core commerce flows — cart management, stock enforcement, order creation, and authorization all behaved correctly under the scenarios tested. Two confirmed defects were found: one API-level (product lookup by ID) and one UI-level (whitespace name validation), both with clear reproduction steps and recommended fixes filed as individual bug reports. No authentication bypass or cross-user data exposure was found.
