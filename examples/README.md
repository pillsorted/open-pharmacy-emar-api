All IDs, codes and people in these examples are made up. dm+d codes are shown as zeros; use real codes from the current NHSBSA dm+d release.

| File | What it shows |
| --- | --- |
| [delivery.json](delivery.json) | A routine repeat delivery for a linked resident: one pouched medicine and one original pack, both unchanged |
| [delivery-new-and-changed.json](delivery-new-and-changed.json) | A delivery with a changed dose (one pouch line per time slot) and a new medicine |
| [medication-item-regular-plus-prn.json](medication-item-regular-plus-prn.json) | One medication item with a regular and a PRN schedule sharing one stock balance |
| [event.json](event.json) | A `delivery.checked_in` webhook |

Every delivery line carries the full medication item, whatever the value of `change`. Quantities are
always counted in the dose unit (`unit`, e.g. tablet), never in packs; `amppCode` identifies the pack.
