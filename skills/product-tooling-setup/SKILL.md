---
skill_id: product-tooling-setup
version: 1.0.0
status: active
purpose: Mengelola tooling setup spesifik per produk — menjawab pertanyaan "untuk produk ini, di mesin ini, operasi ini, tool apa yang dibutuhkan?" — dengan menghubungkan Product Master, Machine Master, Cutting Tool Master, CNC Program, Job Card, dan bukti trial.
triggers:
  - buat tooling setup baru
  - update tooling setup
  - gunakan tooling dari trial sebelumnya
  - ganti tool di setup
  - tandai setup sebagai proven
  - review tooling setup produk
  - tooling setup produk varian baru
  - supersede tooling setup lama
  - cek tool yang dipakai untuk produk ini
related_skills:
  - cutting-tool-database
  - cutting-tool-catalog-intelligence
  - rnd-job-card-copilot
  - cnc-program-revision-control
  - obsidian-vault-traceability
---

# Skill: Product Tooling Setup

## 1. Tujuan Skill

Skill ini mengelola **Product Tooling Setup** — dokumentasi kombinasi tool yang dibutuhkan untuk membuat produk tertentu, pada mesin tertentu, untuk operasi tertentu.

Skill ini menjawab pertanyaan:

```
Untuk produk ini, di mesin ini, operasi ini — tool apa yang dibutuhkan, di posisi mana, dengan fungsi apa?
```

Cakupan skill ini:

- Membuat Product Tooling Setup note baru.
- Menggunakan ulang setup dari trial sebelumnya sebagai dasar setup baru.
- Memperbarui setup selama trial berlangsung.
- Mencatat penggantian tool dan alasannya.
- Mengelola status setup sepanjang siklus trial.
- Menghubungkan setup ke semua record terkait.
- Memastikan traceability antara tool, produk, mesin, program CNC, dan hasil trial.

---

## 2. Kapan Menggunakan Skill Ini

Gunakan skill ini ketika:

- User ingin mendefinisikan tool yang akan digunakan untuk trial produk baru.
- User ingin mendokumentasikan tooling setup dari Job Card yang baru selesai.
- User ingin menggunakan kembali tooling setup dari trial sebelumnya.
- User melaporkan penggantian tool selama trial berlangsung.
- User ingin menandai setup sebagai proven setelah trial berhasil.
- User ingin membandingkan tooling setup antar produk, mesin, atau program CNC.
- User ingin membuat setup untuk varian produk berdasarkan setup produk yang sudah ada.
- Setup lama sudah tidak relevan dan perlu disupersede.

---

## 3. Perbedaan: Cutting Tool Master, Product Tooling Setup, dan Tool Assembly

Ketiga record ini berbeda dan tidak boleh dicampur.

### Cutting Tool Master
- **Apa ini:** Record identitas dan spesifikasi satu tool secara umum.
- **Menjawab:** "Tool ini apa, buat apa, bisa dipakai di mesin apa?"
- **Scope:** Satu tool, berlaku lintas produk dan mesin.
- **Contoh:** `SECO DCMT11T302F1 CP500 Turning Insert`

### Product Tooling Setup
- **Apa ini:** Record kombinasi tool yang dibutuhkan untuk satu produk + mesin + operasi.
- **Menjawab:** "Produk ini pakai tool apa, di posisi mana, dengan fungsi apa?"
- **Scope:** Satu kombinasi produk + mesin + operasi.
- **Contoh:** `Emerald 6.5 mm Poly Screw L45 - CITIZEN A20-3F7 No.1 - Tooling Setup`

### Tool Assembly
- **Apa ini:** Record kombinasi komponen tool yang dirakit menjadi satu unit siap pakai (holder + insert + collet, dsb.).
- **Menjawab:** "Komponen apa saja yang membentuk satu unit tool pada posisi tertentu?"
- **Scope:** Satu posisi tool pada satu mesin.
- **Contoh:** `TA - T01 - Holder PDJNR + DCMT Insert - CITIZEN A20-3F7 No.1`

**Aturan:** Jangan menggabungkan ketiga record ini dalam satu note. Buat note terpisah untuk masing-masing.

---

## 4. Alur Kerja Product Tooling Setup

### 4.1 Membuat Setup Baru

Gunakan alur ini saat membuat Product Tooling Setup dari awal.

1. Identifikasi produk: nama, item_no, product_family, material.
2. Identifikasi mesin: nama mesin, nomor mesin.
3. Identifikasi operasi: nomor operasi, tipe operasi.
4. Tanyakan atau cek apakah ada setup sebelumnya untuk produk serupa di mesin yang sama.
5. Jika ada setup referensi → gunakan sebagai dasar (lihat 4.2).
6. Jika tidak ada → mulai dari kosong.
7. Isi tabel tooling dengan informasi yang diketahui. Gunakan `TBD` untuk yang belum diketahui.
8. Tentukan fungsi setiap tool. Gunakan `TBD` jika belum diketahui.
9. Tentukan criticality setiap tool (lihat Section 8).
10. Hubungkan ke Product Master, Machine Master, CNC Program, dan source Job Card.
11. Tetapkan status: `draft` (jika belum trial) atau `under_trial` (jika trial aktif).
12. Simpan, commit, push.

### 4.2 Menggunakan Setup Sebelumnya

Gunakan alur ini saat membuat setup baru berdasarkan setup yang sudah ada.

1. Identifikasi setup referensi yang akan digunakan.
2. Buat note baru — **jangan overwrite setup lama**.
3. Salin tooling table dari setup referensi ke setup baru.
4. Perbarui field yang berbeda: produk, mesin, operasi, program CNC, source Job Card.
5. Tandai setup lama di field `superseded_by` jika setup baru menggantikannya.
6. Tambahkan catatan di Section "Riwayat Setup": "Berdasarkan [[Nama Setup Referensi]]".
7. Tetapkan status: `draft` atau `under_trial`.
8. Simpan, commit, push.

### 4.3 Memperbarui Setup Selama Trial

Gunakan alur ini saat ada perubahan tool selama trial berlangsung.

1. Identifikasi setup yang sedang aktif.
2. Jangan hapus baris tool yang diganti — tambahkan baris baru di tabel dengan status `replaced`.
3. Catat alasan penggantian di kolom `Remark`.
4. Catat referensi Job Card dan trial number di kolom `Source Job Card`.
5. Perbarui field `cnc_revision` jika ada perubahan program terkait.
6. Perbarui field `status` setup menjadi `under_trial` jika belum.
7. Simpan, commit, push.

### 4.4 Mengganti Tool dalam Setup

1. Tambahkan baris baru di tabel untuk tool pengganti.
2. Beri keterangan di kolom `Remark` pada baris lama: `Diganti oleh [Item No. tool baru] — [alasan] — Trial [nomor trial]`.
3. Perbarui baris lama dengan status `replaced` di kolom `Status`.
4. Catat cross-reference ke Cutting Tool Master tool lama dan baru.
5. Jika penggantian berdampak pada dimensi kritis, pastikan ada Inspection Record terkait.

### 4.5 Menandai Setup sebagai Under Trial

Ubah status menjadi `under_trial` ketika:

- Trial pertama menggunakan setup ini sudah dimulai.
- Job Card dengan setup ini sudah dalam status `trial_ongoing`.

Tambahkan di field `source_job_card` nama Job Card yang sedang menggunakan setup ini.

### 4.6 Menandai Setup sebagai Proven

Lihat Section 13 untuk aturan lengkap.

Ubah status menjadi `proven` hanya setelah semua syarat terpenuhi.

### 4.7 Mensupersede Setup Lama

Gunakan alur ini saat setup baru menggantikan setup lama secara permanen.

1. Buat setup baru terlebih dahulu (lihat 4.1 atau 4.2).
2. Pada setup lama, ubah `status` menjadi `superseded`.
3. Tambahkan field `superseded_by` pada setup lama dengan nama note setup baru.
4. Pindahkan setup lama ke `10_Archive/` jika sudah tidak aktif.
5. Simpan, commit, push.

---

## 5. Metadata Wajib Product Tooling Setup

Setiap Product Tooling Setup note harus memiliki YAML frontmatter berikut:

```yaml
---
type: product_tooling_setup
setup_id: TBD
product: TBD
item_no: TBD
product_family: TBD
machine: TBD
operation_no: TBD
operation: TBD
material: TBD
cnc_program: TBD
cnc_revision: TBD
source_job_card: TBD
status: draft
superseded_by: TBD
based_on_setup: TBD
related_inspection_records: []
related_issue_records: []
proven_date: TBD
proven_by: TBD
---
```

### Penjelasan Field Kunci

| Field | Penjelasan |
| --- | --- |
| `setup_id` | ID internal unik (format: `TS-XXXXX`) |
| `product` | Nama produk — gunakan nama persis dari Product Master note |
| `item_no` | Nomor item internal produk |
| `product_family` | Nama product family |
| `machine` | Nama mesin — gunakan nama persis dari Machine Master note |
| `operation_no` | Nomor operasi di routing |
| `operation` | Tipe operasi (Turning, Milling, Threading, dsb.) |
| `material` | Material produk |
| `cnc_program` | Nama note CNC Program Master yang terkait |
| `cnc_revision` | Revisi CNC program yang digunakan saat setup ini dibuat |
| `source_job_card` | Nama note Job Card yang menjadi sumber setup ini |
| `status` | Status dari nilai terkontrol (lihat Section 9) |
| `superseded_by` | Nama note setup baru yang menggantikan setup ini |
| `based_on_setup` | Nama note setup referensi jika setup ini diturunkan dari setup lain |
| `related_inspection_records` | Daftar nama note Inspection Record yang terkait |
| `related_issue_records` | Daftar nama note Issue Record yang terkait |
| `proven_date` | Tanggal setup dinyatakan proven (format: `YYYY-MM-DD`) |
| `proven_by` | Nama atau jabatan yang menyatakan proven |

---

## 6. Tabel Tooling Setup Wajib

Setiap Product Tooling Setup note harus memiliki tabel berikut:

```markdown
| Tool Pos | Item No. | Tool Name | Category | Tool Type | Function | Criticality | Source Job Card | Status | Remark |
| --- | ---: | --- | --- | --- | --- | --- | --- | --- | --- |
```

### Penjelasan Kolom

| Kolom | Penjelasan |
| --- | --- |
| `Tool Pos` | Nomor posisi tool di mesin (T01, T02, dsb.) atau turret position |
| `Item No.` | Nomor item internal tool — link ke Cutting Tool Master jika ada |
| `Tool Name` | Nama tool dengan format `[[Cutting Tool Master Note Name]]` jika sudah ada |
| `Category` | Kategori dari daftar terkontrol di skill `cutting-tool-database` |
| `Tool Type` | Tipe spesifik tool |
| `Function` | Fungsi tool dalam operasi ini (Facing, OD Turning, Threading, dsb.) — gunakan `TBD` jika tidak diketahui |
| `Criticality` | High / Medium / Low / TBD (lihat Section 8) |
| `Source Job Card` | Nama Job Card tempat tool ini pertama kali digunakan dalam setup ini |
| `Status` | `active`, `replaced`, `TBD` |
| `Remark` | Catatan tambahan, termasuk alasan penggantian jika `replaced` |

### Contoh Baris Tabel

```markdown
| T01 | 4421 | [[SECO DCMT11T302F1 CP500 Turning Insert]] | Insert | DCMT Insert | OD Turning | High | [[RD20260605001 - Emerald 6.5 mm Poly Screw L45 - CITIZEN A20-3F7 No.1]] | active | Grade CP500 untuk Ti6Al4V |
| T02 | TBD | Threading Insert 16ER AG60 | Insert | Threading Insert | Threading | High | [[RD20260605001 - Emerald 6.5 mm Poly Screw L45 - CITIZEN A20-3F7 No.1]] | active | TBD |
```

---

## 7. Section Wajib Product Tooling Setup Note

```markdown
# [Nama Produk] - [Nama Mesin] - Tooling Setup

## 1. Informasi Setup

## 2. Tooling Table

## 3. Catatan Fungsi dan Kritikalitas

## 4. Riwayat Perubahan Tool

## 5. Bukti Trial dan Hasil Inspeksi

## 6. Traceability Links

## 7. Open Questions

## 8. Kesimpulan dan Status
```

---

## 8. Aturan Kritikalitas Tool

Tentukan criticality berdasarkan dampak tool terhadap kualitas produk.

### High
Gunakan jika tool berpengaruh langsung terhadap:
- Dimensi kritis (diameter, panjang, toleransi ketat).
- Kualitas permukaan pada area fungsional.
- Geometri ulir.
- Operasi yang menentukan go/no-go pada inspeksi.

**Default:** Jika tool mempengaruhi dimensi kritis dan belum ada bukti sebaliknya, tetapkan `High`.

### Medium
Gunakan jika tool berpengaruh terhadap:
- Dimensi non-kritis atau referensi.
- Permukaan yang tidak fungsional tetapi mempengaruhi penampilan.
- Operasi antara yang hasilnya akan diproses lagi.

### Low
Gunakan jika tool digunakan untuk:
- Operasi finishing minor (chamfer kecil, deburring).
- Operasi yang tidak mempengaruhi dimensi akhir produk.
- Setup aids atau workholding non-kritis.

### TBD
Gunakan jika fungsi tool belum diketahui atau belum dikonfirmasi.

**Aturan:** Jangan downgrade criticality dari `High` ke `Medium` atau `Low` tanpa dasar dari hasil inspeksi atau konfirmasi engineer.

---

## 9. Nilai Status Setup

| Status | Arti |
| --- | --- |
| `draft` | Setup dibuat tetapi belum digunakan dalam trial |
| `under_trial` | Setup sedang digunakan dalam trial aktif |
| `proven` | Setup terbukti menghasilkan produk yang memenuhi syarat berdasarkan hasil trial |
| `superseded` | Setup ini digantikan oleh setup yang lebih baru |
| `obsolete` | Setup tidak lagi relevan karena produk atau mesin sudah tidak aktif |
| `rejected` | Setup terbukti tidak memenuhi syarat berdasarkan evaluasi trial |
| `TBD` | Status belum ditentukan |

### Aturan Transisi Status

- `draft` → `under_trial`: saat Job Card terkait masuk status `trial_ongoing`.
- `under_trial` → `proven`: hanya setelah syarat di Section 13 terpenuhi.
- `under_trial` → `rejected`: jika trial mengonfirmasi setup tidak layak.
- `proven` → `superseded`: jika ada setup baru yang menggantikan.
- Status `proven` **tidak dapat** dikembalikan ke `under_trial` atau `draft`.
- Jika ada perubahan signifikan pada setup `proven`, buat setup baru dan supersede yang lama.

---

## 10. Aturan Linking ke Record Lain

### Link ke Product Master

```markdown
- Produk: [[Emerald 6.5 mm Poly Screw - Ti6Al4V ELI - L45]]
```

Field YAML: `product` diisi dengan nama persis note Product Master.

### Link ke Machine Master

```markdown
- Mesin: [[CITIZEN A20-3F7 - No.1]]
```

Field YAML: `machine` diisi dengan nama persis note Machine Master.

### Link ke Cutting Tool Master

Setiap tool di tabel tooling harus menggunakan internal link pada kolom `Tool Name`:

```markdown
[[SECO DCMT11T302F1 CP500 Turning Insert]]
```

Jika Cutting Tool Master note belum ada:
- Gunakan nama deskriptif sebagai placeholder.
- Tambahkan ke Open Questions: *"Cutting Tool Master note untuk [nama tool] belum dibuat."*
- Rekomendasikan skill `cutting-tool-database` untuk membuat note tersebut.

### Link ke CNC Program

```markdown
- Program CNC: [[Program 2513 - CITIZEN A20-3F7 No.1]]
```

Field YAML: `cnc_program` diisi nama note CNC Program Master.

### Link ke Job Card

```markdown
- Source Job Card: [[RD20260605001 - Emerald 6.5 mm Poly Screw L45 - CITIZEN A20-3F7 No.1]]
```

Field YAML: `source_job_card` diisi nama note Job Card.

### Link ke Inspection Record

Tambahkan di Section 5 (Bukti Trial) dan field YAML `related_inspection_records`:

```markdown
- [[IR-RD20260605001-T01]]
- [[IR-RD20260605001-T02]]
```

### Link ke Issue / Deviation Record

Tambahkan di Section 5 dan field YAML `related_issue_records`:

```markdown
- [[ISS-RD20260605001-T01-Thread Burr]]
```

---

## 11. Aturan Penggantian Tool

Saat tool dalam setup diganti selama trial:

1. **Jangan hapus baris lama** dari tabel — ubah kolom `Status` menjadi `replaced`.
2. Tambahkan baris baru di tabel untuk tool pengganti.
3. Isi kolom `Remark` pada baris lama:
   ```
   Diganti oleh [Item No. tool baru] — [alasan singkat] — Trial [nomor trial] — [[Job Card]]
   ```
4. Isi kolom `Source Job Card` pada baris baru dengan Job Card tempat penggantian terjadi.
5. Catat di Section 4 (Riwayat Perubahan Tool):
   ```markdown
   | Tanggal | Trial No. | Posisi | Tool Lama | Tool Baru | Alasan | Job Card |
   | --- | --- | --- | --- | --- | --- | --- |
   ```
6. Jika penggantian tool menyebabkan perubahan CNC program → rekomendasikan skill `cnc-program-revision-control`.
7. Jika tool lama yang diganti memiliki status `proven` di Cutting Tool Master → jangan ubah status tersebut, tapi catat bahwa tool diganti untuk setup ini.

---

## 12. Aturan Bukti Sumber (Source Evidence)

Setup hanya dapat dinaikkan statusnya berdasarkan bukti yang terdokumentasi.

### Hierarki Bukti

| Tingkat | Sumber Bukti | Kekuatan |
| --- | --- | --- |
| 1 | Inspection Record dengan hasil Pass dari Job Card | Tertinggi |
| 2 | Catatan trial eksplisit dalam Job Card (output qty, cycle time) | Tinggi |
| 3 | Konfirmasi verbal dari engineer yang terdokumentasi di Job Card | Sedang |
| 4 | Reuse dari setup proven produk serupa tanpa trial baru | Rendah — perlu trial konfirmasi |
| 5 | Asumsi atau pengalaman tanpa dokumentasi | Tidak diterima |

### Aturan Penggunaan Bukti

- Selalu sebutkan sumber bukti secara eksplisit saat mengubah status setup.
- Jangan klaim setup `proven` berdasarkan bukti tingkat 4 atau 5.
- Jika bukti lemah, gunakan status `under_trial` dan catat di Open Questions bukti apa yang masih dibutuhkan.

---

## 13. Aturan Menandai Setup sebagai Proven

Setup hanya dapat dinyatakan `proven` jika **semua** syarat berikut terpenuhi:

```
[ ] Ada minimal satu Inspection Record terkait dengan hasil Pass atau Conditional Pass.
[ ] Output quantity dari trial sudah tercatat di Job Card terkait.
[ ] Tidak ada isu kritis yang masih berstatus Open di Issue Record terkait.
[ ] Semua tool dalam setup sudah teridentifikasi (tidak ada baris Tool Name: TBD untuk posisi kritis).
[ ] CNC program dan revisi yang digunakan sudah terdokumentasi.
[ ] Setup ini sudah melalui minimal satu siklus trial lengkap (bukan trial parsial).
[ ] User atau engineer mengkonfirmasi bahwa setup ini diterima.
```

Jika ada syarat yang belum terpenuhi, tetap gunakan status `under_trial` dan catat syarat yang belum terpenuhi di Open Questions.

---

## 14. Aturan Produk Varian dan Produk Serupa

### Produk Varian (Panjang, Diameter Berbeda)

- Buat Product Tooling Setup **terpisah** untuk setiap varian jika tooling berbeda.
- Jika tooling identik kecuali satu atau dua parameter, buat setup baru dan isi field `based_on_setup` dengan nama setup referensi.
- Catat perbedaan secara eksplisit di Section 1 (Informasi Setup).

### Produk dari Product Family yang Sama

- Produk dari satu product family dapat berbagi setup sebagai referensi, tapi **tidak boleh berbagi satu note setup yang sama**.
- Buat setup baru untuk setiap produk. Gunakan `based_on_setup` untuk menunjukkan asal referensi.
- Jangan asumsikan tooling identik hanya karena produk dari family yang sama — perbedaan ukuran, toleransi, atau material dapat memerlukan tool berbeda.

### Produk di Mesin Berbeda

- Buat **satu setup per mesin** meskipun produk dan operasinya sama.
- Kompatibilitas tool holder, posisi, dan sistem tooling dapat berbeda antar mesin.
- Jangan menyalin setup dari satu mesin ke mesin lain tanpa konfirmasi bahwa holder system-nya kompatibel.

---

## 15. Format Respons Wajib Setelah Membuat atau Memperbarui Tooling Setup

Setelah menyelesaikan tugas yang melibatkan skill ini, Claude **wajib** melaporkan:

```
### Hasil Product Tooling Setup

**Tindakan yang dilakukan:**
- [Setup yang dibuat / diperbarui / disupersede]

**Validasi setup:**
- Produk teridentifikasi: [Ya / Tidak]
- Mesin teridentifikasi: [Ya / Tidak]
- Operasi teridentifikasi: [Ya / Tidak]
- Semua posisi tool terisi: [Ya / Sebagian — sebutkan posisi TBD]
- Semua fungsi tool terisi: [Ya / Sebagian — sebutkan posisi TBD]
- Criticality sudah ditentukan: [Ya / Sebagian — sebutkan posisi TBD]

**Traceability:**
- Link ke Product Master: [ada / belum ada]
- Link ke Machine Master: [ada / belum ada]
- Link ke CNC Program: [ada / belum ada]
- Link ke Job Card: [ada / belum ada]
- Link ke Cutting Tool Master (per tool): [lengkap / ada yang belum — sebutkan]
- Link ke Inspection Record: [ada / belum ada / tidak relevan]
- Link ke Issue Record: [ada / belum ada / tidak relevan]

**Status setup saat ini:** [draft / under_trial / proven / superseded / obsolete / rejected / TBD]

**Syarat proven yang belum terpenuhi (jika ada):**
- [Daftar syarat yang masih kurang]

**Open Questions:**
- [Daftar pertanyaan yang ditambahkan]

**Rekomendasi:**
- [Apakah ada Cutting Tool Master yang perlu dibuat? → skill cutting-tool-database]
- [Apakah ada perubahan CNC yang perlu dicatat? → skill cnc-program-revision-control]

**Git:**
- Files changed: [daftar file]
- Commit: [hash]
- Branch: [nama branch]
- Push: [berhasil / gagal — detail jika gagal]

**Langkah selanjutnya:**
- [Rekomendasi tindakan berikutnya]
```

Jangan klaim setup `proven` dalam laporan ini jika syarat di Section 13 belum terpenuhi seluruhnya.
