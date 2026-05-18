---
skill_id: cutting-tool-catalog-intelligence
version: 1.0.0
status: active
purpose: Meneliti dan mengklasifikasikan cutting tool atau aksesoris tooling dari catalog number, kode manufacturer, kode supplier, atau deskripsi tool yang tidak diketahui — dengan pendekatan investigasi bertahap dan pelaporan temuan secara progresif.
triggers:
  - cari catalog number
  - identifikasi tool dari kode
  - tool ini jenis apa
  - catalog number tidak dikenal
  - identifikasi brand dari item number
  - tool bisa dipakai di mesin apa
  - klasifikasi tool baru
  - research cutting tool sebelum masuk database
related_skills:
  - cutting-tool-database
  - obsidian-vault-traceability
  - product-tooling-setup
---

# Skill: Cutting Tool Catalog Intelligence

## 1. Tujuan Skill

Skill ini membantu Claude melakukan **penelitian dan klasifikasi** cutting tool atau aksesoris tooling berdasarkan:

- Catalog number dari manufacturer.
- Kode internal supplier.
- Nomor item internal perusahaan plus nama tool.
- Deskripsi tool yang tidak diketahui atau tidak jelas.

Skill ini bekerja dengan pendekatan **streaming-style investigation** — Claude melaporkan temuan secara bertahap selama proses penelitian, bukan menunggu sampai selesai baru melaporkan.

Hasil akhir skill ini adalah:

- Klasifikasi tool (kategori, tipe, fungsi).
- Kompatibilitas mesin, operasi, dan material berdasarkan bukti.
- Tingkat kepercayaan (confidence level) yang jujur.
- Rekomendasi tindakan ke database cutting tool.
- Catalog Research note yang siap disimpan.

Skill ini **tidak** mengelola database master data secara langsung. Setelah research selesai dan user mengkonfirmasi, gunakan skill `cutting-tool-database` untuk membuat atau memperbarui Cutting Tool Master note.

---

## 2. Kapan Menggunakan Skill Ini

Gunakan skill ini ketika:

- User memberikan catalog number yang belum ada di database.
- User memberikan kode item atau kode supplier yang tidak dikenal.
- User bertanya "tool ini apa?" atau "bisa dipakai di mesin apa?".
- User memberikan nama tool yang ambigu atau tidak standar.
- User ingin mengkonfirmasi apakah tool tertentu cocok sebelum membeli atau digunakan.
- Skill `cutting-tool-database` tidak menemukan data cukup untuk mengklasifikasikan tool.
- User ingin memastikan kompatibilitas material, mesin, atau operasi sebelum trial.

---

## 3. Contoh Input yang Valid

Skill ini dapat memproses berbagai bentuk input berikut:

### Catalog Number Langsung
```
DCMT11T302F1 CP500
DCMT 11T3 02-F1 CP500
DCMT11T302F1-CP500
```

### Kode Manufacturer
```
OSG EX-GDS 3.4
SECO R217.69-0632.0-12-2AN
SANDVIK 266RG-16MM01A150M
```

### Kode Supplier atau Item Number Internal
```
1010-05255 NTK HYNBH04025K
Item 77821 - Threading insert 16ER AG60
```

### Deskripsi Tidak Lengkap
```
Insert turning triangular 11mm untuk Ti grade
Drill Swiss-type merek OSG diameter 3.4
Threading tool untuk mesin Citizen
```

### Kombinasi Item Number + Nama
```
Item 4421 - SECO DCMT turning insert untuk Ti6Al4V
```

---

## 4. Alur Penelitian

Jalankan langkah berikut secara berurutan. Laporkan temuan di setiap langkah sebelum melanjutkan ke langkah berikutnya.

### Langkah 1 — Normalisasi Catalog Number

Sebelum mencari, normalisasi input terlebih dahulu:

- Hapus spasi ekstra, tanda hubung yang tidak perlu, dan karakter tidak standar.
- Pisahkan catalog number dari grade atau suffix jika ada.
- Contoh: `DCMT 11T3 02-F1 CP500` → catalog number: `DCMT11T302F1`, grade/suffix: `CP500`.
- Catat versi asli (dari user) dan versi yang dinormalisasi secara terpisah.

Laporkan: *"Catalog number dinormalisasi menjadi: [hasil]. Grade/suffix terpisah: [hasil]."*

### Langkah 2 — Identifikasi Brand / Manufacturer

Berdasarkan format catalog number, identifikasi kemungkinan brand atau manufacturer:

- Cek apakah format sesuai dengan konvensi naming brand besar (SECO, SANDVIK, OSG, MITSUBISHI, KYOCERA, ISCAR, KENNAMETAL, NTK, SUMITOMO, dsb.).
- Jika user menyebutkan brand secara eksplisit, gunakan itu sebagai hipotesis awal.
- Jika brand tidak jelas, catat sebagai `TBD` dan lanjutkan dengan pencarian berbasis catalog number.

Laporkan: *"Hipotesis brand: [nama] — alasan: [penjelasan singkat]. Confidence: [High/Medium/Low/TBD]."*

### Langkah 3 — Cari dari Sumber Resmi Terlebih Dahulu

Urutan pencarian:

1. Halaman produk resmi manufacturer.
2. PDF katalog resmi manufacturer.
3. Distributor resmi yang menyebutkan spesifikasi teknis lengkap.
4. Marketplace distributor hanya sebagai pendukung.

Untuk setiap sumber yang ditemukan, catat:
- URL sumber.
- Jenis sumber (halaman resmi / PDF katalog / distributor / marketplace).
- Informasi teknis yang didapat.

Laporkan temuan secara bertahap: *"Sumber ditemukan: [URL] — Tipe: [jenis]. Data yang diperoleh: [ringkasan]."*

Jika sumber resmi tidak ditemukan, laporkan: *"Tidak menemukan sumber resmi. Melanjutkan dengan sumber sekunder."*

### Langkah 4 — Klasifikasi Kategori dan Tipe Tool

Berdasarkan bukti yang ditemukan, tentukan:

- Apakah ini cutting tool, tool holder, collet, workholding, aksesoris, atau consumable?
- Kategori spesifik dari daftar terkontrol (lihat Section 7).
- Tipe tool dalam kategori (contoh: `DCMT Insert`, `ER32 Collet`, `Boring Bar`).

Aturan klasifikasi penting:

- **Tool holder bukan cutting tool** — jangan klasifikasikan sebagai cutting tool.
- **Collet adalah aksesoris** kecuali memiliki fungsi pemotongan langsung.
- **Turning insert bukan milling tool** kecuali bukti eksplisit mendukung.
- **Endmill tidak otomatis kompatibel** dengan semua mesin CNC.

Laporkan: *"Klasifikasi: Kategori [X], Tipe [Y]. Alasan: [ringkasan bukti]."*

### Langkah 5 — Tentukan Kompatibilitas Mesin

Berdasarkan bukti dari sumber, tentukan kompatibilitas mesin menggunakan nilai terkontrol (lihat Section 6).

Aturan penting:

- Kompatibilitas mesin **bergantung pada holder dan setup**, bukan hanya tipe tool.
- Jangan klaim kompatibilitas Swiss-type hanya karena tool kecil — perlu bukti eksplisit.
- Jangan klaim kompatibilitas Turn-Mill hanya karena ini mesin kombinasi — perlu konfirmasi holder live tooling.
- Jika tidak yakin, gunakan `TBD` dan beri catatan di Open Questions.

Laporkan: *"Kompatibilitas mesin yang teridentifikasi: [daftar]. Catatan: [kondisi atau ketergantungan holder]."*

### Langkah 6 — Tentukan Kompatibilitas Operasi

Berdasarkan fungsi tool, tentukan operasi yang dapat dilakukan.

Contoh nilai:
```
Facing, OD Turning, ID Turning, Grooving, Threading, Parting,
Drilling, Reaming, Tapping, Milling, Boring, Chamfering,
Center Drilling, Form Turning, TBD
```

Jangan mengklaim operasi yang tidak didukung oleh spesifikasi tool.

### Langkah 7 — Tentukan Kompatibilitas Material (Jika Ada Bukti)

Tentukan material yang kompatibel hanya jika ada bukti dari sumber resmi atau datasheet.

Material yang umum dalam konteks proyek ini:
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

Jika tidak ada bukti material kompatibilitas, gunakan `TBD`. **Jangan menebak kompatibilitas material.**

### Langkah 8 — Tentukan Confidence Level

Berdasarkan kualitas dan jumlah bukti yang ditemukan, tetapkan confidence level (lihat Section 8).

### Langkah 9 — Rekomendasikan Tindakan Database

Berikan rekomendasi salah satu dari:

| Rekomendasi | Kondisi |
| --- | --- |
| `add_to_master` | Bukti cukup, klasifikasi jelas, siap masuk Cutting Tool Master |
| `add_as_catalog_research` | Informasi parsial, perlu review lebih lanjut sebelum masuk master |
| `update_existing` | Tool sudah ada di database, temuan ini memperbarui data yang ada |
| `do_not_add` | Tool bukan cutting tool/tooling yang relevan, atau duplikat yang sudah ada |
| `requires_engineering_confirmation` | Klasifikasi atau kompatibilitas butuh konfirmasi dari engineer |

---

## 5. Aturan Prioritas Sumber

Gunakan urutan prioritas ini saat mengevaluasi sumber.

| Prioritas | Jenis Sumber | Bobot |
| --- | --- | --- |
| 1 | Halaman produk resmi manufacturer | Tertinggi — dapat dipercaya penuh |
| 2 | PDF katalog resmi manufacturer | Tinggi — dokumen teknis resmi |
| 3 | Distributor resmi (tertera di website manufacturer) | Sedang — biasanya akurat, kadang tidak lengkap |
| 4 | Marketplace distributor (Tokopedia, Misumi, MSC, dsb.) | Rendah — hanya sebagai pendukung, sering tidak lengkap |
| 5 | Bukti dari user (foto, dokumen internal, konfirmasi verbal) | Kontekstual — valid untuk data penggunaan aktual, bukan spesifikasi teknis |

### Aturan Penggunaan Sumber

- Jika sumber prioritas 1 atau 2 tersedia, gunakan sebagai dasar utama klasifikasi.
- Jika hanya sumber prioritas 3 atau 4 yang tersedia, naikkan ketidakpastian ke Medium atau Low.
- Bukti dari user (prioritas 5) valid untuk mencatat penggunaan aktual di lapangan, tapi **bukan** untuk mengklaim spesifikasi teknis resmi.
- Selalu catat URL dan jenis sumber di `source_urls` dan `source_type` pada catalog research note.
- Jika tidak ada sumber yang ditemukan, laporkan secara eksplisit dan tetapkan confidence: `Low`.

---

## 6. Nilai Terkontrol Kompatibilitas Mesin

Gunakan hanya nilai berikut untuk `machine_compatibility`:

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

---

## 7. Kategori Klasifikasi Terkontrol

Gunakan hanya nilai berikut untuk `category`:

```
Insert
Tool Holder
Collet
Drill
Center Drill
Endmill
Ball Nose Endmill
Tap
Threading Tool
Grooving Tool
Boring Tool
Chamfer Tool
Reamer
Form Tool
Special Tool
Workholding
Measuring Tool
Setup Aid
Consumable
TBD
```

---

## 8. Aturan Confidence Level

### High
Digunakan jika:
- Sumber resmi (prioritas 1 atau 2) ditemukan dan mengkonfirmasi semua informasi kunci.
- Catalog number cocok persis.
- Kategori, tipe, dan kompatibilitas mesin dapat ditentukan dengan jelas.

### Medium
Digunakan jika:
- Hanya sumber distributor yang tersedia.
- Catalog number cocok sebagian (format mirip tapi tidak persis).
- Satu atau lebih field kompatibilitas masih `TBD`.
- Terdapat ketidakpastian tentang holder atau setup dependency.

### Low
Digunakan jika:
- Tidak ada sumber resmi ditemukan.
- Hanya ada sumber marketplace atau deskripsi tidak lengkap.
- Catalog number ambigu atau tidak standar.
- Klasifikasi bergantung pada asumsi.

### TBD
Digunakan jika:
- Penelitian belum selesai.
- Bukti terlalu sedikit untuk membuat penilaian apapun.

**Aturan keras:** Jangan tetapkan `High` jika ada field kritis yang masih `TBD` atau jika hanya sumber marketplace yang ditemukan.

---

## 9. Aturan Verification Status

| Status | Arti |
| --- | --- |
| `pending_review` | Research selesai, menunggu konfirmasi dari engineer atau user |
| `auto_classified` | Diklasifikasikan otomatis berdasarkan format catalog number, belum dikonfirmasi |
| `approved_master_data` | Sudah dikonfirmasi dan masuk ke Cutting Tool Master |
| `rejected` | Tool dievaluasi dan tidak akan ditambahkan ke database |
| `obsolete` | Tool tidak lagi relevan (digantikan, tidak tersedia, dsb.) |

Skill ini hanya menghasilkan output dengan `pending_review` atau `auto_classified`. Status `approved_master_data` hanya dapat ditetapkan melalui skill `cutting-tool-database` setelah konfirmasi user.

---

## 10. Metadata Wajib Catalog Research Note

Setiap hasil penelitian yang disimpan harus menggunakan schema berikut:

```yaml
---
type: catalog_research
catalog_no: TBD
normalized_catalog_no: TBD
brand: TBD
manufacturer: TBD
category: TBD
tool_type: TBD
sub_type: TBD
is_cutting_tool: TBD
is_tool_holder: TBD
is_accessory: TBD
machine_compatibility:
  - TBD
compatible_operations:
  - TBD
compatible_materials:
  - TBD
confidence_level: TBD
source_urls:
  - TBD
source_type:
  - TBD
verification_status: pending_review
recommended_database_action: TBD
researched_date: TBD
researched_by: Claude
---
```

Simpan di folder `09_References/` dengan nama:

```
Research - [Brand] [Catalog No].md
```

Contoh:
```
Research - SECO DCMT11T302F1 CP500.md
```

---

## 11. Format Output Wajib

Setelah menyelesaikan penelitian, Claude **wajib** menyajikan output dalam format berikut:

```
### Hasil Penelitian Catalog Intelligence

**Input:** [catalog number / kode / deskripsi yang diberikan user]
**Normalized:** [catalog number setelah normalisasi]

---

#### Hipotesis Awal
- Brand: [nama / TBD]
- Kategori kemungkinan: [kategori]
- Alasan: [ringkasan singkat]

---

#### Bukti yang Ditemukan
| Sumber | Tipe | URL | Informasi Didapat |
| --- | --- | --- | --- |
| [nama sumber] | [jenis sumber] | [URL] | [ringkasan] |

---

#### Klasifikasi
- Kategori: [nilai dari daftar terkontrol]
- Tipe: [tipe spesifik]
- Adalah cutting tool: [Ya / Tidak / TBD]
- Adalah tool holder: [Ya / Tidak / TBD]
- Adalah aksesoris: [Ya / Tidak / TBD]

---

#### Kompatibilitas Mesin
[Daftar mesin yang kompatibel dari nilai terkontrol]
Catatan: [kondisi, ketergantungan holder, atau pembatasan]

---

#### Kompatibilitas Operasi
[Daftar operasi yang dapat dilakukan]

---

#### Kompatibilitas Material
[Daftar material, atau "TBD — tidak ada bukti cukup"]

---

#### Confidence Level
[High / Medium / Low / TBD]
Alasan: [penjelasan singkat mengapa confidence level ini dipilih]

---

#### Rekomendasi Tindakan Database
[add_to_master / add_as_catalog_research / update_existing / do_not_add / requires_engineering_confirmation]
Alasan: [penjelasan singkat]

---

#### Open Questions
| No. | Pertanyaan | Mengapa Penting |
| ---: | --- | --- |
| 1 | [pertanyaan] | [alasan] |
```

---

## 12. Aturan Catalog Number Ambigu

Catalog number dianggap ambigu jika:

- Format tidak sesuai konvensi brand manapun.
- Catalog number sama ditemukan di lebih dari satu manufacturer.
- Terdapat typo yang nyata (karakter terbalik, spasi tidak tepat).
- User tidak yakin dengan catalog number yang diberikan.

### Penanganan Ambiguitas

| Kondisi | Tindakan |
| --- | --- |
| Format tidak dikenal | Coba berbagai variasi normalisasi, laporkan semua kemungkinan |
| Catalog number milik beberapa brand | Laporkan semua kemungkinan dengan confidence masing-masing |
| Ada typo jelas | Sebutkan kemungkinan koreksi, konfirmasi ke user sebelum melanjutkan |
| User tidak yakin | Minta user mengkonfirmasi dari fisik tool, kemasan, atau dokumen pembelian |
| Catalog number tidak ditemukan sama sekali | Tetapkan confidence `Low`, rekomendasikan `add_as_catalog_research` |

**Jangan memilih satu interpretasi dan melanjutkan tanpa melaporkan ambiguitas ke user.**

---

## 13. Aturan Tidak Mengklaim Kompatibilitas Berlebihan

Ini adalah aturan paling kritis dalam skill ini.

### Yang Dilarang

- Mengklaim tool kompatibel dengan mesin hanya karena ukurannya cocok.
- Mengklaim turning insert bisa digunakan di Swiss-type tanpa bukti eksplisit bahwa holder-nya tersedia.
- Mengklaim endmill kompatibel dengan CNC Turning tanpa bukti live-tooling setup.
- Mengklaim tool cocok untuk Ti6Al4V hanya karena tool tersebut untuk material keras secara umum.
- Menetapkan confidence `High` jika kompatibilitas hanya berdasarkan asumsi.
- Menggunakan kata "dapat digunakan" atau "cocok" tanpa menyebutkan sumber atau kondisi.

### Yang Wajib Dilakukan

- Setiap klaim kompatibilitas harus disertai sumber atau alasan.
- Jika kompatibilitas bergantung pada holder atau setup, sebutkan ketergantungan tersebut secara eksplisit.
- Jika tidak ada bukti, gunakan `TBD` dan tambahkan ke Open Questions.
- Gunakan bahasa yang tepat: *"berdasarkan datasheet manufacturer"*, *"perlu konfirmasi holder"*, *"tidak ditemukan bukti"*.

---

## 14. Aturan Menambahkan Hasil ke Cutting Tool Master

Hasil catalog research **tidak otomatis** masuk ke Cutting Tool Master.

### Syarat Sebelum Menambahkan ke Master

Minimal harus terpenuhi:

- [ ] Catalog number sudah dikonfirmasi benar (bukan typo atau ambigu).
- [ ] Brand dan manufacturer sudah diidentifikasi.
- [ ] Kategori sudah ditentukan (bukan `TBD`).
- [ ] Minimal satu nilai `machine_compatibility` sudah diisi (bukan semua `TBD`).
- [ ] `confidence_level` minimal `Medium`.
- [ ] User sudah mengkonfirmasi atau memberikan persetujuan.

### Prosedur Penambahan

1. Setelah research selesai, presentasikan output format Section 11 ke user.
2. Tanyakan: *"Apakah data ini cukup untuk ditambahkan ke Cutting Tool Master?"*
3. Jika user mengkonfirmasi → gunakan skill `cutting-tool-database` untuk membuat note baru.
4. Jika user meminta review lebih lanjut → simpan sebagai `catalog_research` note di `09_References/`.
5. Jika confidence `Low` → rekomendasikan `add_as_catalog_research` dan jelaskan informasi apa yang masih kurang.

Jangan membuat Cutting Tool Master note secara langsung dari skill ini tanpa konfirmasi user.
