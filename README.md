# ShopLedger — Master Repository (All 3 Tickets Solved)

> Complete resolution of all three legacy engineering tickets for the **GeeksforGeeks × OpenAI "Build with Codex" Workshop** (`ShopLedger v0.8.4`).

---

## 🚀 Overview & Architecture

ShopLedger is a wholesale & retail POS, Khata credit ledger, and GST invoicing application. This master repository consolidates the characterization test suites, minimal fixes, and verifications across all three open tickets without introducing any new dependencies or mutating `data/db.json`.

- **Architecture Diagram:** [docs/architecture.svg](docs/architecture.svg)
- **Standing Instructions:** [AGENTS.md](AGENTS.md)
- **Test Runner:** Node.js native test runner (`node:test`, `node:assert`)
- **Total Test Suites:** 3 files, **23 tests passed, 0 failed** (executed in ~86ms)

---

## 📌 Tickets Summary & Individual Pull Requests

| Ticket | Scope & Modules | Root Cause & Resolution | Pull Request |
| :--- | :--- | :--- | :--- |
| **Ticket 1** | **Invoice & GST Reconciliation**<br>`lib/invoice.js` | Invoices computed GST on undiscounted gross totals ignoring HSN slabs (5%, 12%, 18%) and coupon caps.<br>**Fix:** Delegated invoice calculation to `pricing.computeTotals()`, aligning invoices with checkout. Reconciles all 8 orders with 0 mismatches. | **[PR #279](https://github.com/SampurnR/shopledger-legacy-workshop/pull/279)** |
| **Ticket 2** | **Order History Dates & Khata Settlements**<br>`lib/dates.js`, `lib/orders.js`, `lib/ledger.js` | Date comparisons used `DD/MM/YYYY` string comparisons breaking chronological order (#1004 & #1001 above #1008); Khata settlements added amounts as strings (`1200 + "500" = "1200500"`).<br>**Fix:** Normalized dates to `YYYY-MM-DD` and enforced numeric `Number(amount)` parsing in settlements. | **[PR #298](https://github.com/SampurnR/shopledger-legacy-workshop/pull/298)** |
| **Ticket 3** | **Inventory Stock Check & Cancellation Restock**<br>`lib/inventory.js` | Stock check `>=` boundary rejected selling the exact remaining unit (`DRY-001`, `SPC-001`); cancellation looked for `productId` instead of `id` and reduced `reserved` instead of restoring physical stock.<br>**Fix:** Changed check to `qty > sellable` and restored cancelled items to `p.stock`. | **[PR #292](https://github.com/SampurnR/shopledger-legacy-workshop/pull/292)** |

---

## 🧪 Comprehensive Test Suite (23/23 Passing)

Run all tests natively with zero configuration:
```bash
node --test
```

### Verified Test Results:
```text
✔ getSellableStock calculates sellable stock correctly excluding wholesale reservations (1.08ms)
✔ check passes when quantity is strictly less than sellable stock (0.57ms)
✔ check rejects when quantity exceeds sellable stock (0.10ms)
✔ FIX VERIFIED: check allows buying exact remaining sellable stock (last sellable unit) (0.08ms)
✔ reserve decrements physical stock for ordered items (0.13ms)
✔ FIX VERIFIED: release restores stock when line items have id property (0.08ms)
✔ FIX VERIFIED: release restores stock when line items have productId property (0.06ms)
✔ FIX VERIFIED: cancelling an order in orders.cancel restores product stock for all items (0.82ms)
✔ Order #1008: matches when all items are 18% HSN and no coupon applied (0.75ms)
✔ FIX VERIFIED: Order #1002 correctly resolves 5% HSN rate and applies SAVE10 discount proportionally (0.60ms)
✔ FIX VERIFIED: Order #1004 respects maxDiscount cap of 150 on SAVE10 and 5% HSN slab (0.09ms)
✔ FIX VERIFIED: Order #1001 correctly applies item-specific HSN rates (18% and 5%) without coupon (0.09ms)
✔ render produces HTML document without reconciliation warning when figures match (0.31ms)
✔ normalizeDateKey normalizes both DD/MM/YYYY and YYYY-MM-DD to YYYY-MM-DD for chronological comparison (1.11ms)
✔ dates.getFiscalYear correctly determines fiscal year with YYYY-MM-DD and DD/MM/YYYY (0.13ms)
✔ Order History sorts chronologically newest first (#1008 appears above #1004 and #1001) (0.57ms)
✔ orders.list date range filter accurately filters by date range (0.14ms)
✔ orders.list filters by search query matching customer or id (0.12ms)
✔ orders.list filters by order status (0.10ms)
✔ recording settlement with string amount converts to Number and updates balance correctly (0.89ms)
✔ ledger.recordSettlement with valid Number amount updates balance cleanly (0.12ms)
✔ ledger.recordSettlement rejects invalid settlement amounts (0.13ms)
✔ ledger.recordSettlement returns error if customer not found (0.15ms)
ℹ tests 23
ℹ suites 0
ℹ pass 23
ℹ fail 0
```

---

## ⚙️ Running Locally

```bash
npm install
npm start
# Server listens on http://localhost:3000 (or $env:PORT)
```
