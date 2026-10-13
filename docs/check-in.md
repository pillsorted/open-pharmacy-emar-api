# Delivery and check-in

1. **Packed.** The pharmacy calls `PUT /homes/{homeId}/deliveries/{pharmacyDeliveryId}`. Each line names
   the resident link, the item, the quantity, batch and expiry. Lines with `change` = `NEW` or
   `DOSE_CHANGED` carry the full item and its schedules.
2. **Dispatched.** The pharmacy calls `PATCH` with the status and tracking link, and again on proof of
   delivery.
3. **Check-in page.** Care staff see the delivery by resident, then by pouch time slot or pack, with
   the photo and the schedule.
4. **Confirm.** Each line is marked `RECEIVED`, `SHORT` (with quantity), `DAMAGED` or `NOT_RECEIVED`.
   Staff may override the start date, administration times and first-dose date. Overrides are
   recorded and sent as `medication_item.overridden`.
5. **Apply.** On confirm, the eMAR applies item changes, writes `RECEIVED` ledger entries and sends
   `delivery.checked_in`.
6. **Unmatched.** Lines for residents with no confirmed link wait in an unmatched queue and are not
   applied.

**Expected but not sent:** an active item with regular schedules and no line on a cycle delivery is
shown to the home before the cycle starts, so it can be queried.

**Discontinued:** the pharmacy calls `.../discontinue` with a date and reason. The eMAR stops the item
from that date.

See [examples/delivery.json](../examples/delivery.json) and [examples/event.json](../examples/event.json).

## Phase 2: ordering

A reorder asks the pharmacy to request a prescription; the prescription is the order. The eMAR sends
`reorder.requested` with the resident and the items (item reference or dm+d code and name). Quantity
and frequency come from the prescriber. The pharmacy reports progress per item with
`PATCH /reorders/{requestId}`. An item already open on another request is shown as already requested.
