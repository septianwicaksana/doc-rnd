# TDM R&D Manufacturing Documentation

Obsidian-based engineering knowledge management system untuk R&D Job Card lifecycle, cutting tool database, CNC program revision control, dan traceability dokumentasi manufaktur implan ortopedi.

## Folder Structure

| Folder | Isi |
| --- | --- |
| `00_Dashboard/` | Dataview dashboard dan halaman index |
| `01_Master Data/` | Master record stabil: mesin, produk, material, cutting tool, tool holder, tool assembly, operasi |
| `02_RnD Job Cards/` | R&D Job Card aktif dan selesai |
| `03_CNC Programs/` | Catatan program CNC, revision log, dan referensi |
| `04_Inspection Records/` | Hasil inspeksi dan pengukuran |
| `05_Tool Wear Records/` | Catatan keausan tool, tool life, dan performa |
| `06_Deviations and Issues/` | Isu trial, deviasi, dan tindakan korektif |
| `07_Templates/` | Template Markdown |
| `08_Attachments/` | Drawing, foto, video, dan file setup mesin |
| `09_References/` | Referensi eksternal, catatan standar, referensi katalog |
| `10_Archive/` | Record yang sudah obsolete, superseded, atau closed |
| `skills/` | Skill files untuk Claude Code co-pilot workflow |

## Skill Routing

| Skill | Digunakan untuk |
| --- | --- |
| `rnd-job-card-copilot` | Buat, update, review, dan close R&D Job Card |
| `cutting-tool-database` | Tambah atau update master data cutting tool |
| `cutting-tool-catalog-intelligence` | Identifikasi dan klasifikasi tool dari catalog number |
| `product-tooling-setup` | Definisi dan review tooling setup per produk |
| `cnc-program-revision-control` | Catat dan review revisi program CNC |
| `obsidian-vault-traceability` | Review struktur vault, naming, YAML, dan traceability |

## Traceability Alignment

Dokumentasi ini mendukung praktik sesuai **ISO 13485-style traceability** untuk medical device manufacturing — mencakup trial record, verifikasi, dan engineering change control.
