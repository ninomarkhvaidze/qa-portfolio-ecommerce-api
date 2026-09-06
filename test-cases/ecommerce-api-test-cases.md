# E-Commerce REST API — QA Test Cases

## 1. Test Overview

| | |
|---|---|
| **Application** | E-Commerce REST API |
| **Environment** | `https://qademo.com` |
| **Testing approach** | Manual API testing using Postman |
| **Automation** | Draft Postman collection, not yet run (see [`/postman`](../postman)) |
| **Test date** | September 6, 2026 |

### Scope

- Products
- Cart
- Orders
- Authentication & authorization
- Stock validation
- Request validation
- Cart/session isolation
- Order calculations

---

## 2. Test Execution Summary

| Metric | Result |
|---|---:|
| Total manual test scenarios | 30 |
| Passed | 28 |
| Failed / unexpected behavior | 2 |
| Pass rate | 93.3% |
| Critical security issues | 0 identified |
| Functional bugs identified | 2 confirmed ([BUG-01](../bugs/BUG-01-product-detail-endpoint-not-found.md), [BUG-02](../bugs/BUG-02-whitespace-only-names-accepted.md)) |

> Full narrative results are in the [test execution report](../test-reports/test-execution-report-2026-09-06.md).

---

## 3. Cart API Test Cases

| ID | Test Case | Expected Result | Actual Result | Status |
|---|---|---|---|---|
| TC-01 | Add valid product to cart | Product added successfully | Product added | PASS |
| TC-02 | Add second product using same session | Both products appear in cart | Successful | PASS |
| TC-03 | Add same product again | Existing quantity increases | Quantity increased | PASS |
| TC-04 | Add product with quantity `0` | Request rejected | Zod validation error: quantity must be greater than 0 | PASS |
| TC-05 | Add product with negative quantity | Request rejected | Zod validation error | PASS |
| TC-06 | Add nonexistent product | `NOT_FOUND` | `Product not found` | PASS |
| TC-07 | Add item without `X-Session-ID` | Validation error | `Missing X-Session-ID header` | PASS |
| TC-08 | Use different session ID | Separate cart should be created | Separate cart returned | PASS |
| TC-09 | Get cart | Cart items and totals returned | Correct cart returned | PASS |
| TC-10 | Update cart quantity | Quantity updated successfully | Successful | PASS |
| TC-11 | Verify updated cart totals | Totals recalculated | Correct totals returned | PASS |
| TC-12 | Update quantity to `0` | Item removed / handled correctly | Item removed | PASS |
| TC-13 | Update nonexistent cart item | `NOT_FOUND` | `Cart item not found` | PASS |
| TC-14 | Update cart without session ID | Validation error | `Missing X-Session-ID header` | PASS |
| TC-15 | Delete cart item | Item removed | Successful and verified with GET cart | PASS |

---

## 4. Order API Test Cases

| ID | Test Case | Expected Result | Actual Result | Status |
|---|---|---|---|---|
| TC-16 | Create valid order | Order created successfully | Successful | PASS |
| TC-17 | Create order with empty cart | Request rejected | `CART_EMPTY` / `Cart is empty` | PASS |
| TC-18 | Create order without authentication | `UNAUTHORIZED` | `Missing authentication` | PASS |
| TC-19 | Create order with invalid credentials | Authentication rejected | `Invalid credentials` | PASS |
| TC-20 | Create order without `X-Session-ID` | Required header should be validated | `CART_EMPTY` returned instead of a header-validation error | **FAIL** |
| TC-21 | Empty shipping first name | Validation error | `First name is required` | PASS |
| TC-22 | Missing shipping last name | Validation error | Required-field validation | PASS |
| TC-23 | Missing shipping address | Validation error | Required-field validation | PASS |
| TC-24 | Empty card number | Validation error | Minimum 13 characters validation | PASS |
| TC-25 | Missing card number | Validation error | Required-field validation | PASS |
| TC-26 | 12-digit card number | Validation error | Minimum 13 characters validation | PASS |
| TC-27 | Invalid expiry date | Validation error | `Invalid expiry format (MM/YY)` | PASS |
| TC-28 | Invalid CVV | Validation error | `Invalid CVV` | PASS |
| TC-29 | Missing CVV | Validation error | Required-field validation | PASS |
| TC-30 | Empty cardholder name | Validation error | `Cardholder name is required` | PASS |

### Additional Order Tests (exploratory, unnumbered)

- Successful order cleared the cart.
- Created order appeared in `GET /api/orders`.
- Specific order retrieval worked.
- Nonexistent order returned `NOT_FOUND`.
- `GET /api/orders` worked without `X-Session-ID`.
- `GET /api/orders` without authentication returned `UNAUTHORIZED`.
- Access to an order belonging to another user returned `NOT_FOUND`, preventing exposure of the order.

---

## 5. Product API Test Cases

| ID | Test Case | Expected Result | Actual Result | Status |
|---|---|---|---|---|
| PT-01 | Get all products | Product list returned | 26 products returned | PASS |
| PT-02 | Get existing product by ID | Product details returned | Product ID 1 returned `NOT_FOUND` | **FAIL** |
| PT-03 | Get nonexistent product by ID | `NOT_FOUND` | `Product not found` | PASS |
| PT-04 | Get product by slug | Product returned | Successful | PASS |
| PT-05 | Add out-of-stock product | Request rejected | `OUT_OF_STOCK` | PASS |
| PT-06 | Add quantity greater than available stock | Request rejected | `OUT_OF_STOCK` | PASS |
| PT-07 | Update quantity above available stock | Request rejected | `OUT_OF_STOCK` | PASS |

---

## 6. Stock Boundary Testing

Product **19 — Desk Organizer**: unit price `18.50`, available stock `13`. Cart already contained 1 unit.

**Test:** add an additional quantity of 12 (`1 + 12 = 13`, exactly the available stock).

**Result:** Request accepted.

```json
{
  "totalItems": 13,
  "totalAmount": 240.5
}
```

**Status:** PASS — confirms the API allows purchasing exactly the available stock while rejecting quantities that exceed it.

---

## 7. Cart Total Calculation

Product 19: `13 × 18.50 = 240.50`

API returned:
```json
{
  "totalItems": 13,
  "totalAmount": 240.5
}
```

**Status:** PASS — quantity and total amount calculated correctly.

---

## 8. Authentication & Authorization Coverage

| Scenario | Result |
|---|---|
| Missing authentication when creating order | PASS |
| Invalid credentials | PASS |
| Missing authentication when retrieving orders | PASS |
| Accessing another user's order | PASS |
| Order retrieval with valid authentication | PASS |
| Order list without `X-Session-ID` | PASS |

No authentication bypass or unauthorized order-data exposure was identified during API testing.

---

## 9. Overall Result

### Strengths

- Correct cart/session isolation
- Correct quantity validation
- Correct stock enforcement
- Correct cart total calculation
- Correct authentication handling
- Correct order creation and retrieval
- Correct empty-cart handling
- Correct handling of nonexistent resources
- No unauthorized order exposure identified

### Issues Identified

1. **[BUG-01](../bugs/BUG-01-product-detail-endpoint-not-found.md)** — Product detail endpoint returns `NOT_FOUND` for a valid, existing product ID. Confirmed functional bug.
2. **Missing `X-Session-ID` on order creation is not explicitly validated** (TC-20) — the API documentation identifies `X-Session-ID` as required for order creation, but the endpoint returns `CART_EMPTY` instead of a header-validation error. Needs further investigation to confirm intended behavior.
3. **[BUG-02](../bugs/BUG-02-whitespace-only-names-accepted.md)** — UI checkout accepts whitespace-only First/Last Name, inconsistent with the API's own empty-string validation (TC-21). Confirmed functional bug, found during exploratory UI testing.

---

## 10. Next Step — Automation Candidates

Scenarios worth automating first, if these tests are picked up as an automated suite:

1. Create valid order
2. Authentication failure
3. Add product to cart
4. Cart quantity update
5. Stock-limit validation
6. Cart total calculation
7. Product retrieval
8. Regression test for BUG-01 (product ID lookup)
9. Order retrieval
10. Unauthorized order access

A draft Postman collection scaffolding these scenarios exists under [`/postman`](../postman), but it has not been executed yet — see that folder's README for current status.
