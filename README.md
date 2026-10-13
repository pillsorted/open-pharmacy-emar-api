# Open Pharmacy–eMAR API

An open, vendor-neutral API for sending medicines from a pharmacy to a care home's electronic MAR
(eMAR), and getting the check-in result back.

Today every pharmacy–eMAR pair builds its own one-off integration. Data flows one way, care staff
re-key PRN and variable doses by hand, and nobody knows whether a delivery arrived complete. This
spec is our opinionated answer, published so any eMAR or pharmacy can implement it and improve it.

**Status:** 0.1 draft. Breaking changes are still possible.

## How it works

- **The eMAR hosts the API; the pharmacy calls it.** The eMAR pushes events back by webhook.
- **Phase 1, check-in:** the pharmacy sends each delivery. Care staff confirm each line as received,
  short, damaged or not received. New medicines are added to the MAR, changed ones updated,
  discontinued ones stopped, and stock is booked in.
- **Phase 2, ordering:** the home asks for a reorder. This is a request for a prescription, not a
  sales order.

## The ideas that matter

1. **Stock belongs to the medicine, not the dose schedule.** "One every morning plus one extra when
   needed" is one item with two schedules and one stock balance. See
   [docs/data-model.md](docs/data-model.md).
2. **Stock is a ledger, not a number.** Every movement in or out has a reason; the balance is in minus
   out. A stock count is just one more entry.
3. **The NHS number finds a resident; it is never the link.** NHS numbers can change. After the first
   match, both sides store each other's own IDs and use only those.
   See [docs/matching-and-onboarding.md](docs/matching-and-onboarding.md).
4. **Structured doses, with the label text alongside.** Regular, PRN (with interval and 24-hour
   maximum), variable, reducing, interval, one-off and topical doses all have fields.
5. **Every write is idempotent and every change sends an event.** No polling, no "did you get it?"
   phone calls.

## Files

| File | What it is |
| --- | --- |
| [openapi.yaml](openapi.yaml) | The API (OpenAPI 3.1), including webhooks |
| [docs/data-model.md](docs/data-model.md) | Medication items, dosage types, the stock ledger |
| [docs/check-in.md](docs/check-in.md) | The delivery and check-in flow |
| [docs/matching-and-onboarding.md](docs/matching-and-onboarding.md) | Resident matching and connecting a home |
| [docs/design-rationale.md](docs/design-rationale.md) | Where each design choice comes from |
| [examples/](examples/) | Example payloads |

Try it against a mock server:

```sh
npx @stoplight/prism-cli mock openapi.yaml
```

## Contributing

Issues and pull requests are welcome from eMAR suppliers, pharmacies, care providers and anyone
else. Read [CONTRIBUTING.md](CONTRIBUTING.md) first.

## Maintainers

Maintained by [PillSorted](https://pillsorted.com), a UK online and care-home pharmacy.
Licensed under [Apache 2.0](LICENSE).
