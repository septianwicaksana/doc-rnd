---
type: machine
machine_id: 400001
machine_name: Manual Wash No.1
machine_type: Manual Clean
manufacturer: Manual
model: Wash
machine_no: 00
serial_no: TBD
category: Cleaning
technology: Manual Clean
country_of_origin: TBD
year_acquired: TBD
year_manufacture: TBD
location: TBD
axis_config: TBD
bar_capacity: TBD
status: active
person_in_charge: Fajar Sukatma
person_in_charge_2: Erlangga Pratama
---

# Manual Wash No.1

## Basic Information

| Field | Value |
| --- | --- |
| Machine ID | 400001 |
| Machine Name | Manual Wash No.1 |
| Machine Type | Manual Clean |
| Manufacturer | Manual |
| Model | Wash |
| Serial No. | TBD |
| Country of Origin | TBD |
| Year Acquired | TBD |
| Person in Charge | Fajar Sukatma |
| Person in Charge 2 | Erlangga Pratama |
| Status | active |

## Capability

| Feature | Value |
| --- | --- |
| Axis Config | TBD |
| Bar Capacity | TBD mm |
| Max RPM | TBD |
| Max Feed | TBD |
| Turret Positions | TBD |

## Tooling System

TBD

## Tool Position Map

TBD

## Compatible Operations

TBD

## Product History

> Gunakan Dataview untuk menampilkan Job Card terkait mesin ini:

```dataview
TABLE rd_order_no, product, status, created_date
FROM "02_RnD Job Cards"
WHERE type = "rnd_job_card" AND machine = "Manual Wash No.1"
SORT created_date DESC
```

## CNC Program History

TBD

## Common Issues

TBD

## Setup Notes

TBD

## Maintenance / Limitation Notes

TBD

## Related Job Cards

TBD

## Open Questions

| No. | Pertanyaan | Mengapa Penting | Status |
| ---: | --- | --- | --- |
| 1 | Axis configuration belum terdokumentasi | Dibutuhkan untuk kompatibilitas tool | Open |
| 2 | Bar capacity belum terdokumentasi | Dibutuhkan untuk menentukan ukuran material yang bisa diproses | Open |