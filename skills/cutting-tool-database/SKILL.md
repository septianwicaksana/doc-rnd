---
skill_id: cutting-tool-database
version: 1.0.0
status: active
purpose: Mengelola master data cutting tool internal untuk R&D Manufacturing — membuat, memperbarui, mengklasifikasi, memvalidasi, dan menghubungkan record cutting tool ke Job Card, Product Tooling Setup, mesin, program CNC, dan trial record.
triggers:
  - tambah cutting tool baru
  - update data cutting tool
  - cek cutting tool sudah ada atau belum
  - klasifikasi tool dari item number
  - review tool yang digunakan di Job Card
  - update status tool
  - cari tool untuk produk atau mesin tertentu
  - catat tool pengganti atau alternatif
  - review tool wear atau tool life
related_skills:
  - cutting-tool-catalog-intelligence
  - product-tooling-setup
  - rnd-job-card-copilot
  - obsidian-vault-traceability
---

# Skill: Cutting Tool Database

## 1. Tujuan Skill

Skill ini mengelola **database master data cutting tool internal** untuk keperluan R&D Manufacturing.

Cakupan skill ini:

- Membuat Cutting Tool Master note baru.
- Memperbarui data tool yang sudah ada.
- Mengklasifikasikan tool ke kategori dan tipe yang benar.
- Memvalidasi kelengkapan dan konsistensi data.
- Menghubungkan tool ke record terkait.
- Mencegah duplikasi data.
- Mengelola status tool sepanjang siklus hidupnya.
- Mencatat tool pengganti atau alternatif.

Skill ini **tidak** melakukan penelitian katalog eksternal secara mandiri. Jika informasi tool tidak diketahui dan memerlukan identifikasi dari catalog number atau web, gunakan atau rekomendasikan skill `cutting-tool-catalog-intelligence`.

---

## 2. Kapan Menggunakan Skill Ini

Gunakan skill ini ketika:

- User menyebutkan item number tool baru yang belum ada di database.
- User meminta menambahkan tool ke master data.
- User ingin mengubah status, kategori, atau kompatibilitas tool.
- User ingin mengecek apakah tool tertentu sudah terdaftar.
- User melaporkan tool pengganti setelah trial.
- User ingin mengetahui tool apa saja yang digunakan untuk produk atau mesin tertentu.
- User mencatat data tool wear atau tool life dari hasil trial.
- User ingin menandai tool sebagai proven, obsolete, atau rejected setelah evaluasi.

---

## 3. Konsep Cutting Tool Master Data

Cutting Tool Master adalah kumpulan record stabil yang menyimpan informasi **identitas, spesifikasi, dan kompatibilitas** setiap tool yang pernah digunakan atau dievaluasi dalam kegiatan R&D Manufacturing.

Cutting Tool Master menjawab pertanyaan:

```
Tool ini apa? Buat apa? Bisa dipakai di mesin apa? Untuk material apa? Sudah terbukti atau belum?
```

Cutting Tool Master **berbeda** dari Product Tooling Setup:

| Aspek | Cutting Tool Master | Product Tooling Setup |
| --- | --- | --- |
| Cakupan | Satu tool secara umum | Kombinasi tool untuk satu produk + mesin + operasi |
| Isi | Spesifikasi, kompatibilitas, status | Posisi tool, fungsi per operasi, referensi Job Card |
| Pertanyaan | "Tool ini apa dan bisa buat apa?" | "Produk ini pakai tool apa di posisi mana?" |

---

## 4. Kategori Tool yang Dikendalikan

Gunakan hanya nilai berikut untuk field `category`. Jangan membuat nilai baru.

| Kategori | Keterangan |
| --- | --- |
| `Insert` | Sisipan pahat indexable (turning, milling, threading insert) |
| `Tool Holder` | Pemegang tool — boring bar, tool block, shank, dsb. |
| `Collet` | Collet untuk chuck atau ER system |
| `Drill` | Mata bor standar |
| `Center Drill` | Center drill / spot drill |
| `Endmill` | Endmill flat standar |
| `Ball Nose Endmill` | Endmill ball nose untuk kontur |
| `Tap` | Tap ulir |
| `Threading Tool` | Threading tool (external/internal, insert berbasis) |
| `Grooving Tool` | Tool untuk grooving, undercutting, atau parting |
| `Boring Tool` | Tool untuk boring internal |
| `Chamfer Tool` | Tool chamfer |
| `Reamer` | Reamer untuk finishing lubang |
| `Form Tool` | Tool profil khusus (non-standard) |
| `Special Tool` | Tool khusus lainnya yang tidak masuk kategori di atas |
| `Workholding` | Komponen workholding (chuck jaw, fixture, dsb.) |
| `Measuring Tool` | Alat ukur dan gauge |
| `Setup Aid` | Alat bantu setup (presetter, dial indicator holder, dsb.) |
| `Consumable` | Material habis pakai (abrasive, coolant, dsb.) |
| `TBD` | Belum terklasifikasi — wajib di-review |

Jika kategori belum jelas, gunakan `TBD` dan tandai `verification_status: pending_review`.

---

## 5. Nilai Status Tool

Gunakan hanya nilai berikut untuk field `status`.

| Status | Arti |
| --- | --- |
| `active` | Tool tersedia dan diizinkan digunakan |
| `under_trial` | Tool sedang dievaluasi dalam trial aktif |
| `proven` | Tool sudah terbukti memenuhi persyaratan berdasarkan hasil trial |
| `obsolete` | Tool sudah tidak digunakan, digantikan oleh tool lain |
| `rejected` | Tool tidak memenuhi syarat berdasarkan hasil evaluasi |
| `pending_review` | Tool perlu dikonfirmasi atau divalidasi sebelum digunakan |
| `TBD` | Status belum diketahui |

### Aturan Perubahan Status

- Status `proven` hanya dapat diberikan jika ada bukti trial yang terdokumentasi (referensi Job Card atau Inspection Record).
- Status `obsolete` hanya diberikan jika ada tool pengganti atau keputusan eksplisit dari user.
- Status `rejected` harus disertai alasan singkat di field `remark` atau section Evaluation Notes.
- Jangan ubah status dari `proven` ke `active` — `proven` adalah status lebih tinggi dari `active`.

---

## 6. Metadata Wajib Cutting Tool Master

Setiap Cutting Tool Master note harus memiliki YAML frontmatter berikut:

```yaml
---
type: cutting_tool
tool_id: TBD
item_no: TBD
catalog_no: TBD
normalized_catalog_no: TBD
tool_name: TBD
brand: TBD
manufacturer: TBD
category: TBD
tool_type: TBD
sub_type: TBD
diameter: TBD
radius: TBD
length: TBD
insert_grade: TBD
coating: TBD
material_application:
  - TBD
machine_compatibility:
  - TBD
compatible_operations:
  - TBD
unit: Pc
status: active
replacement_tool: TBD
source: TBD
created_from_job_card: TBD
verification_status: pending_review
---
```

### Penjelasan Field Kunci

| Field | Penjelasan |
| --- | --- |
| `tool_id` | ID internal unik (format: `TL-XXXXX`) |
| `item_no` | Nomor item internal perusahaan |
| `catalog_no` | Catalog number dari manufacturer (persis seperti di katalog) |
| `normalized_catalog_no` | Catalog number yang dinormalisasi (huruf kapital, tanpa spasi ekstra) |
| `tool_name` | Nama deskriptif tool dalam Bahasa Inggris |
| `brand` | Merek dagang (SECO, OSG, SANDVIK, dsb.) |
| `manufacturer` | Nama pabrikan lengkap |
| `category` | Kategori dari daftar terkontrol (lihat Section 4) |
| `tool_type` | Tipe spesifik dalam kategori (contoh: `DCMT Insert`, `ER32 Collet`) |
| `sub_type` | Sub-tipe lebih spesifik jika diperlukan |
| `diameter` | Diameter dalam mm, atau `TBD` |
| `radius` | Corner radius dalam mm, atau `TBD` (relevan untuk insert dan endmill) |
| `length` | Panjang dalam mm, atau `TBD` |
| `insert_grade` | Grade insert (contoh: `CP500`, `GC1125`) — hanya untuk insert |
| `coating` | Coating (TiAlN, TiCN, PVD, dsb.) atau `TBD` |
| `material_application` | Daftar material yang kompatibel |
| `machine_compatibility` | Daftar tipe mesin yang kompatibel (nilai terkontrol) |
| `compatible_operations` | Daftar operasi yang dapat dilakukan |
| `unit` | Satuan unit — default `Pc` |
| `status` | Status tool dari nilai terkontrol (lihat Section 5) |
| `replacement_tool` | Nama note tool pengganti jika `obsolete`, atau `TBD` |
| `source` | Sumber data: `job_card`, `catalog_research`, `manual_entry`, dsb. |
| `created_from_job_card` | Nama note Job Card pertama kali tool ini didokumentasikan |
| `verification_status` | `pending_review`, `verified`, atau `requires_engineering_confirmation` |

---

## 7. Section Wajib Cutting Tool Master Note

Setiap Cutting Tool Master note harus memiliki section berikut:

```markdown
# [Brand] [Catalog No] [Deskripsi Singkat]

## 1. Identifikasi Tool

## 2. Spesifikasi Teknis

## 3. Kompatibilitas

### 3.1 Kompatibilitas Material

### 3.2 Kompatibilitas Mesin

### 3.3 Kompatibilitas Operasi

## 4. Riwayat Penggunaan

## 5. Catatan Performa dan Tool Wear

## 6. Tool Pengganti / Alternatif

## 7. Traceability Links

## 8. Open Questions

## 9. Evaluation Notes
```

---

## 8. Aturan Pencegahan Duplikasi

Sebelum membuat Cutting Tool Master note baru, lakukan pengecekan berikut secara berurutan.

### Langkah Pengecekan

1. **Cek `item_no`** — Jika item_no sudah ada di database, **jangan buat note baru**. Update note yang sudah ada.
2. **Cek `catalog_no`** — Jika catalog_no (atau normalized_catalog_no) sudah ada dengan brand yang sama, **jangan buat note baru**.
3. **Cek `tool_name` + `brand`** — Jika kombinasi nama dan brand sudah ada, konfirmasi ke user apakah ini tool yang sama atau berbeda sebelum membuat note baru.
4. Jika ragu, buat `catalog_research` note terlebih dahulu dengan `type: catalog_research` dan tandai `recommended_database_action: TBD`.

### Kasus Khusus

| Situasi | Tindakan |
| --- | --- |
| Item_no sama, catalog_no berbeda | Konfirmasi ke user — kemungkinan revisi produk atau penggantian vendor |
| Catalog_no sama, brand berbeda | Kemungkinan OEM / relabeling — konfirmasi sebelum menggabungkan |
| Tool fisik sama tapi belum punya item_no | Buat note dengan `item_no: TBD`, tandai `verification_status: pending_review` |
| Tool mirip tapi berbeda spesifikasi | Buat note terpisah, tambahkan cross-reference di Section 6 (Alternatif) |

---

## 9. Aturan Kompatibilitas

### 9.1 Kompatibilitas Material

Gunakan nama material yang konsisten:

```
Ti6Al4V ELI
SS316LVM
CoCrMo
PEEK
Aluminium
Baja karbon
Baja paduan
TBD
```

Jangan membuat klaim kompatibilitas material tanpa dasar dari katalog, hasil trial, atau konfirmasi engineer.

### 9.2 Kompatibilitas Mesin

Gunakan hanya nilai terkontrol berikut untuk field `machine_compatibility`:

```
CNC Turning
Swiss-type CNC Turning
CNC Milling
Turn-Mill
Manual Milling
Manual Lathe
Grinding
EDM
Inspection Equipment
Universal / Accessory
TBD
```

Aturan tambahan:

- Turning insert → default `CNC Turning` atau `Swiss-type CNC Turning`, tergantung ukuran dan holder.
- Endmill → default `CNC Milling` atau `Turn-Mill` (live tooling).
- Drill → dapat `CNC Turning`, `Swiss-type CNC Turning`, `CNC Milling`, atau `Turn-Mill` — konfirmasi dari setup.
- Collet → `Universal / Accessory` kecuali ada ukuran atau sistem spesifik mesin.
- Tool holder → spesifik per mesin, jangan diasumsikan universal.
- Jika tidak yakin, gunakan `TBD` dan tandai `verification_status: pending_review`.

### 9.3 Kompatibilitas Operasi

Contoh nilai untuk `compatible_operations`:

```
Facing
OD Turning
ID Turning
Grooving
Threading
Parting
Drilling
Reaming
Tapping
Milling
Boring
Chamfering
Center Drilling
Form Turning
TBD
```

Jangan membuat klaim operasi yang tidak didukung oleh spesifikasi tool atau bukti trial.

---

## 10. Aturan Linking Tool ke Record Lain

### Link ke R&D Job Card

- Saat tool pertama kali digunakan dalam trial, tambahkan nama Job Card ke field `created_from_job_card`.
- Tambahkan internal link di Section 7 (Traceability Links):

```markdown
- Job Card pertama: [[RD20260605001 - Emerald 6.5 mm Poly Screw L45 - CITIZEN A20-3F7 No.1]]
```

- Jika tool digunakan di beberapa Job Card, buat daftar link di Section 4 (Riwayat Penggunaan).

### Link ke Product Tooling Setup

- Tambahkan internal link di Section 7 untuk setiap Product Tooling Setup yang menggunakan tool ini:

```markdown
- Digunakan di setup: [[Emerald 6.5 mm Poly Screw L45 - CITIZEN A20-3F7 No.1 - Tooling Setup]]
```

### Link ke CNC Program

- Jika tool terikat pada program CNC tertentu (contoh: form tool untuk program khusus), tambahkan link di Section 7:

```markdown
- Terkait program: [[Program 2513 - CITIZEN A20-3F7 No.1]]
```

### Link ke Machine Master

- Jika kompatibilitas tool spesifik terhadap mesin tertentu (bukan tipe mesin), tambahkan link:

```markdown
- Dikonfirmasi pada mesin: [[CITIZEN A20-3F7 - No.1]]
```

### Link ke Tool Wear Records

- Tambahkan link di Section 5 (Catatan Performa dan Tool Wear) setiap kali ada Tool Wear Record yang mencatat performa tool ini.

---

## 11. Aturan Tool Tidak Pasti atau Ambigu

Gunakan prosedur berikut saat menghadapi tool yang informasinya tidak lengkap atau ambigu.

### Kondisi Ambigu Umum

| Kondisi | Tindakan |
| --- | --- |
| Catalog number diketahui tapi brand tidak pasti | Gunakan `brand: TBD`, rekomendasikan `cutting-tool-catalog-intelligence` |
| Catalog number tidak jelas (typo, tidak standar) | Catat apa adanya di `catalog_no`, isi `normalized_catalog_no: TBD`, tandai `pending_review` |
| Kategori tool tidak yakin | Gunakan `category: TBD`, jangan menebak |
| Ukuran atau spesifikasi tidak diketahui | Gunakan `TBD` untuk field tersebut, tambahkan ke Open Questions |
| Tool mungkin sama dengan tool lain di database | Buat `catalog_research` note dulu, jangan langsung merge |
| Tool tidak diketahui sama sekali | Buat note minimal dengan field yang diketahui, tandai semua field lain `TBD` |

### Prinsip Penanganan Ambiguitas

- **Jangan menebak** kategori, brand, atau kompatibilitas jika tidak yakin.
- **Jangan klaim** tool sudah `proven` atau `verified` tanpa bukti trial.
- **Selalu** gunakan `verification_status: pending_review` untuk tool yang belum dikonfirmasi.
- **Selalu** tambahkan pertanyaan ke Open Questions jika ada informasi kritis yang hilang.
- Jika identifikasi tool membutuhkan penelitian katalog, rekomendasikan skill `cutting-tool-catalog-intelligence` kepada user.

---

## 12. Aturan Tool Pengganti dan Alternatif

### Tool Pengganti (Replacement Tool)

Tool pengganti adalah tool yang **menggantikan** tool lama yang sudah `obsolete` atau `rejected`.

- Isi field `replacement_tool` pada note tool lama dengan nama note tool pengganti.
- Ubah status tool lama menjadi `obsolete`.
- Buat note baru untuk tool pengganti jika belum ada.
- Tambahkan catatan alasan penggantian di Section 9 (Evaluation Notes) tool lama.

Contoh:
```yaml
status: obsolete
replacement_tool: SECO DCMT11T302F1 CP500 Turning Insert
```

### Tool Alternatif

Tool alternatif adalah tool lain yang **dapat digunakan** sebagai pengganti dalam kondisi tertentu, tanpa menggantikan tool utama.

- Daftar di Section 6 (Tool Pengganti / Alternatif) dengan keterangan kondisi penggunaannya.
- Jangan ubah status tool utama hanya karena ada alternatif.

Contoh:
```markdown
## 6. Tool Pengganti / Alternatif

| Nama Tool | Alasan | Kondisi Penggunaan |
| --- | --- | --- |
| [[SANDVIK DCMT11T302-PF 1125]] | Sebagai fallback jika CP500 tidak tersedia | Hanya untuk Ti6Al4V, bukan SS316LVM |
```

---

## 13. Format Respons Wajib Setelah Memperbarui Data Cutting Tool

Setelah menyelesaikan tugas yang melibatkan skill ini, Claude **wajib** melaporkan:

```
### Hasil Cutting Tool Database

**Tindakan yang dilakukan:**
- [Daftar tool yang dibuat atau diperbarui]

**Validasi data:**
- Duplikasi: [tidak ditemukan / ditemukan — detail]
- Kategori: [terklasifikasi / TBD — sebutkan tool mana]
- Kompatibilitas: [lengkap / ada TBD — sebutkan field mana]
- Verification status: [verified / pending_review — sebutkan tool mana]

**Traceability:**
- Link ke Job Card: [ada / belum ada]
- Link ke Tooling Setup: [ada / belum ada / tidak relevan]
- Link ke Mesin: [ada / belum ada / tidak relevan]

**Open Questions:**
- [Daftar pertanyaan yang ditambahkan, jika ada]

**Rekomendasi:**
- [Apakah perlu cutting-tool-catalog-intelligence untuk tool tertentu?]
- [Tool mana yang perlu dikonfirmasi ke engineer?]

**Git:**
- Files changed: [daftar file]
- Commit: [hash]
- Branch: [nama branch]
- Push: [berhasil / gagal — detail jika gagal]

**Langkah selanjutnya:**
- [Rekomendasi tindakan berikutnya]
```

Jangan klaim tool sudah `verified` atau `proven` tanpa bukti dari trial atau konfirmasi engineer yang terdokumentasi.
