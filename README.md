# E-Commerce API — QA Portfolio Project

A manual QA project against a demo e-commerce REST API, showing the full testing cycle: test case design → execution → bug reporting.

**Status:** 30 manual test scenarios executed · 93.3% pass rate · 2 confirmed bugs · 0 critical security issues.

## Why this project

This repo is meant to demonstrate QA practice end-to-end, not just a list of green checkmarks — including how a genuine defect gets found, reproduced, and documented.

## Repo Structure

```text
.
├── test-cases/
│   └── ecommerce-api-test-cases.md      # 30 documented test cases across Products, Cart, Orders, Auth
├── test-reports/
│   └── test-execution-report-2026-09-06.md   # Execution summary, coverage notes, conclusions
├── bugs/
│   ├── BUG-01-product-detail-endpoint-not-found.md
│   ├── BUG-02-whitespace-only-names-accepted.md
│   └── evidence/
│       └── BUG-02/                      # Screenshots supporting the UI bug
└── postman/
    ├── ECommerce-API.postman_collection.json   # Draft Postman collection (not yet executed)
    ├── ECommerce-API.postman_environment.json
    └── README.md                        # What's in the collection and its current status
```

## Scope Tested

- **Products** — listing, retrieval by ID/slug, stock-aware add-to-cart behavior
- **Cart** — CRUD, session isolation, quantity validation, boundary stock testing
- **Orders** — creation, retrieval, payment field validation, auth/authorization
- **Security** — unauthenticated access, invalid credentials, cross-user data exposure

## Key Findings

| ID | Summary | Severity | Status |
|---|---|---|---|
| [BUG-01](bugs/BUG-01-product-detail-endpoint-not-found.md) | `GET /api/products/{id}` returns `NOT_FOUND` for a valid, existing product | High | Confirmed |
| [BUG-02](bugs/BUG-02-whitespace-only-names-accepted.md) | Checkout accepts whitespace-only First/Last Name | Medium | Confirmed |

Full context: [test execution report](test-reports/test-execution-report-2026-09-06.md).

## Tools & Approach

- **Manual API testing:** Postman (environment variables, auth flows, header/body validation, Tests-tab assertions)
- **Exploratory testing:** UI checkout flow, which is where BUG-02 was found — the API's own validation was already correct for the equivalent case, which is what made the UI gap visible
- **Postman collection (draft, not yet run):** a scaffolded set of requests covering the highest-value scenarios, including regression checks for both confirmed bugs — see [`postman/README.md`](postman/README.md) for status

## Author

QA Tester with a background in manual UI and API testing. Reach out via GitHub for questions about this project.
