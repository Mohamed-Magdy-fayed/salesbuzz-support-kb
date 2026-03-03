# Salesman stuck in sync — SAP integration overrides free item category

## Metadata
- Date: 2026-03-03
- Product/Module: module:integrations (SAP integration service)
- Environment: Test 4.55 SP1
- Severity/Impact: P2 — salesman sync stuck; order failed to upload to SAP
- Customer: (not provided; use alias if needed)
- Related ticket(s): (not provided)
- Owner: (not provided)

## Summary
Salesman sync was stuck because an order upload to SAP failed with an item category validation error. The SAP integration service was overriding the free item category via its `data.xml`, sending `ZFRE` which was not defined in SAP for the item.

## Symptoms (What users saw)
- Salesman stuck during sync.
- Order processing fails in integration queue.

## Detection / Evidence
### Logs
Key log lines (sanitized):
- `[BSQueue_Thread # 0] :Finished processing Order No 004928-11106 with failure.`
- `[BSQueue_Thread # 0] :Item category ZFRE is not defined for item`

Observation:
- `ZFRE` appeared only in `bs_queueLog` (queue logs), not in order data.

### Database checks
Queries used (sanitized):
- `select * from bs_queue where SalesmanNo ='0000004928'`
- `select top 100 * from bs_queueLog where SalesmanNo ='0000004928' order by Serial desc`
- `select Order_Or_Invoice, * from HH_ORDER where orderNO = '004928-11106'`
- `select * from HH_ORDERDETAILS where orderNO = '004928-11106'`
- `select * from HH_PARAMS where PARAM_ID = 7094`
- `select * from HH_PARAMS_BU where PARAM_ID = 7094`

Finding:
- The order details included a free item.
- The configured free item parameter value did **not** match the category shown in the SAP error (`ZFRE`).

## Root Cause
The SAP integration service had a `data.xml` configuration that overwrote (forced) the free item category being sent to SAP, resulting in `ZFRE` being sent even though it was not defined in SAP for the item.

## Resolution (Step-by-step)
1. Update the SAP integration service configuration by removing the fixed/free item category override from the service `data.xml`.
2. Restart the SAP integration service.
3. Re-sync the salesman.

## Verification (How we confirmed it’s fixed)
- Salesman sync completed successfully.
- The order uploaded to SAP successfully.

## Prevention / Follow-ups
- Ensure `data.xml` does not contain hardcoded item category overrides unless required and aligned with SAP configuration.
- When similar errors appear, validate whether the value exists in:
  - order data (HH_ORDERDETAILS / params)
  - integration service mapping/override (e.g., `data.xml`)

## Escalation guidance
Escalate when:
- The SAP side confirms the category is correct, but the integration still sends a different value.
- Restart and config correction do not clear the stuck queue.

Provide to Dev/DBA:
- Queue log excerpt including the failing order number and category error.
- Confirmation of what `HH_PARAMS` / `HH_PARAMS_BU` returns for free item settings.
- Relevant `data.xml` mapping sections (sanitized).

## Tags
- module:integrations
- module:sap
- error:item-category-not-defined
- cause:service-config-override
- fix:update-data-xml
- fix:restart-service
- symptom:salesman-sync-stuck

## Related articles
- (none yet)