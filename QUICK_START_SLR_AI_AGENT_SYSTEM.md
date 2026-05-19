# 🚀 Quick Start — SLR AI Agent System V3.6

> Panduan ringkas untuk memulai SLR dari nol. Untuk penjelasan mendalam, baca `PANDUAN_PENGGUNAAN_SLR_AI_AGENT_SYSTEM.md`.

---

## 🧠 Cara Sistem Ini Bekerja (Gambaran Besar)

Anggap sistem ini seperti **tim asisten riset otomatis**. Anda cukup memberikan topik penelitian, lalu sistem akan membantu Anda dari awal hingga menghasilkan draft jurnal siap submit.

Alur kerjanya:

```
Target Jurnal → Protokol → Cari Paper → Pilah Paper → Ambil Data → Analisis → Tulis Jurnal
```

Setiap tahap dibantu oleh **Agent AI** di Copilot Chat (tanpa kode) atau **Script Python** (lebih cepat, jika sudah ada API key).

---

## 🛠️ Persiapan Awal (Lakukan Sekali Saja)

### 1. Install aplikasi yang dibutuhkan

| Aplikasi | Keterangan |
|---|---|
| VS Code | Editor utama. Wajib. |
| GitHub Copilot Chat | AI Assistant bawaan VS Code. Wajib. |
| Python 3.10+ | Mesin eksekusi script. Wajib. |
| Publish or Perish | Alat mencari artikel ilmiah. Gratis. |
| JASP (opsional) | Software statistik gratis untuk analisis lanjutan. |

### 2. Install library Python

Buka Terminal di VS Code, jalankan:

```bash
python3 -m pip install -r requirements.txt
```

### 3. Siapkan API Key (Opsional tapi Dianjurkan)

Dengan API key, pipeline berjalan otomatis penuh. Tanpa API key, Anda tetap bisa pakai Agent Copilot Chat secara manual.

Salin file `.env.example` menjadi `.env`, lalu isi:

```
LLM_API_KEY=api-key-anda
LLM_BASE_URL=url-provider-anda    # kosongkan jika pakai OpenAI
LLM_MODEL=nama-model              # contoh: gpt-4o-mini, llama3
```

Contoh provider yang bisa dipakai: OpenAI, OpenRouter, Groq, Ollama (lokal), Together AI.

---

## 📋 Isi Konfigurasi Awal

Buka file `config/config.yaml`, isi bagian ini minimal:

```yaml
project:
  name: "Nama SLR Anda"
  author: "Nama Anda"
  institution: "Institusi Anda"

analysis_mode: slr_only  # slr_only | bibliometric | meta_analysis | hybrid

topic: |
  Tulis topik penelitian Anda di sini.

research_questions:
  - "RQ1: (akan diisi bersama Protocol Designer)"
  - "RQ2: (akan diisi bersama Protocol Designer)"

year_start: 2020
year_end: 2025
```

> Belum perlu sempurna. Nanti Agent akan membantu menyempurnakannya.

---

## 🗺️ Tahapan Lengkap (Ikuti Urutan Ini)

---

### FASE 1 — Persiapan Penelitian

---

#### 📌 Tahap 0 — Strategic Journal Positioning (BARU)

**Tujuan:** Memetakan selera target jurnal (Q1/Q2) dan memastikan ide Anda punya nilai "jual" (Research Gap yang nyata) sebelum mulai membuat protokol.

**Cara:** Buka Copilot Chat di VS Code, ketik:

```
@journal-strategist Saya ingin menulis SLR tentang [topik].
Target jurnal saya adalah [Nama Jurnal].
Berikut adalah Aim & Scope mereka: [paste aim & scope].
Ini daftar 10 judul/abstrak paper terbaru mereka: [paste judul/abstrak].
Jika jurnal ini punya kebijakan AI, berikut URL/teks policynya: [paste di sini].
Bantu saya buatkan Journal Positioning Blueprint, Gap Matrix, dan cek apakah kebijakan AI jurnal ini kompatibel dengan workflow sistem kita.
```

**Hasil:** File `data/output/JOURNAL_BLUEPRINT.md` dan `data/output/GAP_MATRIX.md`. Gunakan [templates/journal_blueprint_template.md](/Users/user/Documents/SLR%20AI%20Agent%20System%20V3.6/templates/journal_blueprint_template.md) sebagai struktur baku jika perlu. Kedua file ini akan otomatis memandu agen-agen selanjutnya.

**Setelah selesai:** ringkas hasil kebijakan AI jurnal target ke blok `ai_policy` di `config/config.yaml`.

**Cek wajib di Tahap 0:** pastikan Anda tahu apakah jurnal target:
- menerima AI dengan disclosure,
- membatasi AI hanya untuk language/readability,
- meminta declaration khusus,
- meminta detail tool/version/prompt,
- melarang AI-generated figures.

---

#### 📌 Tahap 1 — Susun Protokol Penelitian

**Tujuan:** Menentukan arah riset — RQ, batasan, dan kriteria paper yang dicari.

**Keputusan wajib di tahap ini:** pilih `analysis_mode` sejak awal:
- `slr_only`
- `bibliometric`
- `meta_analysis`
- `hybrid`

Makna praktis:
- Semua mode tetap mengekstrak baseline metadata bibliografis seperti author, journal/source, year, DOI, country, keywords, citation count, dan references jika tersedia.
- `slr_only` tetap membawa metadata tersebut, tetapi tidak menjadikan bibliometric mapping sebagai kontribusi utama.
- `bibliometric` memperdalam metadata itu untuk performance analysis dan science mapping.
- `meta_analysis` menambahkan ekstraksi numerik untuk pooling.
- `hybrid` menggabungkan keduanya.

**Cara:** Buka Copilot Chat di VS Code, ketik:

```
@protocol-designer Saya ingin memulai SLR dengan topik:
"[tulis topik Anda di sini]".
Saya belum punya Research Questions dan belum punya kriteria inklusi/eksklusi.
Tolong BACA data/output/JOURNAL_BLUEPRINT.md terlebih dahulu, lalu
bantu saya menyusun protokol awal yang selaras dengan target jurnal tersebut.
Sekaligus bantu saya menetapkan analysis_mode yang paling tepat.
```

**Hasil:** File `data/output/protocol.md` berisi RQ, PICOS, kriteria inklusi/eksklusi, dan keputusan `analysis_mode`.

Jika mode yang dipilih adalah `bibliometric` atau `hybrid`, gunakan juga:
- `templates/protocol_bibliometric_template.md`
- `templates/bibliometric_project_checklist.md`
- `templates/bibliometric_e2e_test_scenario.md`

**Setelah selesai:** Salin bagian penting (`analysis_mode`, `topic`, `research_questions`, `inclusion_criteria`, `year_start`) ke `config/config.yaml`.

> Catatan reset: `python src/reset_project.py` menyediakan [1] Reset Biasa dan [2] Reset Full.
> Reset Biasa menghapus artefak + data/input/; Reset Full menghapus juga .venv/ + data/input/，回到 clone 后状态。

---

#### 📌 Tahap 2 — Buat Kata Kunci Pencarian

**Tujuan:** Membuat string pencarian untuk dipakai di Publish or Perish / Scopus.

**Cara:** Di Copilot Chat, ketik:

```
@search-strategy Baca data/output/protocol.md saya.
Buatkan search_strings.md berisi kata kunci siap pakai untuk
Publish or Perish, Scopus, dan Web of Science.
```

**Hasil:** File `data/output/search_strings.md`.

---

#### 📌 Tahap 3 — Cari Artikel & Import Data

**Tujuan:** Mengumpulkan artikel dari internet ke dalam sistem.

**Cara Manual:**
1. Buka aplikasi **Publish or Perish**
2. Gunakan kata kunci dari `search_strings.md`
3. Export hasil ke CSV
4. Simpan file CSV ke folder `data/input/`

**Lalu jalankan script:**

```bash
python src/pipeline/03_import_deduplicate.py
```

**Hasil:** `data/output/screened/clean_corpus.json` — daftar artikel bersih tanpa duplikat.

---

### FASE 2 — Seleksi & Pengambilan Data

---

#### 📌 Tahap 4 — Screening Paper

**Tujuan:** Memilah mana artikel yang relevan (`INCLUDE`) dan mana yang tidak (`EXCLUDE`).

**Cara dengan script:**

```bash
python src/pipeline/04_screen.py
```

**Cara manual (tanpa API key), ketik di Copilot Chat:**

```
@screener Bantu saya melakukan title and abstract screening.
Baca config/config.yaml dan data/output/protocol.md sebagai pedoman.
Keputusan hanya boleh: INCLUDE atau EXCLUDE. Tidak ada status MAYBE.
```

**Hasil:** `data/output/screened/screened_results.json`

---

#### 📌 Tahap 5 — Ambil Data Penting dari Abstrak (Smart Extraction)

**Tujuan:** AI membaca abstrak setiap paper dan mengambil data penting (metode, temuan, statistik, dll).

> ⚙️ **Sebelum menjalankan ini, ada 2 langkah persiapan singkat:**
>
> **Langkah 5a — Buat Draf Template Kolom (Otomatis)**
>
> ```
> @data-extractor Baca data/output/protocol.md dan config/config.yaml saya.
> Berdasarkan PICOS dan topik penelitian di sana, buatkan draf file
> templates/extraction_template.json. Isi field "domain_variables" dengan
> daftar variabel eksperimen yang paling relevan untuk diekstrak dari paper.
> ```
>
> **Langkah 5b — Cek & Sesuaikan Template**
> Buka `templates/extraction_template.json`. Periksa daftar variabel yang dibuat Agent.
> Tambah atau hapus jika ada yang tidak sesuai. Simpan file.

**Setelah template siap, jalankan:**

```bash
python src/pipeline/05_extract_abstract.py
```

**Fitur otomatis yang bekerja di balik layar:**
- Jika paper hanya mencantumkan **Standard Error (SE)**, sistem otomatis mengubahnya menjadi SD.
- Jika data abstrak kosong, sistem akan mencoba mencari di file PDF lengkap sebagai cadangan.
- Paper yang benar-benar tidak ada datanya dicatat di `missing_data_report.md`.

**Hasil:**
- `data/output/extracted/extraction_table.json`
- `data/output/extracted/extraction_table.csv`

---

#### 📌 Tahap 6 — Lengkapi Data Jurnal dari Internet

**Tujuan:** Mengisi data yang kurang (nama publisher, negara, jumlah sitasi) secara otomatis dari CrossRef.

```bash
python src/pipeline/06_enrich_metadata.py
```

**Hasil:** `extraction_table.json` yang lebih lengkap.

---

### FASE 3 — Pengumpulan Full-Text

---

#### 📌 Tahap 7 — Kumpulkan File PDF

**Tujuan:** Mengunduh file PDF full-text dari setiap artikel yang lolos screening.

```bash
python src/pipeline/07_generate_download.py
```

**Hasil:** File HTML berisi tautan download. Unduh PDF secara manual, lalu simpan ke folder `data/fulltext_pdfs/`.

---

#### 📌 Tahap 8 — Analisis Full-Text (Gemini Native PDF)

**Tujuan:** AI menganalisis isi lengkap artikel (PDF asli) untuk mengambil data kuantitatif (tabel) dan kualitatif secara akurat.

```bash
python src/pipeline/08_fulltext_processor.py
```

**Hasil:**
- `extraction_table.json` yang sudah terisi penuh dari full-text.
- `data/output/extracted/summaries_markdown/` (Ringkasan naratif & tabel per paper).

---

#### 📌 Tahap 10 — Sinkronisasi ke Wiki (BARU)

**Tujuan:** Memindahkan hasil ekstraksi ke dalam Wiki Markdown untuk memudahkan penelusuran manual dan audit.

```bash
python src/pipeline/10_sync_to_wiki.py
```

**Hasil:** Folder `wiki/papers/` terisi dengan file markdown per paper.

---

#### 📌 Tahap 11 — Audit Kualitas Metodologi (CASP)

**Tujuan:** Menilai seberapa baik kualitas riset masing-masing paper berdasarkan data metodologi yang sudah diekstrak.

**Catatan mode:** tahap ini wajib untuk `slr_only`, `meta_analysis`, dan `hybrid`, tetapi boleh di-skip untuk `bibliometric`.

```bash
python src/pipeline/11_quality_assess.py
```

**Cara manual, ketik di Copilot Chat:**

```
@quality-assessor Baca extraction_table.json dan audit kualitas metodologi
setiap paper menggunakan CASP checklist.
Hasilkan quality_assessed.json dengan quality_score (PASS/FAIL).
```

**Hasil:** `data/output/screened/quality_assessed.json`

---

#### 📌 Tahap 11b — Health Check & Exclusion Reconciliation (WAJIB sebelum Sintesis)

**Tujuan:** Memastikan paper yang lanjut ke sintesis benar-benar bersih: tidak ada paper `INCLUDE` yang hilang dari extraction, tidak ada paper tanpa full-text, dan angka PRISMA sinkron.

Jalankan:

```bash
python src/utils/health_check.py --fix-prisma
```

**Baca hasil:**
- `PASS` → aman lanjut.
- `WARN` → boleh lanjut jika warning hanya field rekomendasi/schema dan tidak mengubah corpus.
- `FAIL` → jangan lanjut dulu. Buka `data/output/health_report.md`.

Jika health report menunjukkan paper bermasalah dan paper tersebut memang harus dikeluarkan, jalankan:

```bash
python src/utils/exclude_problem_papers.py
python src/utils/health_check.py --fix-prisma
python src/pipeline/16_generate_viewer.py
```

**Output penting:**
- `data/output/paper_manifest.json` — status lineage setiap paper.
- `data/output/health_report.md` — laporan masalah pipeline.
- `data/output/exclusion_reconciliation_log.json` — audit trail paper yang dikeluarkan.

---

#### 📌 Tahap 12 — Hitung Statistik Meta-Analisis (JASP Ready)

**Tujuan:** Menghitung angka statistik ilmiah secara matematis murni (bukan ditebak AI). Menghasilkan grafik dan file siap pakai untuk software JASP.

**Catatan mode:** jalankan tahap ini hanya jika `analysis_mode` adalah `meta_analysis` atau `hybrid`.

```bash
python src/pipeline/12_meta_analysis_engine.py
```

**Cara manual, ketik di Copilot Chat:**

```
@meta-analyst Jalankan perhitungan meta-analisis.
Baca extraction_table.json, hitung Hedges' g dan I² per parameter,
lalu buat forest plot dan file jasp_meta_analysis_ready.csv.
```

**Hasil:**
- `meta_stats_results.json` — angka statistik (Hedges' g, I², p-value)
- `forest_plot_*.png` — grafik Forest Plot per parameter
- `jasp_meta_analysis_ready.csv` — 🎯 **File siap diimpor ke aplikasi JASP**

Catatan:
- Forest plot mentah dibuat di Tahap 12.
- Paket figure final yang rapi untuk dipakai ulang akan dikonsolidasikan lagi pada Tahap 16 ke folder `data/output/figure/meta_analysis/`.

> 💡 **Cara pakai di JASP:** Buka JASP → Open CSV ini → Pilih modul Meta-Analysis → Drag kolom `Effect_Size_g` dan `Standard_Error` → Selesai!

---

#### 📌 Tahap 13 — Gabungkan Semua Temuan (Sintesis)

**Tujuan:** Merangkum seluruh hasil dari ratusan paper menjadi satu narasi yang koheren per Research Question.

**Catatan mode:** tahap ini sekarang juga menghasilkan `bibliometric_summary.json` dan `bibliometric_report.md`. Pada mode `bibliometric` atau `hybrid`, kedua artefak ini menjadi keluaran inti.

```bash
python src/pipeline/13_synthesize.py
```

**Cara manual, ketik di Copilot Chat:**

```
@synthesizer Baca extraction_table.json, quality_assessed.json,
meta_stats_results.json, dan GAP_MATRIX.md. Susun narrative synthesis
yang sinkron dengan hasil JASP (Stage 12), identifikasi tema utama,
dan temukan research gap yang selaras dengan Stage 0.
```

**Hasil:** `data/output/synthesized/synthesis_narrative.md`

---

#### 📌 Tahap 14 — Tulis Draft Jurnal

**Tujuan:** Membuat draft lengkap manuscript jurnal (Abstract, Introduction, Methods, Results, Discussion, Conclusion, References) secara otomatis.

```bash
python src/pipeline/14_write_manuscript.py
```

**Cara manual, ketik di Copilot Chat:**

```
@slr-writer Baca synthesis_narrative.md, meta_stats_results.json,
dan research_gaps.md. Tulis draft manuscript lengkap untuk jurnal
Scopus Q1/Q2 dalam format akademis.
```

**Hasil:**
- `data/output/draft_manuscript_en.md` — draft English dalam Markdown
- `data/output/draft_manuscript_id.md` — draft Indonesian dalam Markdown
- `data/output/manuscript_en.docx` — draft English dalam format Word
- `data/output/manuscript_id.docx` — draft Indonesian dalam format Word
- `data/output/REWRITING_GUIDE.md` — ⚠️ **Wajib dibaca sebelum submit!**

---

#### 📌 Tahap 15 — Audit Kualitas Draft (Reviewer 2 Virtual)

**Tujuan:** Mengecek apakah draft Anda sudah memenuhi standar jurnal Q1 — sebelum dikirim ke reviewer sungguhan.

```bash
python src/pipeline/15_audit_manuscript.py
```

**Hasil:**
- `data/output/AUDIT_REPORT_en.md` — laporan audit English
- `data/output/AUDIT_REPORT_id.md` — laporan audit Indonesian
- `data/output/audit_report.json` — laporan audit machine-readable

**Tabel Arti Skor Audit:**

| Skor | Status | Artinya |
|---|---|---|
| 90–100 | ✅ LULUS | Siap untuk ditulis ulang & submit |
| 70–89 | ⚠️ PERLU REVISI KECIL | Ada beberapa poin yang perlu diperbaiki |
| 50–69 | ❌ REVISI BESAR | Konten perlu distrukturisasi ulang |
| < 50 | ❌ GAGAL | Perlu pengerjaan ulang dari awal |

---

#### 📌 Tahap 16 — Dashboard Hasil Interaktif (Viewer)

**Tujuan:** Melihat seluruh temuan, tabel ekstraksi, dan draf jurnal Anda dalam dashboard web yang profesional.

```bash
python src/pipeline/16_generate_viewer.py
```

**Hasil:** Folder `data/output/viewer/` berisi file HTML yang bisa dibuka di browser apa saja.

Viewer sekarang juga memiliki:
- `data/output/viewer/health.html` — status health check, warnings, dan paper yang perlu perhatian.

Tahap ini sekarang juga membuat paket figure final berbasis data di `data/output/figure/`.

Isi utamanya:
- `data/output/figure/prisma/prisma_2020_flow.png` — PRISMA 2020 raster 300 dpi
- `data/output/figure/prisma/prisma_2020_flow.svg` — PRISMA 2020 editable vector
- `data/output/figure/prisma/prisma_2020_flow.json` — source-of-truth PRISMA figure
- `data/output/figure/meta_analysis/` — forest plot final + JSON sidecar per outcome
- `data/output/figure/descriptive/` — publication trend, country distribution, top journals
- `data/output/figure/manifest.json` — daftar figure yang didukung, figure yang dibuat, stage owner, dan path output

Aturan penting:
- Figure dibuat dari data pipeline yang tersedia, bukan dari imajinasi AI bebas.
- PRISMA final dibangun di Tahap 16 agar memakai angka yang sudah stabil.
- Chart deskriptif di Tahap 16 dibangun dari `data/output/synthesized/stats_summary.json`.

---

### FASE 5 — Finalisasi & Submit

---

#### 📌 Tahap 15 — Tulis Ulang & Submit

> ⛔ **JANGAN submit draft AI langsung ke jurnal.** Setiap paragraf sudah ditandai `[AUTHOR MUST REWRITE]`.

**Langkah wajib:**

1. Baca `data/output/REWRITING_GUIDE.md` terlebih dahulu
2. Tulis ulang setiap paragraf dengan kata-kata Anda sendiri
3. Masukkan data spesifik: angka, nama author, tahun
4. Buka setiap DOI referensi — pastikan klaim Anda akurat
5. Cek Turnitin → target: **0% AI**, **< 20% similarity**
6. Submit ke jurnal

---

## ✅ Checklist Cepat

**Sebelum mulai pipeline:**
- [ ] VS Code + Copilot Chat sudah aktif
- [ ] `pip install -r requirements.txt` sudah dijalankan
- [ ] `config/config.yaml` sudah diisi topik minimal
- [ ] `analysis_mode` sudah dipilih sejak awal
- [ ] `JOURNAL_BLUEPRINT.md` sudah dibuat bersama Journal Strategist
- [ ] Kebijakan AI jurnal target sudah dicek dan status kompatibilitasnya sudah jelas
- [ ] `protocol.md` sudah dibuat bersama Protocol Designer
- [ ] `search_strings.md` sudah dibuat bersama Search Strategy
- [ ] CSV dari Publish or Perish sudah ada di `data/input/`
- [ ] `templates/extraction_template.json` sudah diperiksa & disetujui

**Sebelum sintesis/manuscript:**
- [ ] `python src/utils/health_check.py --fix-prisma` sudah dijalankan
- [ ] `data/output/health_report.md` sudah dibaca
- [ ] Jika health status `FAIL`, paper bermasalah sudah direkonsiliasi dengan `python src/utils/exclude_problem_papers.py`
- [ ] Viewer sudah diregenerate dengan `python src/pipeline/16_generate_viewer.py`
- [ ] Folder `data/output/figure/` sudah dicek bila Anda butuh figure siap pakai/editable

**Setelah draft jadi:**
- [ ] `REWRITING_GUIDE.md` sudah dibaca
- [ ] Setiap paragraf sudah ditulis ulang dengan kata sendiri
- [ ] Semua `[AUTHOR MUST REWRITE]` sudah dihapus
- [ ] Semua referensi sudah diverifikasi (buka DOI satu per satu)
- [ ] Turnitin AI: **0%** | Similarity: **< 20%**

---

## 🔄 Reset untuk Mulai SLR Baru

Jika ingin memulai project SLR yang baru:

```bash
python src/reset_project.py
```

Script menampilkan dua pilihan:

**[1] Reset Biasa** — hapus artefak project tanpa uninstall Python environment.
- Menghapus: `data/output/`, `data/fulltext_pdfs/`, `data/fulltext_markdown/`, `wiki/`, strategy files
- Menghapus juga: semua file di `data/input/`
- Mempertahankan: `.venv/`, source code

**[2] Reset Full** — kembali ke kondisi awal setelah clone/unzip. Menghapus SEMUA termasuk `.venv/` dan `data/input/`. Anda perlu install ulang (`python setup.sh`) dan masukkan ulang file input.

Masing-masing meminta konfirmasi dengan mengetik **"YA"** sebelum menghapus.

Yang di-reset ulang: `config/config.yaml` dibuat ulang dari `config/config.default.yaml`.
Folder `data/output/figure/` juga ikut dibersihkan saat reset, lalu akan dibuat ulang otomatis ketika Tahap 16 dijalankan lagi.
Pada reset biasa, `data/input/` tetap dipertahankan. Penghapusan `data/input/` hanya terjadi pada reset full.

---

> 💬 **Butuh bantuan?** Panggil Agent yang sesuai di Copilot Chat, atau baca penjelasan lengkap di `PANDUAN_PENGGUNAAN_SLR_AI_AGENT_SYSTEM.md`.
