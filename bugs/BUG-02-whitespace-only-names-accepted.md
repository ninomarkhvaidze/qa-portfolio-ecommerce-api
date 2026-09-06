# BUG-02 — Whitespace-only customer names accepted during checkout

| | |
|---|---|
| **Area** | UI / Checkout |
| **Severity** | Medium |
| **Priority** | High |
| **Status** | Confirmed |
| **Found by** | Manual UI testing |
| **Related** | Order creation API correctly rejects an empty first name — see [test-cases](../test-cases/ecommerce-api-test-cases.md#4-order-api-test-cases) TC-21 |

## Summary

The checkout form allows an order to be submitted when the First Name and Last Name fields contain only whitespace characters.

## Steps to Reproduce

1. Add a product to the cart.
2. Proceed to Checkout.
3. Enter only spaces in the **First Name** field.
4. Enter only spaces in the **Last Name** field.
5. Fill the remaining required checkout fields with valid values.
6. Click **Place Order**.

## Expected Result

Whitespace-only names should be treated as empty input and rejected with an appropriate validation message.

## Actual Result

The order was submitted successfully and the application displayed the **Order Confirmed!** page.

## Evidence

See [`evidence/BUG-02/`](evidence/BUG-02/):
- `01-checkout-form-before.png` — checkout form before submission.
- `02-order-confirmed.png` — successful order confirmation after submission.

## Impact

Invalid customer information can be submitted and stored in completed orders — this pollutes order records and shipping data with unusable names.

This is also inconsistent with the API validation observed during testing, where an actually empty first name **is** rejected (TC-21).

## Root Cause (Suspected)

Client-side and/or server-side validation checks for an empty string but does not trim whitespace before the check, so `"   "` passes a naive truthy/length check.

## Recommendation

Trim leading/trailing whitespace before validation and reject values that become empty after trimming. Apply the same rule consistently across the UI form and the order-creation API.

## Regression Test Cases

| Input | Field | Expected |
|---|---|---|
| `"   "` | First Name | Rejected with validation message |
| `"   "` | Last Name | Rejected with validation message |
| `" John "` | First Name | Normalized (trimmed) or handled per the application's stated input policy |
