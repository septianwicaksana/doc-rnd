---
skill_id: cnc-program-revision-control
version: 1.0.0
status: active
purpose: Mengelola dokumentasi dan traceability revisi program CNC — mencatat setiap perubahan program dengan alasan, produk terdampak, mesin terdampak, Job Card terkait, before/after perubahan, risiko, kebutuhan verifikasi, hasil trial setelah revisi, dan keputusan akhir.
triggers:
  - catat perubahan CNC
  - buat revisi program CNC
  - update program CNC
  - before after perubahan program
  - program CNC baru
  - dokumentasi revisi
  - CNC program revision
  - perubahan feed speed depth
  - koreksi dimensi via program
  - review riwayat revisi CNC
related_skills:
  - rnd-job-card-copilot
  - product-tooling-setup
  - obsidian-vault-traceability
---

# Skill: CNC Program Revision Control

## 1. Tujuan Skill

Skill ini mengelola **dokumentasi dan traceability revisi program CNC** untuk kegiatan R&D Manufacturing.

Setiap perubahan pada program CNC — sekecil apapun — harus terdokumentasi dengan:

- Alasan perubahan yang jelas.
- Area program yang berubah.
- Kondisi before dan after.
- Penilaian risiko terhadap kualitas dan dimensi produk.
- Kebutuhan verifikasi setelah perubahan.
- Hasil trial setelah revisi diterapkan.
- Keputusan akhir apakah revisi diterima atau ditolak.

Skill ini menjaga dua jenis record:

1. **CNC Program Master** — identitas dan status terkini satu program CNC.
2. **CNC Revision Record** — dokumentasi satu event perubahan pada satu program CNC.

---

## 2. Kapan Menggunakan Skill Ini

Gunakan skill ini ketika:

- User melaporkan perubahan pada program CNC selama atau setelah trial.
- User ingin membuat record program CNC baru.
- User ingin mendokumentasikan revisi: before/after, alasan, dan dampak.
- User ingin mengecek riwayat revisi suatu program.
- User ingin mengetahui revisi CNC mana yang sedang aktif.
- Ada perubahan feed, speed, depth of cut, tool offset, atau tool path yang perlu dicatat.
- Ada isu kualitas yang diselesaikan melalui perubahan program.
- Perubahan program CNC memerlukan retest atau verifikasi.
- File CNC fisik berubah dan perlu dicommit bersama dokumentasinya.

---

## 3. Konsep CNC Program Master

CNC Program Master adalah record identitas dan status terkini dari satu program CNC.

CNC Program Master menjawab pertanyaan:

```
Program nomor berapa ini? Untuk produk apa? Di mesin mana? Revisi berapa yang aktif sekarang?
```

Satu CNC Program Master per kombinasi `program_no` + `machine`.

Jika program yang sama dijalankan di mesin berbeda dengan perbedaan signifikan, buat Program Master terpisah per mesin.

---

## 4. Konsep CNC Revision Record

CNC Revision Record adalah record satu event perubahan pada program CNC.

Satu Revision Record dibuat **setiap kali** ada perubahan pada program, tanpa terkecuali.

CNC Revision Record menjawab pertanyaan:

```
Pada revisi ini, apa yang berubah, kenapa berubah, apa risikonya, dan apa hasilnya setelah dicoba?
```

Revision Record **tidak menggantikan** Program Master — keduanya harus ada dan terhubung.

---

## 5. Metadata Wajib CNC Program Master

```yaml
---
type: cnc_program
program_no: TBD
program_name: TBD
machine: TBD
product: TBD
item_no: TBD
operation_no: TBD
operation: TBD
current_revision: TBD
first_revision: REV00
status: active
related_job_cards: []
related_revisions: []
file_path: TBD
---
```

### Penjelasan Field Kunci

| Field | Penjelasan |
| --- | --- |
| `program_no` | Nomor program CNC (contoh: `2513`) |
| `program_name` | Nama deskriptif program jika ada |
| `machine` | Nama mesin — gunakan nama persis dari Machine Master note |
| `product` | Nama produk utama yang menggunakan program ini |
| `item_no` | Nomor item produk |
| `operation_no` | Nomor operasi di routing produk |
| `operation` | Tipe operasi (Turning, Milling, dsb.) |
| `current_revision` | Revisi yang sedang aktif saat ini (contoh: `REV02`) |
| `first_revision` | Revisi pertama saat program dibuat — selalu `REV00` |
| `status` | `active`, `inactive`, `obsolete` |
| `related_job_cards` | Daftar Job Card yang menggunakan program ini |
| `related_revisions` | Daftar nama note CNC Revision Record yang terhubung |
| `file_path` | Path file CNC fisik di repo Git jika ada (relatif dari root) |

---

## 6. Metadata Wajib CNC Revision Record

```yaml
---
type: cnc_revision
program_no: TBD
revision: TBD
previous_revision: TBD
product: TBD
item_no: TBD
machine: TBD
operation_no: TBD
operation: TBD
job_card: TBD
trial_no: TBD
change_date: TBD
changed_by: TBD
reviewed_by: TBD
approved_by: TBD
reason_for_change: TBD
change_type:
  - TBD
risk_level: TBD
verification_required:
  - TBD
trial_result_after_change: TBD
status: draft
---
```

### Penjelasan Field Kunci

| Field | Penjelasan |
| --- | --- |
| `program_no` | Nomor program CNC yang direvisi |
| `revision` | Nomor revisi ini (contoh: `REV01`) |
| `previous_revision` | Nomor revisi sebelumnya (contoh: `REV00`) |
| `product` | Nama produk yang terpengaruh |
| `item_no` | Nomor item produk |
| `machine` | Nama mesin |
| `operation_no` | Nomor operasi |
| `job_card` | Nama note Job Card yang memicu revisi ini |
| `trial_no` | Nomor trial dalam Job Card (contoh: `T02`) |
| `change_date` | Tanggal perubahan dibuat (format: `YYYY-MM-DD`) |
| `changed_by` | Nama atau jabatan yang membuat perubahan |
| `reviewed_by` | Nama atau jabatan yang mereview — `TBD` jika belum |
| `approved_by` | Nama atau jabatan yang menyetujui — `TBD` jika belum |
| `reason_for_change` | Alasan singkat mengapa perubahan diperlukan |
| `change_type` | Satu atau lebih nilai dari daftar terkontrol (lihat Section 12) |
| `risk_level` | Low / Medium / High / TBD (lihat Section 13) |
| `verification_required` | Daftar verifikasi yang diperlukan setelah revisi |
| `trial_result_after_change` | Pass / Fail / Conditional Pass / Pending / TBD |
| `status` | `draft`, `under_review`, `approved`, `rejected`, `superseded` |

---

## 7. Section Wajib CNC Revision Record

```markdown
# CNC Revision - Program [Nomor Program] - [Revisi]

## 1. Ringkasan Revisi

## 2. Alasan Perubahan

## 3. Produk yang Terpengaruh

## 4. Mesin yang Terpengaruh

## 5. Job Card Terkait

## 6. Area yang Diubah

## 7. Kondisi Sebelum Perubahan

## 8. Kondisi Setelah Perubahan

## 9. Penilaian Risiko

## 10. Verifikasi yang Diperlukan

## 11. Hasil Trial Setelah Perubahan

## 12. Keputusan

## 13. Persetujuan
```

---

## 8. Aturan Penomoran Revisi

### Format

```
REV00   — Revisi pertama (program awal / baseline)
REV01   — Revisi pertama setelah REV00
REV02   — Revisi kedua setelah REV00
REV03   — dst.
```

### Aturan

- **REV00** adalah revisi baseline — program pertama kali dibuat atau pertama kali didokumentasikan.
- Setiap revisi berikutnya menambahkan angka secara berurutan: REV01, REV02, REV03.
- Tidak ada revisi yang dilewati — jika REV01 sudah ada, revisi berikutnya adalah REV02, bukan REV03 atau REV1A.
- Tidak ada sistem sub-revisi (REV01A, REV01B) — gunakan revisi penuh berikutnya.
- Jika program sudah berjalan tapi belum ada dokumentasi revisi sebelumnya, mulai dari revisi tertinggi yang diketahui. Dokumentasikan kondisi saat ini sebagai revisi aktif dan catat di Open Questions bahwa riwayat sebelumnya belum terdokumentasi.
- Nomor revisi pada nama file harus konsisten dengan field `revision` di YAML.

### Naming Convention Revision Record

```
Program [Nomor Program] - [Revisi] - [Alasan Singkat].md
```

Contoh:
```
Program 2513 - REV01 - Reduce Thread Burr.md
Program 2513 - REV02 - Dimensional Correction OD.md
```

### Naming Convention Program Master

```
Program [Nomor Program] - [Nama Mesin].md
```

Contoh:
```
Program 2513 - CITIZEN A20-3F7 No.1.md
```

---

## 9. Aturan Before / After Perubahan CNC

### Kewajiban Mencatat Before/After

Jika user menyediakan snippet kode CNC aktual, **wajib** dicatat dalam Section 7 (Sebelum) dan Section 8 (Sesudah) menggunakan fenced code block.

### Format Fenced Code Block

Gunakan label bahasa yang sesuai:

Untuk G-code / CNC program standar:
````markdown
```gcode
[isi kode CNC]
```
````

Untuk program Swiss-type / Citizen:
````markdown
```
[isi kode CNC]
```
````

### Aturan Penting

- **Jangan mengubah atau mengoreksi** kode CNC yang diberikan user kecuali diminta secara eksplisit.
- **Preservasi persis** — spasi, indentasi, comment, dan karakter khusus harus dipertahankan.
- Jika hanya sebagian kode yang berubah, dokumentasikan hanya area yang berubah, bukan seluruh program.
- Tambahkan keterangan baris di luar code block untuk menjelaskan parameter yang berubah.

### Jika Kode Tidak Tersedia

Jika user tidak memberikan kode aktual, dokumentasikan perubahan secara deskriptif:

```markdown
## 7. Kondisi Sebelum Perubahan

Parameter tidak tersedia dalam format kode. Perubahan didokumentasikan secara deskriptif.

- Feed rate threading: 0.15 mm/rev
- Speed: 1200 RPM

## 8. Kondisi Setelah Perubahan

- Feed rate threading: 0.12 mm/rev (dikurangi 20%)
- Speed: 1200 RPM (tidak berubah)
```

Tambahkan ke Open Questions: *"Kode CNC aktual untuk before/after belum terdokumentasi. Minta user untuk menyediakan snippet program."*

---

## 10. Aturan Linking CNC Revision ke Record Lain

### Link ke R&D Job Card

Field YAML `job_card` diisi nama note Job Card yang memicu revisi.

Di Section 5 (Job Card Terkait):
```markdown
- Job Card: [[RD20260605001 - Emerald 6.5 mm Poly Screw L45 - CITIZEN A20-3F7 No.1]]
- Trial No.: T02
```

### Link ke Product Master

Field YAML `product` diisi nama produk. Di Section 3 (Produk Terpengaruh):
```markdown
- Produk: [[Emerald 6.5 mm Poly Screw - Ti6Al4V ELI - L45]]
- Item No.: [nomor item]
```

### Link ke Machine Master

Field YAML `machine` diisi nama mesin. Di Section 4 (Mesin Terpengaruh):
```markdown
- Mesin: [[CITIZEN A20-3F7 - No.1]]
```

### Link ke Inspection Record

Di Section 11 (Hasil Trial):
```markdown
- Hasil inspeksi: [[IR-RD20260605001-T02]]
```

### Link ke Issue / Deviation Record

Di Section 2 (Alasan Perubahan) jika revisi dipicu oleh isu:
```markdown
- Isu yang memicu revisi: [[ISS-RD20260605001-T01-Thread Burr]]
```

### Link ke Product Tooling Setup

Di Section 6 (Area yang Diubah) jika revisi berdampak pada tooling:
```markdown
- Setup tooling terkait: [[Emerald 6.5 mm Poly Screw L45 - CITIZEN A20-3F7 No.1 - Tooling Setup]]
```

### Update CNC Program Master

Setelah Revision Record selesai:
1. Perbarui field `current_revision` di CNC Program Master dengan nomor revisi baru.
2. Tambahkan nama Revision Record ke daftar `related_revisions` di Program Master.
3. Commit kedua file bersamaan.

---

## 11. Nilai Terkontrol Tipe Perubahan CNC

Gunakan hanya nilai berikut untuk field `change_type`. Boleh memilih lebih dari satu.

| Nilai | Keterangan |
| --- | --- |
| `Feed adjustment` | Perubahan feed rate (mm/rev atau mm/min) |
| `Speed adjustment` | Perubahan spindle speed (RPM atau m/min) |
| `Depth of cut adjustment` | Perubahan kedalaman pemotongan |
| `Tool path change` | Perubahan jalur tool (approach, retract, kontur) |
| `Tool offset change` | Perubahan nilai offset tool di program |
| `Tool change` | Penggantian tool yang dipanggil dalam program |
| `Operation sequence change` | Perubahan urutan operasi atau sub-operasi |
| `Coolant command change` | Perubahan perintah coolant (ON/OFF, tipe, tekanan) |
| `Subprogram change` | Perubahan pada subprogram atau macro |
| `Cycle time optimization` | Perubahan yang bertujuan mengurangi cycle time |
| `Burr reduction` | Perubahan yang bertujuan mengurangi burr |
| `Dimensional correction` | Perubahan untuk memperbaiki dimensi produk |
| `Surface finish improvement` | Perubahan untuk meningkatkan kualitas permukaan |
| `Tool life improvement` | Perubahan untuk memperpanjang umur tool |
| `Other` | Perubahan lain yang tidak masuk kategori di atas |
| `TBD` | Tipe perubahan belum ditentukan |

---

## 12. Aturan Risk Level

Risk level dinilai berdasarkan dampak potensial terhadap kualitas dan keselamatan produk.

### High

Gunakan jika perubahan:
- Berpotensi mengubah dimensi kritis (diameter, panjang, toleransi ketat).
- Mempengaruhi geometri ulir atau thread profile.
- Mengubah surface finish pada area fungsional implan.
- Mengubah urutan operasi secara fundamental.
- Belum pernah dicoba sebelumnya pada material atau mesin ini.

**Aturan keras:** Jika perubahan menyentuh dimensi kritis, risk level **minimum Medium**. Jika dampaknya tidak dapat diprediksi, tetapkan `High`.

### Medium

Gunakan jika perubahan:
- Berpotensi mengubah dimensi non-kritis.
- Mengubah parameter cutting yang sudah pernah dicoba dalam range serupa.
- Menyentuh area yang akan diproses lebih lanjut di operasi berikutnya.
- Memiliki preseden dari produk atau program serupa.

### Low

Gunakan jika perubahan:
- Hanya mempengaruhi cycle time tanpa mengubah hasil dimensi.
- Mengubah perintah coolant tanpa mengubah jalur cutting.
- Merupakan penyesuaian minor yang sudah terbukti aman sebelumnya.
- Tidak menyentuh area kritis sama sekali.

### TBD

Gunakan jika dampak perubahan belum dapat dinilai. Tambahkan ke Open Questions dan jangan lanjutkan trial sampai risk level ditentukan.

---

## 13. Aturan Verifikasi yang Diperlukan

Isi field `verification_required` dan Section 10 (Verifikasi yang Diperlukan) berdasarkan risk level.

### Panduan Minimum Verifikasi per Risk Level

| Risk Level | Verifikasi Minimum |
| --- | --- |
| `High` | Inspection Record lengkap + konfirmasi engineer |
| `Medium` | Inspection Record untuk dimensi terdampak |
| `Low` | Observasi visual + cycle time check |

### Contoh Nilai `verification_required`

```yaml
verification_required:
  - Dimensional inspection — OD dan threading
  - Surface finish check pada area fungsional
  - Konfirmasi tidak ada burr
  - Cycle time measurement
```

### Aturan

- Jangan kosongkan `verification_required`. Minimal isi `TBD` dan tambahkan ke Open Questions.
- Verifikasi yang sudah selesai dicatat hasilnya di Section 11 (Hasil Trial Setelah Perubahan).
- Jika verifikasi belum selesai, status revisi tetap `under_review` atau `draft`.

---

## 14. Aturan Versioning File CNC dengan Git

Jika proyek menyimpan file program CNC fisik (`.cnc`, `.nc`, `.min`, atau format mesin spesifik) dalam repository Git:

### Aturan Commit Bersama

- Jika file CNC fisik berubah bersamaan dengan pembuatan Revision Record → **commit keduanya dalam satu commit**.
- Jangan commit file CNC tanpa dokumentasi Revision Record.
- Jangan commit Revision Record tanpa file CNC jika file fisiknya dikelola di repo ini.

### Naming File CNC

Simpan file CNC dengan penamaan yang mencerminkan revisi:

```
[NomorProgram]_[RevisiAktif].[ekstensi]
```

Contoh:
```
2513_REV01.min
2513_REV02.min
```

Atau simpan dalam subfolder per program:
```
03_CNC Programs/2513/2513_REV00.min
03_CNC Programs/2513/2513_REV01.min
```

### Aturan Penting

- Jangan menimpa file revisi lama dengan file baru — simpan sebagai file terpisah.
- Jika hanya dokumentasi yang berubah (bukan file CNC fisik), commit hanya dokumentasinya.
- Jika file CNC disimpan di sistem eksternal (mesin controller, server produksi), catat path atau referensi lokasi di field `file_path` pada Program Master.

---

## 15. Aturan Tidak Menimpa Revisi Lama

Ini adalah aturan paling mendasar dalam skill ini.

### Yang Dilarang

- Mengedit atau mengubah konten Revision Record yang sudah ada.
- Menimpa field `revision` pada Revision Record yang sudah dibuat.
- Menghapus section Before/After dari Revision Record yang sudah terdokumentasi.
- Mengubah nomor revisi pada Program Master ke revisi yang lebih lama.

### Yang Wajib Dilakukan

- Setiap perubahan program → buat **Revision Record baru** dengan nomor revisi berikutnya.
- Revision Record lama tetap ada sebagai bagian dari riwayat — jangan arsipkan atau hapus.
- Jika ada kesalahan pada Revision Record yang sudah dibuat → tambahkan note koreksi di Section 1 (Ringkasan Revisi), jangan edit section lain.
- Perbarui `current_revision` di Program Master setiap kali ada revisi baru.

---

## 16. Aturan Mendokumentasikan Hasil Trial Setelah Revisi

Setelah trial dengan revisi baru dijalankan:

1. Buka Revision Record yang sesuai.
2. Isi Section 11 (Hasil Trial Setelah Perubahan) dengan:
   - Tanggal trial.
   - Nomor trial dalam Job Card.
   - Ringkasan hasil: dimensi yang diukur, surface finish, kondisi tool.
   - Referensi Inspection Record: `[[IR-RDXXXXXXXX-TXX]]`.
   - Kesimpulan awal: apakah perubahan menyelesaikan masalah?
3. Isi Section 12 (Keputusan):

```markdown
## 12. Keputusan

| Keputusan | Tanggal | Oleh |
| --- | --- | --- |
| [Accept / Reject / Retest / Conditional Accept] | YYYY-MM-DD | [Nama/Jabatan] |

Catatan: [penjelasan singkat]
```

4. Perbarui field YAML `trial_result_after_change` dan `status`.
5. Jika revisi diterima → perbarui `current_revision` di Program Master.
6. Jika revisi ditolak → buat Revision Record baru untuk perubahan berikutnya.
7. Commit semua perubahan bersamaan.

### Nilai `trial_result_after_change`

```
Pass
Fail
Conditional Pass
Retest Required
Pending Inspection
TBD
```

---

## 17. Format Respons Wajib Setelah Membuat atau Memperbarui Dokumentasi CNC

Setelah menyelesaikan tugas yang melibatkan skill ini, Claude **wajib** melaporkan:

```
### Hasil CNC Program Revision Control

**Tindakan yang dilakukan:**
- [Program Master yang dibuat / diperbarui]
- [Revision Record yang dibuat / diperbarui]

**Validasi revision record:**
- Nomor revisi: [REVxx — sesuai urutan / ada gap?]
- Alasan perubahan: [terdokumentasi / TBD]
- Tipe perubahan: [terisi / TBD]
- Before/after: [terdokumentasi dengan kode / deskriptif / belum ada]
- Risk level: [Low / Medium / High / TBD]
- Verifikasi diperlukan: [terisi / TBD]
- Hasil trial: [terisi / belum ada — menunggu trial]
- Status revisi: [draft / under_review / approved / rejected / superseded]

**Traceability:**
- Link ke Job Card: [ada / belum ada]
- Link ke Product Master: [ada / belum ada]
- Link ke Machine Master: [ada / belum ada]
- Link ke Inspection Record: [ada / belum ada / menunggu trial]
- Link ke Issue Record: [ada / belum ada / tidak relevan]
- Link ke Product Tooling Setup: [ada / belum ada / tidak relevan]
- Program Master diperbarui (current_revision): [Ya / Belum]

**Perhatian:**
- [Ada dimensi kritis terdampak? Risk level sudah di-set minimum Medium?]
- [Ada kode CNC yang belum terdokumentasi?]
- [Ada approval yang diklaim tanpa bukti?]

**Open Questions:**
- [Daftar pertanyaan yang ditambahkan]

**Git:**
- Files changed: [daftar file — termasuk file CNC fisik jika ada]
- Commit: [hash]
- Branch: [nama branch]
- Push: [berhasil / gagal — detail jika gagal]

**Langkah selanjutnya:**
- [Apakah perlu Inspection Record? → skill rnd-job-card-copilot]
- [Apakah ada tooling yang berubah? → skill product-tooling-setup]
- [Rekomendasi tindakan berikutnya]
```

Jangan klaim revisi `approved` kecuali field `approved_by` sudah terisi dengan nama atau jabatan yang jelas. Jangan klaim revisi menghasilkan `Pass` kecuali Inspection Record sudah ada dan terhubung.
