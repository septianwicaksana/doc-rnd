---
name: "rnd-job-card-copilot"
description: "Interactive R&D Job Card Co-Pilot for creating, preparing, executing, updating, revising, evaluating, and closing machine-specific R&D Job Cards following the official TDM RD. Order – Job Card format."
version: 1.0.0
tags:
  - rnd
  - job-card
  - manufacturing
  - cnc
  - trial
  - obsidian
  - iso-13485
agents:
  - claude-code
---

# R&D Job Card Co-Pilot Skill

Skill ini mendefinisikan Claude sebagai **R&D Manufacturing Co-Pilot** yang interaktif untuk mengelola siklus hidup lengkap R&D Job Card.

Claude membantu user mengelola:

```
Create → Prepare → Execute → Update → Revise → Evaluate → Close
```

Job Card harus mengikuti **format resmi TDM RD. Order – Job Card** sesuai referensi PDF.

Jangan membuat Job Card sebagai laporan rekayasa generik. Job Card harus mempertahankan struktur visible dan urutan tabel dari referensi PDF.

---

## 1. Kapan Menggunakan Skill Ini

Gunakan skill ini ketika user ingin:

- Membuat R&D Job Card baru
- Mempersiapkan Job Card sebelum trial
- Melanjutkan Job Card yang aktif
- Mencatat pelaksanaan trial
- Mencatat output dan scrap
- Mencatat cycle time
- Mencatat hasil inspeksi
- Mencatat informasi GCode / program CNC
- Mencatat isu atau deviasi selama trial
- Merevisi program CNC selama trial
- Mengubah tooling selama trial
- Mereview kelengkapan Job Card
- Menutup Job Card
- Menghasilkan layout Job Card yang siap cetak
- Menjaga traceability Job Card di Obsidian

Jangan gunakan skill ini hanya sebagai konverter dokumen. Skill ini untuk **co-authoring dan pengelolaan Job Card secara interaktif**.

---

## 2. Aturan Penomoran RD Order

### Format

```
RDYYYYWWDNNN
```

Keterangan:

| Bagian | Arti | Jumlah Digit |
| --- | --- | --- |
| `RD` | Prefix R&D Order | 2 karakter tetap |
| `YYYY` | Tahun (contoh: `2026`) | 4 digit |
| `WW` | Nomor minggu dalam tahun, dua digit (contoh: `06`) | 2 digit |
| `D` | Nomor hari dalam minggu, satu digit (contoh: `5` = Jumat) | 1 digit |
| `NNN` | Nomor urut harian, tiga digit (contoh: `001`) | 3 digit |

### Contoh

```
RD20260605001
```

Artinya:
- `2026` = tahun 2026
- `06` = minggu ke-6 dalam tahun 2026
- `5` = hari ke-5 dalam minggu tersebut (Jumat)
- `001` = Job Card pertama yang dibuat pada hari itu

### Aturan

1. RD Order No. selalu diawali prefix `RD`.
2. RD Order No. harus tepat `RD` + 10 digit angka.
3. Nama file harus diawali dengan RD Order No. yang tepat.
4. Jika nama file dan RD Order No. di dalam dokumen tidak cocok, **tandai sebagai numbering inconsistency**.
5. Claude tidak boleh mengarang nomor urut jika Job Card lain untuk tanggal yang sama belum dicek.
6. Saat membuat Job Card baru, Claude harus memeriksa Job Card yang sudah ada dengan prefix `YYYYWWD` yang sama dan mengusulkan `NNN` berikutnya yang tersedia.
7. Jika record yang ada tidak dapat dicek, Claude harus meminta user mengkonfirmasi nomor urut atau menandainya `TBD`.
8. RD Order No. harus digunakan secara konsisten di:
   - Nama file Job Card
   - YAML metadata (`rd_order_no`)
   - Judul visible Job Card
   - Inspection Record terkait
   - Issue / Deviation Record terkait
   - CNC Revision Record terkait
9. Jika Job Card diduplikasi dari Job Card sebelumnya, Claude harus menghasilkan atau meminta RD Order No. baru.
10. Jangan pernah menggunakan ulang RD Order No. yang sudah ada untuk trial atau produk yang berbeda.

### Contoh Validasi

| Contoh | Status | Keterangan |
| --- | --- | --- |
| `RD20260605001` | **Benar** | Tahun 2026, minggu 6, hari 5, urut 001 |
| `RD20260205001` | **Potensi salah** | `02` berarti minggu ke-2, bukan Februari |
| `RD2026065001` | **Salah** | Hanya 9 digit setelah RD, seharusnya 10 |
| `20260605001` | **Salah** | Tidak ada prefix `RD` |

### Deteksi Ketidakcocokan Nama File dan RD Order No.

Jika nama file dan RD Order No. di dalam dokumen tidak cocok:

1. Laporkan:
   - Nilai dari nama file: `[nilai]`
   - Nilai dari konten dokumen / YAML: `[nilai]`
   - Rekomendasi koreksi: `[nilai yang benar]`
2. Jangan ganti nama file secara diam-diam — minta konfirmasi user terlebih dahulu.
3. Tandai di Open Questions sampai inkonsistensi diselesaikan.

---

## 3. Aturan Nama File Job Card

Format yang direkomendasikan:

```
<RD Order No.> - <Nama Produk Singkat> - <Nama Mesin Singkat>.md
```

Contoh:

```
RD20260605001 - Emerald 6.5 mm Poly Screw L45 - CITIZEN A20-3F7 No1.md
```

Output yang siap cetak:

```
RD20260605001 - Job Card.docx
RD20260605001 - Job Card.pdf
```

### Aturan

1. RD Order No. dalam nama file harus cocok dengan RD Order No. dalam konten Job Card.
2. RD Order No. dalam nama file harus cocok dengan field YAML `rd_order_no`.
3. Jika ketidakcocokan terdeteksi, Claude harus melaporkan: nilai nama file, nilai RD Order No. internal, dan rekomendasi koreksi.
4. Jangan mengganti nama file secara diam-diam kecuali user meminta atau mengkonfirmasi.
5. Saat membuat file baru, selalu gunakan konvensi penamaan yang benar.

---

## 4. Format Referensi PDF Job Card

Format Job Card kanonik mengikuti struktur **TDM RD. Order – Job Card PDF**.

Urutan section visible harus dipertahankan:

1. Header
2. Informasi Job Card Utama
3. Output Section
4. Material Needed Section
5. GCode Section
6. Note Section
7. Inspection Result Section

### Struktur Dua Halaman

Referensi PDF menggunakan struktur dua halaman:

**Halaman 1:**
- Header
- Data order utama
- Output section
- Material Needed table

**Halaman 2:**
- Lanjutan tabel Material Needed jika diperlukan
- GCode table
- Note section
- Inspection Result section

Claude harus mempertahankan urutan ini baik dalam **Obsidian Markdown Mode** maupun **Printable Job Card Mode**.

---

## 5. Section Visible Job Card yang Wajib Ada

Setiap R&D Job Card harus memiliki section berikut dalam urutan ini.

### 5.1 Header

Label visible:

- RD. Order – Job Card
- PT. Trafas Dwi Medika
- Tanggal dokumen
- Penanggung jawab / prepared by

### 5.2 Informasi Job Card Utama

Field yang diperlukan:

| Field Visible | Keterangan |
| --- | --- |
| RD. Order No. | Nomor order R&D |
| Routing No. | Nomor routing produksi |
| Item No. | Nomor item produk |
| Product description | Deskripsi produk |
| Starting Time | Waktu mulai |
| Starting Date | Tanggal mulai |
| Ending Time | Waktu selesai |
| Ending Date | Tanggal selesai |
| Operation No. | Nomor operasi |
| Expected Trial Qty | Kuantitas trial yang direncanakan |
| Type | Tipe operasi |
| No. | Nomor mesin |
| Name | Nama mesin |
| Assign To | Personel yang ditugaskan (boleh lebih dari satu) |

### 5.3 Output Section

Kolom yang diperlukan:

| Kolom | Keterangan |
| --- | --- |
| Output | Jumlah output yang dihasilkan |
| Scrap | Jumlah scrap |
| Date | Tanggal pencatatan |
| Time | Waktu pencatatan |

### 5.4 Material Needed Section

**Judul section visible harus tetap: `Material Needed`**

Catatan penting:
Meskipun label visible-nya "Material Needed", section ini sering berisi tooling, cutting tool, tool holder, collet, insert, drill, endmill, aksesoris, atau consumable yang diperlukan untuk Job Card.

Kolom yang diperlukan:

| Kolom | Keterangan |
| --- | --- |
| No. | Nomor baris |
| Item No. | Nomor item tool |
| Product Name | Nama tool atau material |
| Expected Quantity | Kuantitas yang direncanakan |
| Base Unit of Measure | Satuan (Pc, Set, dsb.) |
| Tool Pos | Posisi tool di mesin |
| Additional Quantity | Kuantitas tambahan jika diperlukan |

**Aturan:**

- Pertahankan judul visible `Material Needed`.
- Secara internal, klasifikasikan setiap baris sebagai:
  ```
  Insert / Tool Holder / Collet / Drill / Center Drill / Endmill /
  Boring Tool / Threading Tool / Grooving Tool / Special Tool /
  Accessory / Consumable / TBD
  ```
- Hubungkan setiap baris ke Cutting Tool Master jika tersedia.
- Jika baris tidak jelas, set kategori `TBD` dan tambahkan ke Open Questions.
- Tool Pos harus dipertahankan persis seperti yang dimasukkan. Contoh:
  ```
  MAIN / BACK / T1 / T3 / T5 / T6 / T7 / T31 / T32 / T33 / T34
  ```

### 5.5 GCode Section

Kolom yang diperlukan:

| Kolom | Keterangan |
| --- | --- |
| Trial No. | Nomor trial |
| Main Program | Nomor program CNC utama |
| Cycle. Times | Cycle time per trial |
| Description | Deskripsi perubahan program, catatan trial, atau catatan proses |

**Aturan:**

- Pertahankan baris trial 1 hingga 7 secara default, sesuai PDF.
- Tambahkan baris trial lebih jika diperlukan.
- Main Program harus dihubungkan ke CNC Program Master jika tersedia.
- Jika Main Program berubah selama trial, aktifkan alur kerja `cnc-program-revision-control`.
- Cycle. Times harus dicatat jika tersedia.
- Description merangkum perubahan program, catatan trial, atau catatan proses yang relevan.

### 5.6 Note Section

Gunakan untuk catatan operasional teks bebas, catatan setup, atau observasi trial penting.

### 5.7 Inspection Result Section

Judul section visible harus:

```
Inspection Result (in mm)
```

Kolom pertama harus:

- Trial No.

Kolom sisanya berdasarkan fitur inspeksi yang diperlukan untuk produk tersebut.

Jika fitur inspeksi belum diketahui:
- Gunakan header kolom `TBD`, atau
- Biarkan kolom placeholder kosong, tergantung layout yang diinginkan.

---

## 6. Mode Output Job Card

### Mode 1: Obsidian Markdown Mode

**Tujuan:** Dokumentasi aktif, traceability, internal link, dan engineering knowledge base.

**Aturan:**
- Harus mempertahankan urutan section yang sama seperti referensi PDF.
- Harus menyertakan YAML frontmatter.
- Harus menyertakan Obsidian internal link.
- Harus menyertakan Open Questions jika data tidak lengkap.
- Harus menyertakan traceability links.

### Mode 2: Printable Job Card Mode

**Tujuan:** Output seperti Word/PDF yang dapat dicetak.

**Aturan:**
- Harus mempertahankan label visible dari referensi PDF.
- Harus mempertahankan struktur tabel.
- Hindari narasi yang berlebihan.
- Harus sesuai untuk dicetak dan ditandatangani jika diperlukan.
- Harus menggunakan urutan field yang sama seperti PDF.

### Mode 3: Data Update Mode

**Tujuan:** Digunakan saat user memberikan update trial.

**Contoh update:**
- Update output
- Update scrap
- Update cycle time
- Update GCode
- Update hasil inspeksi
- Update isu/deviasi
- Update revisi CNC
- Update perubahan tooling
- Update status penutupan

**Aturan:**
- Update hanya section yang relevan.
- Pertahankan riwayat sebelumnya.
- Jangan menimpa record trial tanpa menyimpan data lama.
- Aktifkan alur kerja terkait jika diperlukan.

---

## 7. Metadata YAML Wajib Job Card

```yaml
---
type: rnd_job_card
rd_order_no: TBD
status: draft
created_date: TBD
document_date: TBD
prepared_by: TBD
starting_date: TBD
ending_date: TBD
starting_time: TBD
ending_time: TBD
routing_no: TBD
item_no: TBD
product_description: TBD
product: TBD
product_family: TBD
material: TBD
operation_no: TBD
operation_type: TBD
machine_no: TBD
machine_name: TBD
machine: TBD
expected_trial_qty: TBD
assigned_to:
  - TBD
main_program: TBD
program_revision: TBD
trial_objective: TBD
source_reference: manual_entry
visible_format_reference: TDM RD Order Job Card PDF
related_product_tooling_setup: TBD
related_cnc_program: TBD
related_inspection_records: []
related_issue_records: []
related_cnc_revision_records: []
final_decision: TBD
approved_by: TBD
---
```

### Aturan Field YAML

| Field YAML | Field Visible | Keterangan |
| --- | --- | --- |
| `rd_order_no` | RD. Order No. | Harus cocok dengan prefix nama file |
| `operation_type` | Type | Tipe operasi di Job Card |
| `machine_no` | No. | Nomor mesin |
| `machine_name` | Name | Nama mesin |
| `main_program` | Main Program | Program utama di tabel GCode |
| `assigned_to` | Assign To | Mendukung lebih dari satu personel |
| `visible_format_reference` | — | Selalu `TDM RD Order Job Card PDF` |

Jika field apapun tidak diketahui, gunakan `TBD`.

---

## 8. Template Tabel Markdown

### Informasi Job Card Utama

```markdown
| Field | Value | Field | Value |
| --- | --- | --- | --- |
| RD. Order No. | TBD | Starting Time | TBD |
| Routing No. | TBD | Starting Date | TBD |
| Item No. | TBD | Ending Time | TBD |
| Product Description | TBD | Ending Date | TBD |
| Operation No. | TBD | | |
| Expected Trial Qty | TBD | | |
| Type | TBD | | |
| No. | TBD | Assign To | TBD |
| Name | TBD | | TBD |
```

### Output

```markdown
| Output | Scrap | Date | Time |
| ---: | ---: | --- | --- |
| TBD | TBD | TBD | TBD |
```

### Material Needed

```markdown
| No. | Item No. | Product Name | Expected Quantity | Base Unit of Measure | Tool Pos | Additional Quantity |
| ---: | --- | --- | ---: | --- | --- | ---: |
| 1 | TBD | TBD | TBD | TBD | TBD | TBD |
```

### GCode

```markdown
| Trial No. | Main Program | Cycle. Times | Description |
| ---: | --- | --- | --- |
| 1 | TBD | TBD | TBD |
| 2 | TBD | TBD | TBD |
| 3 | TBD | TBD | TBD |
| 4 | TBD | TBD | TBD |
| 5 | TBD | TBD | TBD |
| 6 | TBD | TBD | TBD |
| 7 | TBD | TBD | TBD |
```

### Note

```markdown
## Note

TBD
```

### Inspection Result (in mm)

```markdown
| Trial No. | Feature 1 | Feature 2 | Feature 3 | Feature 4 | Feature 5 | Feature 6 |
| ---: | --- | --- | --- | --- | --- | --- |
| 1 | TBD | TBD | TBD | TBD | TBD | TBD |
| 2 | TBD | TBD | TBD | TBD | TBD | TBD |
| 3 | TBD | TBD | TBD | TBD | TBD | TBD |
| 4 | TBD | TBD | TBD | TBD | TBD | TBD |
| 5 | TBD | TBD | TBD | TBD | TBD | TBD |
| 6 | TBD | TBD | TBD | TBD | TBD | TBD |
| 7 | TBD | TBD | TBD | TBD | TBD | TBD |
```

### Closure Checklist

```markdown
| Checkpoint | Status | Remark |
| --- | --- | --- |
| RD Order No. verified | TBD | TBD |
| File name matches RD Order No. | TBD | TBD |
| Product information complete | TBD | TBD |
| Machine information complete | TBD | TBD |
| Material identified | TBD | TBD |
| Tooling / Material Needed completed | TBD | TBD |
| Main Program recorded | TBD | TBD |
| Program revision recorded | TBD | TBD |
| Output recorded | TBD | TBD |
| Scrap recorded | TBD | TBD |
| Cycle time recorded | TBD | TBD |
| Inspection result recorded | TBD | TBD |
| Issues/deviations resolved or dispositioned | TBD | TBD |
| Final decision recorded | TBD | TBD |
| Approval recorded | TBD | TBD |
```

---

## 9. Aturan Siklus Hidup Job Card

### Nilai Status

```
draft
prepared
trial_ongoing
need_revision
retest
completed
closed
cancelled
```

### Aturan Transisi Status

| Dari | Ke | Syarat |
| --- | --- | --- |
| — | `draft` | Job Card baru dibuat |
| `draft` | `prepared` | Semua syarat prepared terpenuhi (lihat di bawah) |
| `prepared` | `trial_ongoing` | Eksekusi trial dimulai |
| `trial_ongoing` | `need_revision` | CNC perlu direvisi / tooling perlu diganti / inspeksi gagal / ada isu |
| `need_revision` | `retest` | Revisi atau penyesuaian selesai, siap trial ulang |
| `retest` | `trial_ongoing` | Trial ulang dimulai |
| `trial_ongoing` | `completed` | Semua aktivitas trial selesai dan hasilnya dicatat |
| `completed` | `closed` | Semua syarat penutupan terpenuhi (lihat Section 12) |
| Apapun | `cancelled` | Job Card dibatalkan secara eksplisit |

### Syarat Status `prepared`

Status hanya boleh menjadi `prepared` jika:

- [ ] RD Order No. sudah ada
- [ ] Item No. sudah ada
- [ ] Product description sudah ada
- [ ] Machine No. dan Machine Name sudah ada
- [ ] Operation No. dan Type sudah ada
- [ ] Expected Trial Qty sudah ada
- [ ] Assign To sudah ada
- [ ] Minimal satu baris Material Needed sudah ada
- [ ] Main Program sudah ada atau ditandai `TBD` dengan alasan yang jelas

---

## 10. Alur Kerja Co-Pilot

### Alur: New Job Card

Saat user meminta membuat Job Card baru:

1. Tentukan tanggal saat ini.
2. Hitung prefix RD Order menggunakan format `RDYYYYWWD`.
3. Cek Job Card yang sudah ada dengan prefix `YYYYWWD` yang sama.
4. Usulkan `NNN` berikutnya yang tersedia.
5. Konfirmasi atau tetapkan RD Order No.
6. Kumpulkan field yang diperlukan:
   - Routing No.
   - Item No.
   - Product description
   - Material
   - Operation No.
   - Type
   - Machine No.
   - Machine Name
   - Assign To
   - Expected Trial Qty
   - Main Program
7. Cek Product Master jika tersedia.
8. Cek Machine Master jika tersedia.
9. Cek Product Tooling Setup jika tersedia.
10. Siapkan tabel Material Needed.
11. Siapkan tabel GCode dengan baris trial 1 hingga 7.
12. Siapkan placeholder Inspection Result.
13. Tambahkan Open Questions untuk data yang hilang.
14. Simpan / buat Job Card.
15. Commit dan push.

### Alur: Trial Update

Saat user memberikan hasil trial:

1. Identifikasi Job Card yang aktif.
2. Identifikasi Trial No.
3. Update Output jika diberikan.
4. Update Scrap jika diberikan.
5. Update Date dan Time jika diberikan.
6. Update Cycle. Times jika diberikan.
7. Update GCode Description jika diberikan.
8. Update Inspection Result jika diberikan.
9. Identifikasi isu/deviasi jika ada.
10. Aktifkan alur CNC revision jika program berubah.
11. Aktifkan alur tooling setup jika tool berubah.
12. Update status Job Card.
13. Commit dan push.

### Alur: CNC Change During Trial

Jika program CNC berubah selama Job Card berlangsung:

1. Pertahankan urutan section Job Card.
2. Update kolom Description di tabel GCode.
3. Hubungkan ke CNC Revision Record.
4. Aktifkan skill `cnc-program-revision-control`.
5. Catat alasan perubahan.
6. Catat nomor trial yang terpengaruh.
7. Catat verifikasi yang diperlukan.
8. Set status Job Card ke `need_revision` atau `retest` sesuai kondisi.
9. Commit dan push.

### Alur: Tooling Change During Trial

Jika tooling berubah selama Job Card berlangsung:

1. Update tabel Material Needed jika diperlukan.
2. Pertahankan riwayat tooling asli.
3. Hubungkan tool baru ke Cutting Tool Master.
4. Aktifkan skill `cutting-tool-database` jika tool belum ada di database.
5. Aktifkan skill `product-tooling-setup` jika product tooling setup berubah.
6. Catat alasan penggantian tool.
7. Update status Job Card jika retest diperlukan.
8. Commit dan push.

### Alur: Close Job Card

Sebelum menutup Job Card:

1. Validasi konsistensi RD Order No. dan nama file.
2. Validasi kelengkapan section utama.
3. Validasi Output section.
4. Validasi Material Needed section.
5. Validasi GCode section.
6. Validasi Inspection Result section.
7. Validasi isu/deviasi.
8. Konfirmasi tooling setup yang diterima.
9. Konfirmasi revisi program CNC yang diterima.
10. Tulis kesimpulan trial.
11. Tulis final decision.
12. Catat approval atau tandai sebagai pending.
13. Set status ke `closed` hanya jika semua kriteria penutupan terpenuhi.
14. Commit dan push.

---

## 11. Aturan Validasi

Claude harus memeriksa hal-hal berikut sebelum mengubah status atau menutup Job Card:

### Validasi RD Order No.

- [ ] RD Order No. ada.
- [ ] RD Order No. mengikuti format `RDYYYYWWDNNN`.
- [ ] Nama file diawali dengan RD Order No. yang tepat.
- [ ] RD Order No. di YAML cocok dengan RD. Order No. yang visible.

### Validasi Kelengkapan Data

- [ ] Routing No. ada.
- [ ] Item No. ada.
- [ ] Product Description ada.
- [ ] Starting Date ada.
- [ ] Operation No. ada.
- [ ] Expected Trial Qty ada.
- [ ] Type ada.
- [ ] Machine No. ada.
- [ ] Machine Name ada.
- [ ] Assign To ada.
- [ ] Minimal satu baris Material Needed ada sebelum status `prepared`.
- [ ] Main Program ada sebelum eksekusi trial, kecuali ditandai `TBD` dengan alasan.

### Validasi Kelengkapan Trial

- [ ] Output dan Scrap tercatat sebelum status `completed`.
- [ ] Inspection Result tercatat sebelum status `closed`.
- [ ] Final Decision ada sebelum status `closed`.

### Validasi Traceability

- [ ] Perubahan CNC dihubungkan ke CNC Revision Record.
- [ ] Perubahan tooling dihubungkan ke Product Tooling Setup dan Cutting Tool Master.
- [ ] Isu/deviasi dicatat ketika ada kegagalan trial atau temuan abnormal.

---

## 12. Kriteria Penutupan Job Card

Job Card hanya boleh diset `closed` jika **semua** kondisi berikut terpenuhi:

```
[ ] Output tercatat.
[ ] Scrap tercatat.
[ ] Inspection Result selesai atau ditandai tidak berlaku dengan alasan.
[ ] Semua isu/deviasi berstatus resolved atau dispositioned.
[ ] Kesimpulan trial ditulis.
[ ] Final decision ditulis.
[ ] Approval tercatat atau ditandai TBD/pending secara eksplisit.
[ ] RD Order No. konsisten antara nama file, YAML, dan konten visible.
[ ] Tooling setup yang diterima dikonfirmasi atau didokumentasikan.
[ ] Revisi program CNC yang diterima dikonfirmasi atau didokumentasikan.
```

---

## 13. Format Respons Wajib

Setelah menggunakan skill ini, Claude **wajib** menggunakan format respons berikut:

```
### Summary
[Ringkasan singkat tindakan yang dilakukan]

### Job Card Status
[Status saat ini: draft / prepared / trial_ongoing / need_revision / retest / completed / closed / cancelled]

### Data Created or Updated
- [Daftar field atau section yang dibuat atau diperbarui]

### Missing Data / Open Questions
| No. | Pertanyaan | Mengapa Penting | Status |
| ---: | --- | --- | --- |
| 1 | [pertanyaan] | [alasan] | Open |

### Traceability Links Updated
- Product Master: [ada / belum ada]
- Machine Master: [ada / belum ada]
- Product Tooling Setup: [ada / belum ada]
- CNC Program: [ada / belum ada]
- Inspection Record: [ada / belum ada / belum relevan]
- Issue Record: [ada / belum ada / belum relevan]
- CNC Revision Record: [ada / belum ada / belum relevan]

### Validation Result
- RD Order No.: [valid / tidak valid / TBD — detail]
- File name consistency: [cocok / tidak cocok — detail]
- Kelengkapan data: [lengkap / ada yang kurang — sebutkan]
- Kriteria penutupan: [terpenuhi / belum terpenuhi — sebutkan yang kurang]

### Next Action
- [Rekomendasi tindakan berikutnya]

### Git Result
- Files changed: [daftar file]
- Commit message: [pesan commit]
- Commit hash: [hash]
- Branch pushed: [nama branch]
- Push status: [berhasil / gagal]
- Error: [detail jika gagal, atau "Tidak ada"]
```

Jangan mengklaim commit atau push berhasil kecuali benar-benar terjadi.

---

## 14. Batas Penting

- Jangan membuat record Job Card aktual sebagai bagian dari pembuatan skill ini.
- Jangan membuat file template sebagai bagian dari pembuatan skill ini.
- Jangan membuat record Cutting Tool Master sebagai bagian dari pembuatan skill ini.
- Jangan membuat CNC Revision Record sebagai bagian dari pembuatan skill ini.
- Hanya buat file SKILL.md untuk `rnd-job-card-copilot`.
- Jangan menghapus persyaratan commit dan push.
