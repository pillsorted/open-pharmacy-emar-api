# Design rationale

Where each design choice comes from. Every part of this spec traces to one of four sources:

- **Public standards:** NHS dm+d, NHS number, CQC location ID, OAuth 2.0, OpenAPI 3.1, RFC 9457, ISO 8601.
- **General API practice:** idempotent `PUT`, ETags, signed webhooks, append-only ledgers.
- **PillSorted's own dispensing model:** one prescription item per medicine with one quantity, and a
  dosing type of standard, variable, PRN, or standard plus PRN.
- **Care-home practice:** check-in of cycle deliveries, MAR charts, controlled drug registers, PRN protocols.

| Choice | Source | Why |
| --- | --- | --- |
| One medication item per medicine, many dose schedules | PillSorted's dispensing model | Regular and PRN doses share one stock, so counts stay right |
| `dosageType` values | Care-home MAR practice; PillSorted's dosing types | Covers how MAR charts are actually written |
| PRN `minIntervalHours`, `maxPer24h`, `indication` | Care-home PRN protocols | These are the facts staff check before giving a PRN dose |
| Stock as a ledger with reasons | Accounting and ERP practice | Balances can be explained; counts become adjustments, not resets |
| dm+d VMP, AMP and AMPP codes | NHS dm+d | The national medicines dictionary; AMPP identifies the pack supplied |
| `doseUnit` from the dm+d unit of measure | NHS dm+d | A dose unit is not the same as the product form |
| NHS number, then linked IDs | NHS number standard; general practice | NHS numbers can change; linked IDs do not |
| CQC location ID for homes | CQC register | A public, unique ID for every registered care location |
| `PUT` on the pharmacy's own references | General API practice | Retries and corrections never create duplicates |
| Webhooks with `eventId` and HMAC signature | General API practice | No polling; duplicates are safe to drop |
| Refuse an unreadable dose | Patient safety | A wrong dose on a MAR is worse than a rejected line |
| Ordering = request for a prescription | UK prescribing law | Only a prescriber can authorise supply of a POM |

Contributions should add a row here when they add a design choice.
