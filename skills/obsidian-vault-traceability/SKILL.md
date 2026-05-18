---
skill_id: obsidian-vault-traceability
version: 1.0.0
status: active
purpose: Mengatur struktur vault Obsidian, konvensi folder, penamaan catatan, metadata YAML, internal link, kesiapan Dataview, dan traceability antar record R&D Manufacturing.
triggers:
  - review struktur vault
  - cek naming convention
  - cek atau buat YAML frontmatter
  - review internal link
  - buat atau review dashboard
  - review traceability record
  - audit kesiapan dokumentasi
  - folder atau file baru perlu ditempatkan
related_skills:
  - rnd-job-card-copilot
  - cutting-tool-database
  - cnc-program-revision-control
  - product-tooling-setup
---

# Skill: Obsidian Vault Traceability

## 1. Tujuan Skill

Skill ini memastikan bahwa setiap record R&D Manufacturing:

- Ditempatkan di folder yang benar.
- Diberi nama sesuai konvensi.
- Memiliki YAML frontmatter yang lengkap dan konsisten.
- Terhubung ke record terkait melalui Obsidian internal links.
- Dapat di-query menggunakan Dataview.
- Siap untuk audit traceability.

Skill ini **bukan** untuk membuat konten teknis record. Skill ini adalah penjaga konsistensi struktur dan traceability seluruh vault.

---

## 2. Kapan Menggunakan Skill Ini

Gunakan skill ini ketika:

- Membuat folder atau file baru di vault.
- Membuat note baru dari jenis apapun.
- Memeriksa apakah note sudah lengkap dan terhubung.
- Membuat atau memperbarui dashboard Dataview.
- Melakukan review traceability sebelum menutup Job Card.
- Ada pertanyaan tentang di mana menyimpan suatu record.
- Ada pertanyaan tentang nama file yang benar.
- Ada pertanyaan tentang field YAML yang diperlukan.
- Melakukan audit kesiapan dokumentasi.

---

## 3. Aturan Struktur Folder

Gunakan struktur berikut. Jangan buat folder di luar struktur ini tanpa alasan jelas.

```
00_Dashboard/
01_Master Data/
01_Master Data/Machines/
01_Master Data/Products/
01_Master Data/Product Families/
01_Master Data/Materials/
01_Master Data/Cutting Tools/
01_Master Data/Tool Holders/
01_Master Data/Tool Assemblies/
01_Master Data/Operations/
02_RnD Job Cards/
03_CNC Programs/
04_Inspection Records/
05_Tool Wear Records/
06_Deviations and Issues/
07_Templates/
08_Attachments/
08_Attachments/Drawings/
08_Attachments/Photos/
08_Attachments/Videos/
08_Attachments/Machine Setup/
09_References/
10_Archive/
```

### Pemetaan Tipe Note ke Folder

| Tipe Note (`type`) | Folder |
| --- | --- |
| `machine` | `01_Master Data/Machines/` |
| `product` | `01_Master Data/Products/` |
| `product_family` | `01_Master Data/Product Families/` |
| `material` | `01_Master Data/Materials/` |
| `cutting_tool` | `01_Master Data/Cutting Tools/` |
| `tool_holder` | `01_Master Data/Tool Holders/` |
| `tool_assembly` | `01_Master Data/Tool Assemblies/` |
| `product_tooling_setup` | `01_Master Data/` (root, atau subfolder khusus jika volume tinggi) |
| `rnd_job_card` | `02_RnD Job Cards/` |
| `cnc_program` | `03_CNC Programs/` |
| `cnc_revision` | `03_CNC Programs/` |
| `inspection_record` | `04_Inspection Records/` |
| `tool_wear_record` | `05_Tool Wear Records/` |
| `deviation_issue` | `06_Deviations and Issues/` |
| `catalog_research` | `09_References/` |
| `dashboard` | `00_Dashboard/` |
| `template` | `07_Templates/` |
| `reference` | `09_References/` |

### Aturan Folder

- Jangan simpan note di root vault kecuali `README.md` atau `CLAUDE.md`.
- Jangan buat subfolder baru di dalam folder yang sudah terdefinisi tanpa instruksi eksplisit.
- Record yang sudah obsolete, superseded, atau cancelled dipindahkan ke `10_Archive/`.
- Lampiran (gambar, drawing, video) disimpan di subfolder `08_Attachments/` yang sesuai.

---

## 4. Aturan Naming Convention

### R&D Job Card

```
RDYYYYMMDDNNN - Nama Produk - Nama Mesin.md
```

Contoh:
```
RD20260605001 - Emerald 6.5 mm Poly Screw L45 - CITIZEN A20-3F7 No.1.md
```

### Product Master

```
Nama Produk - Material - Panjang atau Varian.md
```

Contoh:
```
Emerald 6.5 mm Poly Screw - Ti6Al4V ELI - L45.md
```

### Machine Master

```
Brand Model Mesin - Nomor Mesin.md
```

Contoh:
```
CITIZEN A20-3F7 - No.1.md
```

### Cutting Tool Master

```
Brand Catalog-No Deskripsi Singkat.md
```

Contoh:
```
SECO DCMT11T302F1 CP500 Turning Insert.md
```

### Product Tooling Setup

```
Nama Produk - Nama Mesin - Tooling Setup.md
```

Contoh:
```
Emerald 6.5 mm Poly Screw L45 - CITIZEN A20-3F7 No.1 - Tooling Setup.md
```

### CNC Program Master

```
Program NOMORPROGRAM - Nama Mesin.md
```

Contoh:
```
Program 2513 - CITIZEN A20-3F7 No.1.md
```

### CNC Revision Record

```
Program NOMORPROGRAM - REVXX - Alasan Singkat.md
```

Contoh:
```
Program 2513 - REV01 - Reduce Thread Burr.md
```

### Inspection Record

```
IR-RDORDERNO-TNOMORTRIAL.md
```

Contoh:
```
IR-RD20260605001-T01.md
```

### Issue / Deviation Record

```
ISS-RDORDERNO-TNOMORTRIAL-Isu Singkat.md
```

Contoh:
```
ISS-RD20260605001-T01-Thread Burr.md
```

### Aturan Penamaan Umum

- Gunakan spasi (bukan underscore atau hyphen) dalam nama produk, mesin, dan deskripsi.
- Huruf kapital pada setiap kata (Title Case) untuk nama produk dan mesin.
- Nomor selalu menggunakan format yang konsisten (RD + 8 digit tanggal + 3 digit urut).
- Jangan singkat nama mesin atau produk secara bebas — gunakan nama resmi.
- Jika ada keraguan nama, tanyakan ke user sebelum membuat file.

---

## 5. Aturan YAML Frontmatter

### Prinsip Umum

- Setiap note **wajib** memiliki YAML frontmatter.
- Semua field yang belum diketahui diisi dengan `TBD`.
- Jangan meninggalkan field kosong (nilai kosong `""` atau tanpa nilai).
- Jangan menambahkan field yang tidak ada di schema standar tanpa alasan.
- Field list (array) ditulis dengan format:

```yaml
field_name:
  - nilai1
  - nilai2
```

Bukan:

```yaml
field_name: [nilai1, nilai2]
```

### Nilai `type` yang Valid

```
machine
product
product_family
material
cutting_tool
tool_holder
tool_assembly
product_tooling_setup
rnd_job_card
cnc_program
cnc_revision
inspection_record
tool_wear_record
deviation_issue
catalog_research
dashboard
template
reference
```

Gunakan nilai ini secara konsisten. Jangan buat nilai `type` baru.

### Field Wajib untuk Semua Note

```yaml
---
type: <nilai_type>
---
```

Semua tipe note lain menambahkan field sesuai schema masing-masing yang didefinisikan di CLAUDE.md.

### Aturan Field Tanggal

- Format tanggal: `YYYY-MM-DD`
- Contoh: `2026-06-05`
- Jika tanggal belum diketahui: `TBD`

### Aturan Field Status

Gunakan nilai status yang terkontrol sesuai tipe note. Lihat CLAUDE.md untuk daftar lengkap per tipe note.

---

## 6. Aturan Internal Link

### Format Link

Gunakan Obsidian internal link untuk semua referensi antar note:

```markdown
[[Nama Note]]
```

Jangan gunakan path relatif atau URL untuk menghubungkan note internal.

### Kapan Menggunakan Internal Link

- Setiap R&D Job Card harus memiliki link ke: Product Master, Machine Master, Product Tooling Setup, CNC Program Master.
- Setiap Inspection Record harus memiliki link ke: R&D Job Card terkait.
- Setiap CNC Revision Record harus memiliki link ke: CNC Program Master dan R&D Job Card terkait.
- Setiap Issue / Deviation Record harus memiliki link ke: R&D Job Card, CNC Revision (jika ada), Cutting Tool (jika relevan).
- Setiap Product Tooling Setup harus memiliki link ke: setiap Cutting Tool yang digunakan, Machine Master, Product Master, dan source Job Card.

### Aturan Internal Link

- Nama dalam `[[...]]` harus sama persis dengan nama file (tanpa ekstensi `.md`).
- Jika note yang dirujuk belum ada, tetap tulis link-nya — Obsidian akan menampilkan sebagai broken link sampai note dibuat.
- Jangan hapus broken link hanya karena note belum ada. Broken link adalah pengingat bahwa record terkait belum dibuat.
- Jangan duplikasi link yang sama dalam satu section.

---

## 7. Aturan Traceability

### Prinsip Traceability

Setiap record harus dapat ditelusuri dari dan ke record terkait. Traceability berjalan dua arah:

- **Forward traceability**: dari produk → Job Card → trial → inspeksi → keputusan.
- **Backward traceability**: dari hasil inspeksi → Job Card → produk → mesin → program CNC.

### Matriks Traceability Minimum

| Record | Harus Link ke |
| --- | --- |
| R&D Job Card | Product Master, Machine Master, Product Tooling Setup, CNC Program Master, Inspection Record(s), Issue Record(s) |
| Inspection Record | R&D Job Card |
| CNC Revision | CNC Program Master, R&D Job Card |
| Issue / Deviation | R&D Job Card, CNC Revision (jika ada), Cutting Tool (jika relevan) |
| Product Tooling Setup | Product Master, Machine Master, Cutting Tool(s), CNC Program, source Job Card |
| Cutting Tool Master | (opsional) Job Card pertama kali tool digunakan |

### Cara Verifikasi Traceability

Saat mereview traceability suatu record, periksa:

1. Apakah semua link internal sudah ada?
2. Apakah ada link yang broken (note belum dibuat)?
3. Apakah field YAML yang merujuk record lain sudah diisi (bukan `TBD`) jika record sudah ada?
4. Apakah record terkait juga memiliki back-link ke record ini?

---

## 8. Dashboard dan Dataview

### Prinsip Dashboard

- Dashboard disimpan di `00_Dashboard/`.
- Dashboard menggunakan Dataview query untuk menampilkan data dinamis.
- Jangan hardcode daftar note di dashboard — gunakan query.

### Dataview Query Dasar

**Semua R&D Job Card aktif:**

````markdown
```dataview
TABLE rd_order_no, product, machine, status, created_date
FROM "02_RnD Job Cards"
WHERE type = "rnd_job_card" AND status != "closed" AND status != "cancelled"
SORT created_date DESC
```
````

**Job Card yang perlu tindakan:**

````markdown
```dataview
TABLE rd_order_no, product, machine, status
FROM "02_RnD Job Cards"
WHERE type = "rnd_job_card" AND (status = "need_revision" OR status = "retest" OR status = "draft")
SORT created_date ASC
```
````

**Semua isu terbuka:**

````markdown
```dataview
TABLE issue_id, job_card, product, issue_type, severity, status
FROM "06_Deviations and Issues"
WHERE type = "deviation_issue" AND status = "open"
SORT detected_date ASC
```
````

**Cutting tool dengan status under_trial atau pending_review:**

````markdown
```dataview
TABLE tool_id, catalog_no, brand, category, verification_status
FROM "01_Master Data/Cutting Tools"
WHERE type = "cutting_tool" AND (status = "under_trial" OR verification_status = "pending_review")
SORT brand ASC
```
````

**CNC Revision terbaru:**

````markdown
```dataview
TABLE program_no, revision, product, machine, reason_for_change, change_date
FROM "03_CNC Programs"
WHERE type = "cnc_revision"
SORT change_date DESC
LIMIT 20
```
````

### Aturan Dashboard

- Setiap dashboard note harus memiliki field `type: dashboard` dan `dashboard_for` di YAML.
- Jika Dataview tidak tersedia (plugin belum aktif), beri catatan di dashboard bahwa query membutuhkan plugin Dataview.
- Jangan buat dashboard manual (daftar link statis) kecuali sebagai fallback sementara.

---

## 9. Aturan Pencegahan Duplikasi

Sebelum membuat note baru, periksa apakah sudah ada note dengan identitas yang sama.

### Cara Cek Duplikasi

| Tipe Note | Field Identitas Unik |
| --- | --- |
| `rnd_job_card` | `rd_order_no` |
| `product` | `item_no` |
| `machine` | `machine_id` atau `machine_name` + `machine_no` |
| `cutting_tool` | `item_no` atau `catalog_no` + `brand` |
| `tool_holder` | `item_no` atau `catalog_no` + `brand` |
| `cnc_program` | `program_no` + `machine` |
| `cnc_revision` | `program_no` + `revision` |
| `inspection_record` | `rd_order_no` + `trial_no` |
| `deviation_issue` | `issue_id` |
| `product_tooling_setup` | `product` + `machine` + `operation` |

### Aturan Duplikasi

- Jika ditemukan note dengan identitas sama, **jangan buat note baru**. Update note yang sudah ada.
- Jika catalog number sama tapi brand berbeda, konfirmasi ke user sebelum memutuskan duplikat atau bukan.
- Jika ada keraguan, buat `catalog_research` note terlebih dahulu daripada langsung membuat `cutting_tool` note baru.
- Laporkan ke user jika ditemukan potensi duplikat.

---

## 10. Penanganan Open Questions

Setiap note yang memiliki informasi belum lengkap harus menyertakan section Open Questions.

### Format Open Questions

```markdown
## Open Questions

| No. | Pertanyaan | Mengapa Penting | Status |
| ---: | --- | --- | --- |
| 1 | TBD | TBD | Open |
```

### Aturan Open Questions

- Tambahkan ke Open Questions setiap informasi yang dibutuhkan tapi belum tersedia.
- Gunakan `TBD` di field YAML untuk informasi yang hilang, dan dokumentasikan alasannya di Open Questions.
- Jangan blokir pembuatan record hanya karena ada informasi yang hilang — gunakan `TBD` dan lanjutkan.
- Tandai status `Resolved` dan isi jawabannya jika pertanyaan sudah terjawab.
- Jangan hapus pertanyaan yang sudah resolved — ubah statusnya saja untuk menjaga history.

---

## 11. Checklist Validasi

Gunakan checklist ini saat mereview atau menyelesaikan note apapun.

### Checklist Struktur

```
[ ] Note disimpan di folder yang benar sesuai tipe.
[ ] Nama file mengikuti naming convention yang benar.
[ ] YAML frontmatter ada dan diawali dengan field `type`.
[ ] Semua field YAML wajib terisi (atau TBD jika belum diketahui).
[ ] Tidak ada field YAML yang kosong tanpa nilai.
[ ] Format tanggal YYYY-MM-DD digunakan di semua field tanggal.
[ ] Nilai `type` menggunakan nilai yang terkontrol.
[ ] Nilai `status` menggunakan nilai yang terkontrol sesuai tipe note.
```

### Checklist Traceability

```
[ ] Semua internal link ke record terkait sudah ditambahkan.
[ ] Field YAML yang merujuk record lain sudah diisi dengan nama note yang benar.
[ ] Tidak ada record yang berdiri sendiri tanpa link ke record terkait (kecuali master data awal).
[ ] Back-link dari record terkait sudah ada atau sudah dijadwalkan.
```

### Checklist Kelengkapan Konten

```
[ ] Semua section yang diperlukan untuk tipe note ini sudah ada.
[ ] Open Questions section ada jika ada informasi yang hilang.
[ ] Tidak ada data rekayasa yang diklaim tanpa bukti.
[ ] TBD digunakan untuk semua informasi yang belum dikonfirmasi.
```

---

## 12. Format Respons Wajib Setelah Menggunakan Skill Ini

Setelah menyelesaikan tugas yang melibatkan skill ini, Claude **wajib** melaporkan:

```
### Hasil Vault Traceability

**Tindakan yang dilakukan:**
- [Daftar note yang dibuat, diperbarui, atau diperiksa]

**Validasi struktur:**
- Folder: [sesuai / tidak sesuai — detail jika ada masalah]
- Naming: [sesuai / tidak sesuai — detail jika ada masalah]
- YAML: [lengkap / ada field TBD — sebutkan field apa]
- Internal links: [lengkap / ada broken link — sebutkan]
- Traceability: [lengkap / ada gap — sebutkan]

**Open Questions:**
- [Daftar pertanyaan yang ditambahkan, jika ada]

**Git:**
- Files changed: [daftar file]
- Commit: [hash]
- Branch: [nama branch]
- Push: [berhasil / gagal — detail jika gagal]

**Langkah selanjutnya:**
- [Rekomendasi tindakan berikutnya]
```

Jangan klaim traceability lengkap jika masih ada broken link atau field `TBD` pada field kritis (seperti `rd_order_no`, `item_no`, `machine`, `product`).
