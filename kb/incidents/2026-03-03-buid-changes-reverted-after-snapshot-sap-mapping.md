# Employee assigned BUID keeps changing — reverted after snapshot due to SAP mapping (KNVV.VKBUR)

## Metadata
- Date: 2026-03-03
- Product/Module: module:integrations (SAP integration)
- Environment: (not specified in the email thread)
- Severity/Impact: P2 — transfer/unload requests not appearing to supervisors in Salesbuzz Back Office
- Customer: the customer
- Related ticket(s): CAS-303342-N3R9N4
- Owner: Mohamed Fayed

## Summary
The customer reported that employee/salesman BUID assignments revert automatically after each **Salesbuzz snapshot job**. The cause was incorrect SAP-side definitions used by the integration mapping: `KNVV.VKBUR` values were not aligned with the expected mapping to the target BUID (example from this case: to map to BUID `11114`, `KNVV.VKBUR` must be `TF00`, but it was `MK00`).

## Business impact
- Transfer/unload requests were not appearing to the respective supervisors in Salesbuzz Back Office, preventing required actions

## Symptoms (What users saw)
- After updating employee/salesman BUID (example in this case: `11112` → `11114`), the value is reverted after the next Salesbuzz snapshot.
- Difficulty tracing the change because the audit log report was not functioning appropriately (as reported by the customer).

## Detection / Evidence
### Customer-provided evidence (sanitized)
- Multiple employee/salesman IDs were affected.
- A supervisor employee ID was also reported to revert to another BUID after an update.
- Customer stated SAP-side verification was done and fields were correctly updated on their side.

### Support analysis
- This behavior is consistent with snapshot/integration re-applying master data definitions based on SAP mapping rules.

## Root Cause
Data definition / mapping issue:
- The SAP field `KNVV.VKBUR` was not set to the correct value required by the customer’s mapping table to derive the intended BUID.
- During the Salesbuzz snapshot job, the integration re-applies the mapping and overwrites the manually updated BUID back to the mapped value.

## Resolution (Step-by-step)
1. Identify the affected employee/salesman records (IDs) reported by the customer.
2. On SAP side, validate the mapping-driving fields in table `KNVV`, specifically `VKBUR`.
3. Update `KNVV.VKBUR` to match the customer’s mapping table definition for the target BUID.
   - Example from this case: for BUID `11114`, set `KNVV.VKBUR = TF00` (not `MK00`).
4. Run/allow the next Salesbuzz snapshot job.
5. Confirm the BUID remains stable after the snapshot.

## Verification
- After the next Salesbuzz snapshot job, confirm the employee/salesman BUID does not revert.
- Confirm transfer/unload requests appear to the correct supervisors in Salesbuzz Back Office.

## Prevention / Follow-ups
- Use the integration reference (customer-facing/internal) for SAP definition/mapping inquiries to reduce unnecessary escalation to dev.
- Improvement item: fix/enable audit log reporting to better trace automatic changes.

## Escalation guidance
Escalate when:
- SAP definitions are confirmed correct but BUID still reverts after snapshot.
- Reversion occurs for a larger population (possible mapping regression).

Provide to Dev/Integration team:
- Snapshot time(s) when reversion occurs
- Expected BUID vs actual BUID after snapshot
- SAP evidence for `KNVV.VKBUR` and the mapping table rule

## Tags
- module:integrations
- module:sap
- symptom:buid-reverts
- event:salesbuzz-snapshot
- cause:sap-mapping-misconfiguration
- cause:user-definition-issue
- fix:update-sap-knvv-vkbur
- area:master-data

## Related articles
- (Optional) Integration reference doc: SRS Document - SalesBuzz SAP Integration v1.10
