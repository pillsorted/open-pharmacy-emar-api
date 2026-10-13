# Resident matching and onboarding

## Matching residents

The NHS number is a way to find a resident the first time, never the link itself: it can change
through an official process. After the first match, both sides store each other's own IDs
(`residentId` ↔ `pharmacyPatientId`) and every call uses those. Both sides keep the history of NHS
numbers, so a match on an old number still finds the resident.

When an NHS number changes, the eMAR sends `resident.nhs_number_changed` with the old and new
numbers. The link is not affected.

| Order | Key | When | Rule |
| --- | --- | --- | --- |
| 1 | Linked ID pair (`residentId` ↔ `pharmacyPatientId`) | Every call after the first link | Survives a move between homes on the same eMAR |
| 2 | NHS number, current or previous | First link | Exact match on a valid 10-digit number |
| 3 | Family name + date of birth | Prescription arrives before the resident exists | Creates a `PENDING` link that care staff confirm; never automatic |

Events keep links current: `resident.moved`, `resident.nhs_number_changed`, `resident.left`.

## Connecting a home

No onboarding-specific API is needed.

1. **Grant access.** In the eMAR's settings, the home turns the pharmacy on. The home then appears in
   the pharmacy's `GET /homes`, with its CQC location ID.
2. **Link the home.** The pharmacy matches the CQC location ID to its own record and stores the eMAR's
   `homeId`. Unmatched homes go to a person to link.
3. **Link residents.** On NHS number, as above. This works whichever system held the resident first.
4. **Go live.** The pharmacy picks a start date. The first delivery after it carries every item in
   full as `NEW`, which also fills the MAR for a home new to the eMAR. Opening stock is counted once
   and booked as `CARRIED_FORWARD`.
5. **Stop.** The home turns access off. Further calls are refused with 403.

Consent to change pharmacy, nomination changes and clinical review stay outside the API.
