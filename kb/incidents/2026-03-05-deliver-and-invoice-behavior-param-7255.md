# Delivery and Invoice issue — Deliver & Invoice depends on parameter 7255

## Metadata
- Date: 2026-03-05
- Product/Module: module:ui (Back Office delivery/invoicing) + module:operations (delivery cycle)
- Environment: Test (as referenced in email thread)
- Severity/Impact: (not explicitly stated; business impact relates to invoicing/payment for credit orders)
- Customer: the customer
- Related ticket(s): CAS-303279-K0M0B8
- Owner: Mohamed Fayed

## Summary
The customer reported issues generating invoices and completing payment/collection for credit orders during the delivery/collection cycle. Investigation found behavior is controlled by backend parameter `7255`:
- If `7255` is **enabled**, the system performs **Deliver and Invoice**.
- If `7255` is **disabled**, the system performs **Deliver only**.

Parameter `7255` is not available in Back Office screens and must be updated from the backend.

## Symptoms (What users saw)
- When trying to perform manual **Deliver and Invoice** from Back Office for credit orders, invoicing/payment was not generated as expected.
- Confusion occurred when orders were allocated to a deliveryman: those should be delivered from PDA rather than BO.

## Detection / Evidence
### Customer description (sanitized)
- Customer requested support to “check the delivery and collection cycle”.
- Customer reported an order was delivered but invoice/payment for a credit order still required support.

### Support notes / reproduction guidance
- If an order is planned/allocated to a driver, deliver it from **PDA**, not from **Back Office**.
- To reproduce BO Deliver & Invoice behavior, ensure the credit order is not assigned to a deliveryman and then attempt Deliver & Invoice from BO.

## Root Cause
System configuration:
- Parameter `7255` controls whether the system executes Deliver-only vs Deliver-and-Invoice.

## Resolution (Step-by-step)
1. Verify current value/state of parameter `7255` in backend configuration.
2. Enable parameter `7255` on the **test** server (done in this case).
3. Ask customer to run a full delivery cycle on test and confirm invoice generation works as expected.
4. If confirmed, apply the same parameter change to the live server.

## Verification
- Customer runs a cycle on the test environment and confirms invoices are generated correctly when Deliver & Invoice is expected.

## Prevention / Follow-ups
- Consider exposing parameter `7255` on Back Office configuration screens (if product decision allows) or document it clearly in internal runbooks.
- Add/maintain a runbook for BO vs PDA delivery flows:
  - If allocated to driver → PDA
  - BO deliver/invoice only for non-allocated/manual scenarios

## Escalation guidance
Escalate when:
- `7255` is enabled but Deliver & Invoice still does not execute.
- Errors occur only for credit orders (possible business rule/config validation issue).

Provide to Dev team:
- Order type (credit/cash)
- Whether order was allocated to a driver
- Exact steps attempted in BO and/or PDA
- Current parameter `7255` value

## Tags
- module:back-office
- module:delivery
- module:invoicing
- module:pda
- param:7255
- cause:configuration
- fix:enable-param
- symptom:deliver-without-invoice
