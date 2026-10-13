# Data model

## Medication item: one medicine, one stock balance

Each resident has one **medication item** per medicine. The item owns the stock balance and holds one
or more **dose schedules**. A regular dose and an as-required (PRN) dose of the same medicine are two
schedules on one item. Holding them as two items splits the stock and the counts drift.

| Entity | What it is | Key fields |
| --- | --- | --- |
| Medication item | One medicine for one resident; owns the stock | `pharmacyItemRef`, `vmpCode`, `ampCode`, `name`, `form`, `strength`, `route`, `status`, `supplyFormat`, `flags`, `labelText`, `imageUrl` |
| Dose schedule | One way the item is given; 1 to n per item | `scheduleId`, `dosageType`, dose fields |
| Delivery line | One supplied quantity of an item | `deliveryLineId`, `amppCode`, `quantity`, `unit`, `batch`, `expiry` |
| Ledger entry | One stock movement in or out | `direction`, `quantity`, `reason`, `occurredAt`, `sourceRef` |

## Dosage types

| `dosageType` | Meaning | Required fields | Example |
| --- | --- | --- | --- |
| `REGULAR` | Fixed dose at fixed times | `times[]`, `doseQuantity`, `doseUnit` | 1 tablet at 08:00 and 20:00 |
| `PRN` | When required, for a stated reason | `doseQuantity` or `doseMin`/`doseMax`, `minIntervalHours`, `maxPer24h`, `indication` | 1 tablet for headache, 4 hours apart, max 4 in 24 hours |
| `VARIABLE` | A range at fixed times; staff choose and record | `times[]`, `doseMin`, `doseMax`, `doseUnit` | 1 to 2 tablets at 22:00 |
| `REDUCING` | Dose set per date range | `steps[]` | 30 mg for 5 days, then 20 mg for 5 days |
| `INTERVAL` | Every n days or on set weekdays | `everyNDays` or `weekdays[]`, `times[]`, `doseQuantity` | 1 tablet every Monday; 1 patch every 3 days |
| `ONCE` | A single dose | `dueAt`, `doseQuantity` | Stat dose on arrival |
| `APPLY` | Topical or other non-counted dose | `times[]` or PRN fields, `site`, `directions` | Apply thinly to both legs twice a day |

Notes:

- `doseUnit` is the dm+d unit of measure, separate from the product form.
- `labelText` is the dispensing label word for word. The structured schedule drives the MAR; the label
  is shown beside it so staff can spot a mismatch.
- `maxPer24hAcrossItem: true` means the 24-hour maximum counts doses from every schedule on the item.
- A dose quantity that cannot be read is refused with an error. It is never defaulted to 0.

See [examples/medication-item-regular-plus-prn.json](../examples/medication-item-regular-plus-prn.json).

## Stock ledger

The balance is the sum of ledger entries. Entries are append-only; a mistake is fixed with a reversing
entry.

| `reason` | Direction | Created by |
| --- | --- | --- |
| `RECEIVED` | In | Check-in of a delivery line |
| `CARRIED_FORWARD` | In | Opening stock at go-live or cycle start |
| `BROUGHT_IN` | In | Resident arrives with own medicines, or hospital discharge |
| `ADMINISTERED` | Out | Any schedule on the item, regular or PRN |
| `REFUSED_DESTROYED` | Out | Dose prepared but refused or dropped |
| `RETURNED_TO_PHARMACY` | Out | Discontinued, expired or excess stock |
| `EXPIRED` | Out | Past expiry, awaiting return |
| `SENT_WITH_RESIDENT` | Out | Hospital stay, leave, transfer |
| `COUNT_ADJUSTMENT` | In or out | Stock count difference, with a note |

Controlled drugs keep their own register as well; the ledger does not replace it.
