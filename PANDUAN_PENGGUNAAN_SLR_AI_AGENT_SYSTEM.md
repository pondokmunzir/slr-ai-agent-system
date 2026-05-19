# Panduan Lengkap SLR AI Agent System V3.6 di VS Code

Versi singkat untuk pemula: `QUICK_START_SLR_AI_AGENT_SYSTEM.md`

Panduan ini untuk pengguna awam yang ingin memakai project ini sebagai template SLR di VS Code. Dibagi 4 bagian:

1. Penjelasan sistem
2. Konfigurasi awal
3. Tahapan menjalankan SLR
4. Setelah draft selesai (Turnitin & submission)

Asumsi:
- Anda baru punya topik penelitian
- Belum punya RQ, rentang tahun, search string, atau CSV

## Bagian A. Penjelasan Panduan Sistem

## 1. Sistem Ini Dipakai untuk Apa?

Project ini membantu Anda menjalankan alur SLR secara bertahap dari awal sampai draft manuscript.

Urutan kerja sistem:

1. **Strategic Journal Positioning**: Menentukan target jurnal & Research Gap.
2. **Menyusun Protokol**: Menentukan topik, RQ, batasan (PICOS), dan `analysis_mode`.
3. **Search Strategy**: Membuat kata kunci pencarian.
4. **Import Data**: Memasukkan hasil pencarian dari PoP.
5. **Screening**: Memilah paper relevan vs sampah.
6. **Smart Data Extraction**: Mengambil data penting dari abstrak.
7. **Enrich Metadata**: Mengisi data jurnal via CrossRef.
8. **Tracking Download**: Mengumpulkan file PDF full-text.
9. **Gemini Native Full-Text Engine**: Analisis PDF untuk ekstraksi & Smart MD.
10. **Wiki Sync**: Menyusun perpustakaan pengetahuan (knowledge base).
11. **Quality Assessment (CASP Audit)**: Audit kualitas riset jika mode analisis memerlukannya.
12. **Hybrid Meta-Analysis**: Hitung angka statistik & Forest Plot jika mode analisis memerlukannya.
13. **Sintesis**: Narrative synthesis, theme identification, dan bibliometric outputs.
14. **Writing**: Menulis draft jurnal secara utuh.
15. **Quality Audit**: Mengecek draft jurnal ala Reviewer 2.
16. **Interactive Viewer**: Dashboard visualisasi hasil.
17. **Health Check & Reliability Backbone**: Mengecek konsistensi paper, PRISMA, dan file antar-stage.

Output akhir utama ada di folder `data/output/`, terutama:
- `data/output/protocol.md`
- `data/output/search_strings.md`
- `data/output/screened/`
- `data/output/extracted/`
- `data/output/synthesized/`
- `data/output/paper_manifest.json`
- `data/output/health_report.md`
- `data/output/manuscript_en.docx`
- `data/output/manuscript_id.docx`

## 2. Pengguna Perlu Mengetahui Apa?

Sistem ini bukan alat yang langsung menghasilkan jurnal hanya dari satu klik. Sistem ini adalah workflow bertahap.

Artinya:
- Anda tetap harus menentukan topik penelitian
- Anda tetap harus memeriksa hasil agent dan output file
- Anda tetap harus memastikan konfigurasi sesuai topik
- Anda bisa dibantu agent Copilot Chat pada tiap tahap

Untuk pengguna pemula, cara paling aman adalah:
- mulai dari topik penelitian
- minta agent merumuskan strategi target jurnal dulu
- lalu minta agent membantu menyusun protokol
- baru lanjut ke search strategy
- baru lanjut ke pengumpulan paper
- setelah itu jalankan pipeline Python tahap demi tahap

## 3. Folder yang Perlu Anda Kenal

Folder penting dalam project ini:

- `config/config.yaml` → konfigurasi utama project
- `data/input/` → tempat file CSV hasil export Publish or Perish
- `data/output/` → semua hasil tiap tahap pipeline
- `data/fulltext_pdfs/` → tempat file PDF full-text
- `templates/` → template ekstraksi data
- `src/pipeline/` → script pipeline tahap 3 sampai 14 (termasuk audit & viewer)
- `.github/agents/` → agent yang membantu tiap tahap

## 4. Aplikasi yang Harus Disiapkan

Wajib:
- VS Code + GitHub Copilot Chat
- Python 3.10+
- Publish or Perish (untuk cari artikel)

Opsional:
- API key dari LLM provider (OpenAI, OpenRouter, Groq, Ollama, dll) — jika ingin pipeline Python berjalan otomatis

Tanpa API key, Anda tetap bisa memakai sistem ini. Gunakan agent Copilot Chat untuk tahap yang butuh AI (screening, quality assessment, extraction, synthesis, writing).

### Setup LLM Provider (opsional)

Sistem ini **bukan hanya untuk OpenAI**. Anda bisa pakai provider LLM mana saja yang kompatibel dengan OpenAI SDK.

Cara setup:

1. Salin file `.env.example` menjadi `.env`
2. Isi 3 variabel:
   ```
   LLM_API_KEY=api-key-dari-provider-anda
   LLM_BASE_URL=url-api-provider-anda
   LLM_MODEL=nama-model
   ```
3. Selesai — semua script akan otomatis membaca konfigurasi ini

Contoh konfigurasi per provider:

| Provider | LLM_BASE_URL | LLM_MODEL | LLM_API_KEY |
|---|---|---|---|
| OpenAI | _(kosongkan)_ | gpt-4o-mini | sk-xxx |
| OpenRouter | https://openrouter.ai/api/v1 | openai/gpt-4o-mini | sk-or-v1-xxx |
| Groq | https://api.groq.com/openai/v1 | llama-3.1-70b-versatile | gsk_xxx |
| Ollama (lokal) | http://localhost:11434/v1 | llama3 | ollama |
| Together | https://api.together.xyz/v1 | meta-llama/Llama-3-70b-chat-hf | xxx |
| Kimi | https://api.moonshot.cn/v1 | moonshot-v1-8k | sk-xxx |

Anda juga bisa set konfigurasi di `config/config.yaml` bagian `llm:` sebagai alternatif dari `.env`.

## Bagian B. Konfigurasi Awal yang Harus Disiapkan

## 5. Langkah Awal di VS Code

### Step 1: Buka folder project

Di VS Code:

1. Klik `File` → `Open Folder`
2. Pilih folder project ini
3. Pastikan file `config/config.yaml` terlihat di Explorer

### Step 2: Buka terminal di VS Code

Di VS Code:

1. Klik `Terminal` → `New Terminal`
2. Pastikan posisi terminal ada di root project

Contoh path yang benar:

```bash
/Users/user/Documents/SLR AI Agent System V3.6
```

### Step 3: Install dependency Python

Jalankan:

```bash
python3 -m pip install -r requirements.txt
```

## 6. File Konfigurasi yang Wajib Diisi Sejak Awal

File utama yang harus Anda siapkan adalah:

```text
config/config.yaml
```

Catatan penting:
- `config/config.yaml` adalah file konfigurasi aktif untuk project yang sedang dikerjakan.
- `config/config.default.yaml` adalah template default yang dipakai saat reset project atau saat memulai project baru.
- Saat Anda menjalankan `python src/reset_project.py`, file `config/config.yaml` akan dibuat ulang dari `config/config.default.yaml`.
- Jika Anda ingin mengubah default untuk semua project berikutnya, ubah `config/config.default.yaml`.

Walaupun Anda belum punya RQ, rentang tahun, atau kriteria detail, file ini tetap harus mulai diisi dengan informasi dasar.

## 7. Yang Perlu Diisi Dulu di `config/config.yaml`

Jika Anda baru punya topik, fokus isi bagian berikut terlebih dahulu.

### A. Identitas project

Isi minimal:

```yaml
project:
  name:
  author:
  institution:
```

### B. Target jurnal

Isi minimal:

```yaml
target_journal:
  name:
  quartile:
```

Jika belum tahu jurnal spesifiknya, Anda boleh isi sementara seperti ini:

```yaml
target_journal:
  name: "Target journal not decided yet"
  quartile: "Q1"
```

### C. Topik penelitian

Isi bagian ini dulu dengan jelas:

```yaml
topic: |
  [Tulis topik penelitian Anda di sini]
```

Contoh:

```yaml
topic: |
  Application of artificial intelligence in personalized learning systems in higher education.
```

### C.1 Analysis mode

Isi juga keputusan metodologis utama ini sejak awal:

```yaml
analysis_mode: slr_only
```

Pilihan yang tersedia:
- `slr_only`
- `bibliometric`
- `meta_analysis`
- `hybrid`

Makna singkat:
- Semua mode tetap membawa baseline metadata bibliografis seperti author, year, journal/source, publisher, DOI, country/affiliation context, keywords, citation count, dan references jika tersedia.
- `slr_only`: fokus pada narrative synthesis, thematic analysis, dan gap analysis. Metadata bibliografis dasar tetap ada, tetapi bibliometric mapping bukan kontribusi utama.
- `bibliometric`: fokus pada publication trends, citation patterns, source mapping, dan knowledge structure.
- `meta_analysis`: fokus pada pooling kuantitatif dan forest plot, sambil tetap mempertahankan baseline metadata corpus.
- `hybrid`: menggabungkan bibliometric, meta-analysis, dan narrative synthesis.

Catatan reset:
- `python src/reset_project.py` menyediakan [1] Reset Biasa dan [2] Reset Full.
- Reset Biasa menghapus artefak + data/input/；Reset Full menghapus juga .venv/ + data/input/，回到 clone 后状态。
- Template default sudah membawa scaffold bibliometric dasar — tinggal pilih `bibliometric` atau `hybrid` bila science mapping ingin ikut dibuat.
- Untuk project bibliometric baru, gunakan juga:
  - `templates/protocol_bibliometric_template.md`
  - `templates/bibliometric_project_checklist.md`
  - `templates/bibliometric_e2e_test_scenario.md`
  - `python3 src/utils/check_bibliometric_readiness.py`

### D. Research Questions

Jika belum punya RQ final, Anda boleh isi sementara dengan placeholder.

Contoh aman:

```yaml
research_questions:
  - "RQ1: Placeholder - to be refined with Protocol Designer"
  - "RQ2: Placeholder - to be refined with Protocol Designer"
```

### E. Rentang tahun

Jika belum yakin, isi sementara dengan rentang awal yang masuk akal.

Contoh:

```yaml
year_start: 2020
year_end: 2025
```

Nanti bisa diperbaiki setelah diskusi dengan agent.

### F. Kriteria inklusi dan eksklusi

Anda boleh memakai default yang sudah ada dulu. Tidak perlu langsung sempurna di awal.

Yang penting, setelah protokol final selesai, Anda harus menyelaraskan kembali:
- topik
- research questions
- year range
- inclusion criteria
- exclusion criteria

### G. Quality assessment dan blacklist publisher

Bagian ini biasanya bisa dibiarkan default di awal, kecuali Anda punya kebutuhan khusus.

Default sistem:
- quality assessment tool = CASP
- minimum score = 3/5
- blacklist publisher = MDPI, Frontiers, Hindawi

## 8. Input yang Harus Disiapkan Pengguna

Saat project baru dimulai, Anda belum perlu langsung menyiapkan semua input akhir. Input dibutuhkan secara bertahap.

### Input awal yang perlu ada

- topik penelitian
- file `config/config.yaml` yang sudah diisi dasar

### Input yang akan dibutuhkan setelah itu

#### 1. Protokol SLR

File:

```text
data/output/protocol.md
```

#### 2. Search strings

File:

```text
data/output/search_strings.md
```

#### 3. CSV hasil Publish or Perish

Simpan ke:

```text
data/input/
```

Contoh nama file:
- `raw_GS_2026-04-28.csv`
- `raw_SCOPUS_2026-04-28.csv`
- `raw_WOS_2026-04-28.csv`

#### 4. PDF full-text

Simpan ke:

```text
data/fulltext_pdfs/
```

Contoh nama file:
- `P0001_Author_2023.pdf`
- `P0002_Smith_2022.pdf`

## Bagian C. Tahapan Memulai dan Menjalankan SLR

## 9. Jika User Baru Punya Topik Saja, Mulainya Dari Mana?

Jika Anda hanya punya topik penelitian, jangan mulai dari script Python dulu.

Urutan yang benar untuk pemula adalah:

1. Isi dasar `config/config.yaml`
2. Panggil agent `Journal Strategist` untuk merumuskan posisi riset
3. Panggil agent `Protocol Designer`
4. Minta agent membantu merumuskan RQ, scope, dan kriteria
5. Finalkan `data/output/protocol.md`
6. Sinkronkan hasilnya ke `config/config.yaml`
7. Panggil agent `Search Strategy`
7. Baru lakukan pencarian artikel dan export CSV
8. Setelah CSV tersedia, jalankan pipeline Python

## 10. Contoh Perintah Pertama Kali ke Agent

### Agent pertama yang dipanggil: `Journal Strategist`

Tujuan agent ini:
- memetakan arena kompetisi jurnal target
- menyusun Research Gap Matrix
- mengecek apakah jurnal target punya kebijakan khusus terkait Generative AI / AI-assisted technologies
- menghasilkan isi `data/output/JOURNAL_BLUEPRINT.md` dan `data/output/GAP_MATRIX.md`

Jika perlu, gunakan [templates/journal_blueprint_template.md](/Users/user/Documents/SLR%20AI%20Agent%20System%20V3.6/templates/journal_blueprint_template.md) sebagai struktur baku untuk `JOURNAL_BLUEPRINT.md`.

### Contoh prompt pertama untuk Journal Strategist

```text
Gunakan Journal Strategist. Saya ingin menulis SLR tentang [topik Anda]. Target jurnal saya adalah [Nama Jurnal]. Berikut adalah Aim & Scope mereka: [paste aim & scope]. Ini daftar 10 judul/abstrak paper terbaru mereka: [paste judul/abstrak]. Jika jurnal ini punya kebijakan AI, berikut URL atau teks kebijakannya: [paste URL/teks policy]. Bantu saya buatkan Journal Positioning Blueprint, Gap Matrix, dan analisis apakah kebijakan AI jurnal ini kompatibel dengan workflow sistem kita.
```

Pada tahap ini, Anda sebaiknya sudah menjawab pertanyaan berikut:
- Apakah jurnal target mengizinkan AI hanya untuk language/readability, atau juga menerima disclosure untuk workflow review/metode?
- Apakah jurnal meminta declaration khusus sebelum References?
- Apakah jurnal meminta detail model, versi, atau prompt?
- Apakah jurnal melarang AI-generated images/figures?

Jika jawaban atas pertanyaan-pertanyaan itu belum jelas, jangan anggap target jurnal sudah aman untuk workflow sistem ini.

Setelah Stage 0 selesai, salin ringkasan hasil cek ini ke blok `ai_policy` di `config/config.yaml`.

### Agent kedua yang dipanggil: `Protocol Designer`

Tujuan agent ini:
- membantu merumuskan RQ
- membantu menentukan rentang tahun
- membantu menentukan inclusion dan exclusion criteria
- menghasilkan isi `data/output/protocol.md`

### Contoh prompt pertama jika user baru punya topik

Contoh 1:

```text
Gunakan Protocol Designer. Saya ingin memulai SLR dengan topik: "Application of artificial intelligence in personalized learning systems in higher education". Saya belum punya Research Questions, belum punya rentang tahun, dan belum punya inclusion/exclusion criteria. Bantu saya menyusun protokol awal yang layak untuk SLR jurnal Scopus Q1/Q2.
```

Contoh 2:

```text
Protocol Designer, bantu saya menyusun protocol.md berdasarkan topik penelitian saya: "Use of generative AI in academic writing support for university students". Saya masih di tahap awal dan hanya punya topik. Tolong bantu tentukan RQ awal, rentang tahun yang masuk akal, PICO/SPIDER, serta kriteria inklusi dan eksklusi.
```

Contoh 3:

```text
Saya baru punya topik SLR: "Artificial intelligence for adaptive assessment in online learning". Gunakan Protocol Designer dan arahkan saya langkah demi langkah dari topik ini sampai menjadi protokol penelitian yang siap dipakai.
```

## 11. Setelah Protokol Selesai, Lanjut ke Mana?

Setelah `data/output/protocol.md` selesai dibuat:

1. Baca hasilnya
2. Perbaiki jika ada bagian yang belum sesuai
3. Samakan isi pentingnya ke `config/config.yaml`

Bagian yang wajib disinkronkan ke `config/config.yaml`:
- `topic`
- `research_questions`
- `year_start` dan `year_end`
- `inclusion_criteria`
- `exclusion_criteria`
- `quality_assessment.minimum_score` jika berubah

Setelah itu, panggil agent `Search Strategy`.

### Contoh prompt untuk agent `Search Strategy`

```text
Gunakan Search Strategy. Baca data/output/protocol.md dan bantu saya membuat search_strings.md untuk topik ini. Saya ingin string pencarian yang siap dipakai di Publish or Perish, Scopus, dan Web of Science.
```

## 12. Tahapan Membuat SLR Setelah Search Strategy Siap

Setelah `data/output/search_strings.md` selesai:

### Tahap 1: Lakukan pencarian artikel

1. Buka Publish or Perish
2. Gunakan search string yang sudah dibuat
3. Export hasil pencarian ke CSV
4. Simpan CSV ke `data/input/`

### Tahap 2: Import dan deduplikasi

Jalankan:

```bash
python src/pipeline/03_import_deduplicate.py
```

Output utama:
- `data/output/screened/clean_corpus.json`
- `data/output/screened/duplicates.json`
- `data/output/screened/prisma_counts.json`

### Tahap 3: Screening judul dan abstrak

Jalankan:

```bash
python src/pipeline/04_screen.py
```

Aturan penting sistem:
- tidak ada status MAYBE
- keputusan hanya `INCLUDE` atau `EXCLUDE`
- publisher blacklist otomatis di-exclude

Jika belum ada API key, gunakan agent `Screener`.

Contoh prompt:

```text
Gunakan Screener. Bantu saya melakukan title and abstract screening berdasarkan config/config.yaml dan protocol.md. Keputusan hanya boleh INCLUDE atau EXCLUDE.
```

### Tahap 5: Smart Data Extraction (Dinamis & Universal)

Jalankan:

```bash
python src/pipeline/05_extract_abstract.py
```

Ini adalah tahap "pencurian data" dari abstrak paper. Sistem ini menggunakan **Universal Schema**, artinya ia bisa beradaptasi dengan bidang ilmu apapun (Ayam, Psikologi, IT, dll).

**Sistem ini bekerja dalam 3 Fase:**
1. **Fase 1 (Otomatis)**: Anda minta Agent (`@data-extractor`) membaca `protocol.md` untuk membuat draf kolom apa saja yang mau diambil (misal: "Strain Ayam", "Dosis Obat").
2. **Fase 2 (Intervensi)**: Anda cek file `templates/extraction_template.json`. Jika ada kolom yang kurang, Anda tinggal tambah sendiri di situ.
3. **Fase 3 (Ekstraksi)**: Jalankan script di atas. AI akan patuh mengekstrak data HANYA sesuai kolom yang Anda setujui di Fase 2.

**Fitur Canggih:**
- **Smart Imputation**: AI otomatis mengubah angka SE (Standard Error) menjadi SD (Standard Deviation) jika paper hanya mencantumkan SE.
- **Full-Text Fallback**: Jika data di abstrak tidak ada, AI akan otomatis mencoba mencarinya di file PDF lengkap (jika sudah ada).
- **Missing Data Report**: Jika data benar-benar tidak ada, sistem akan membuat laporan `missing_data_report.md` sehingga Anda tahu paper mana yang datanya bolong.

Output:
- `data/output/extracted/extraction_table.json`
- `data/output/extracted/extraction_table.csv`
- `data/output/extracted/missing_data_report.md` (Jika ada data hilang)

### Tahap 5.5: Enrich Metadata via CrossRef API

Jalankan:

```bash
python src/pipeline/06_enrich_metadata.py
```

Mengisi field metadata yang kosong dari CrossRef API menggunakan DOI. Field yang di-enrich: `pages` (range halaman), `publisher` (nama publisher), `country` (negara dari author affiliation), dan `cites` (jumlah citation).

Stage ini **non-destructive** — hanya mengisi field yang masih kosong, tidak menimpa data yang sudah ada.

Output:
- `data/output/extracted/extraction_table.json` — updated dengan metadata dari CrossRef
- `data/output/extracted/crossref_enrichment_log.json` — audit log enrichment

### Tahap 6: Download PDF Full-Text

Jalankan:

```bash
python src/pipeline/07_generate_download.py
```

Generate tracker HTML untuk download PDF full-text berdasarkan DOI.

Output utama:
- `data/output/manual_download.html` — tracker untuk download manual
- `data/fulltext_pdfs/` — folder dengan PDF hasil download

### Tahap 8: Gemini Native Full-Text Engine (Smart Extraction)

**Tujuan:** AI (Gemini 2.0 Flash) membaca langsung file PDF untuk mengambil data kuantitatif (tabel) dan kualitatif secara akurat sesuai Protocol.

**Mengapa menggunakan Gemini Native?**
- **Akurasi Tabel**: Gemini "melihat" tabel sebagai gambar, sehingga angka statistik tidak tertukar.
- **Smart Markdown**: Menghasilkan catatan ringkas (bukan full-text verbatim) yang fokus pada kebutuhan protokol penelitian Anda.

**Cara menjalankan:**
```bash
python src/pipeline/08_fulltext_processor.py
```

**Hasil:**
- `data/output/extracted/extraction_table.json` (Terisi penuh dari full-text)
- `data/output/extracted/summaries_markdown/` (Ringkasan naratif)

### Tahap 10: Wiki Sync

Jalankan:

```bash
python src/pipeline/10_sync_to_wiki.py
```

Sync hasil ekstraksi ke wiki untuk mengorganisasi pengetahuan.

Output:
- `wiki/papers/` — organized knowledge structure

### Tahap 11: Quality Assessment (CASP Audit) - Audit Berbasis Data

Catatan mode:
- `slr_only`, `meta_analysis`, `hybrid`: jalankan tahap ini.
- `bibliometric`: tahap ini boleh di-skip jika protokol memang tidak memakai methodological appraisal.

Jalankan:

```bash
python src/pipeline/11_quality_assess.py
```

**Penting:** Tahap quality assessment sekarang berfungsi sebagai **Audit Metodologi**. AI tidak membaca PDF lagi, melainkan melakukan audit kritis terhadap metodologi yang sudah diekstrak di Stage 8. Ini memastikan penilaian jauh lebih akurat dan objektif.

Jika belum ada API key, gunakan agent `Quality Assessor`.

Contoh prompt:

```text
Gunakan Quality Assessor. Audit seluruh paper dengan CASP checklist berdasarkan extraction_table.json. Output: quality_assessed.json dengan quality_score (PASS/FAIL).
```

Output:
- `data/output/screened/quality_assessed.json` — hasil assessment dengan quality_score dan quality_decision
- `data/output/screened/quality_assessed.csv` — versi tabel untuk inspeksi manual

### Tahap 11b: Health Check & Reliability Backbone (Wajib Sebelum Sintesis)

Jalankan setelah Stage 8 selesai dan seluruh stage analitis yang relevan dengan `analysis_mode` sebelumnya sudah selesai, sebelum Stage 13/14:

```bash
python src/utils/health_check.py --fix-prisma
```

Tahap ini tidak menulis manuscript atau mengubah keputusan ilmiah. Fungsinya adalah memeriksa apakah data antar-stage konsisten.

Yang dicek:
- Paper `INCLUDE` saat screening tetapi hilang dari `extraction_table.json`
- Paper yang ada di extraction tetapi belum masuk `quality_assessed.json`
- Paper QA PASS tetapi tidak siap masuk synthesis
- PDF atau markdown full-text yang tidak cocok dengan DOI/paper_id
- Angka PRISMA yang tidak sinkron dengan data aktual
- Konfigurasi model LLM yang tidak terdaftar
- Warning schema dari output pipeline

Output:
- `data/output/paper_manifest.json` — status lengkap setiap paper di seluruh pipeline
- `data/output/prisma_counts_derived.json` — PRISMA hasil hitung ulang dari data aktual
- `data/output/health_report.json` — laporan machine-readable
- `data/output/health_report.md` — laporan yang mudah dibaca

Arti status:

| Status | Arti | Tindakan |
|---|---|---|
| `PASS` | Tidak ada masalah terdeteksi | Lanjut ke synthesis/writing |
| `WARN` | Ada warning, tetapi tidak ada error lineage | Baca warning; boleh lanjut jika warning tidak memengaruhi corpus |
| `FAIL` | Ada masalah serius antar-stage | Jangan lanjut sebelum direkonsiliasi |

Jika `health_report.md` menunjukkan paper bermasalah dan Anda memutuskan paper tersebut memang harus dikeluarkan, jalankan:

```bash
python src/utils/exclude_problem_papers.py
python src/utils/health_check.py --fix-prisma
python src/pipeline/16_generate_viewer.py
```

Script `exclude_problem_papers.py` akan:
- mengubah paper bermasalah menjadi `EXCLUDE` di `screened_results.json`
- menambahkan paper tersebut ke `excluded.json`
- menghapus record terkait dari `extraction_table.json`, `quality_assessed.json`, dan cache full-text
- menghitung ulang `stats_summary.json` dan `themes_summary.json`
- menulis audit trail ke `data/output/exclusion_reconciliation_log.json`

Gunakan tahap ini setiap kali Anda menemukan paper yang tidak punya full-text, tidak layak diproses, atau tidak lengkap sehingga tidak boleh masuk synthesis.

### Tahap 12: Hybrid Meta-Analysis Engine (JASP Ready)

Catatan mode:
- Jalankan hanya jika `analysis_mode` adalah `meta_analysis` atau `hybrid`.
- Jika `analysis_mode` adalah `slr_only` atau `bibliometric`, sistem akan menandai tahap ini sebagai `skipped`.

Jalankan:

```bash
python src/pipeline/12_meta_analysis_engine.py
```

Ini adalah tahap penghitungan angka statistik yang sangat ketat untuk jurnal standar Q1. 

**Keunggulan:**
- **Anti-Hallucination**: AI tidak disuruh berhitung. AI hanya mengambil angka, lalu Python yang menghitung secara matematis (Hedges' g, $I^2$, dll).
- **Intra-Study Aggregation**: Jika satu paper punya banyak hasil data, sistem otomatis menggabungkannya agar tidak merusak statistik.
- **JASP Ready Export**: Selain membuat grafik sendiri, sistem otomatis membuat file **`jasp_meta_analysis_ready.csv`**. Anda tinggal buka aplikasi JASP, masukkan file ini, dan semua grafik kelas dunia langsung jadi!

Output:
- `data/output/synthesized/meta_stats_results.json` — Angka statistik mentah.
- `data/output/synthesized/meta_analysis.json` — Ringkasan status meta-analysis untuk stage downstream.
- `data/output/synthesized/forest_plot_*.png` — Gambar grafik Forest Plot per kategori.
- `data/output/synthesized/jasp_meta_analysis_ready.csv` — File siap pakai untuk software JASP.

### Tahap 13: Sintesis dan Narrative Synthesis (Data-Driven)

Jalankan:

```bash
python src/pipeline/13_synthesize.py
```

Narrative synthesis, thematic analysis, bibliometric mapping, dan meta-analysis dari paper yang lolos tahap relevan.
 
 **Fitur Utama:**
 - **JASP-Aligned Reporting**: Otomatis memilih pelaporan **Fixed-Effect** atau **Random-Effects** berdasarkan nilai $I^2$ dari Stage 12 (Standar Q1).
 - **Evidence-Based Narrative**: Narasi disesuaikan dengan skor CASP (Stage 11). Jika kualitas rendah, AI akan melabelinya sebagai "Weak Evidence".
 - **Bibliometric Outputs**: Sistem kini menghasilkan `bibliometric_summary.json` dan `bibliometric_report.md`.
 - **Strategic Gap Identification**: Menghasilkan *Research Gap* yang selaras dengan **Gap Matrix (Stage 0)** yang dibuat di awal.

Jika perlu bantuan interpretasi, gunakan agent `Synthesizer`.

Contoh prompt:

```text
Gunakan Synthesizer. Baca extraction_table.json, quality_assessed.json, meta_stats_results.json, dan GAP_MATRIX.md. Susun narrative synthesis per RQ yang sinkron dengan hasil JASP.
```

Output:
- `data/output/synthesized/synthesis_narrative.md` — Narasi utama manuskrip.
- `data/output/synthesized/themes_summary.json` — Pengelompokan tema riset.
- `data/output/synthesized/research_gaps.md` — Celah riset yang ditemukan.
- `data/output/synthesized/stats_summary.json` — Ringkasan statistik deskriptif.

### Tahap 14: Write Draft Manuscript

Jalankan:

```bash
python src/pipeline/14_write_manuscript.py
```

Script ini menghasilkan file draft bilingual:
- `data/output/draft_manuscript_en.md` — draft English dalam Markdown
- `data/output/draft_manuscript_id.md` — draft Indonesian dalam Markdown
- `data/output/manuscript_en.docx` — draft English dalam format Word
- `data/output/manuscript_id.docx` — draft Indonesian dalam format Word
- `data/output/REWRITING_GUIDE.md` — panduan menulis ulang agar lulus Turnitin

Manuscript di-generate **per section** (Abstract, Introduction, Methodology, Results, Discussion, Conclusion, References) agar setiap bagian lebih detail dan mendalam.

Setiap paragraf ditandai `[AUTHOR MUST REWRITE]` — artinya **wajib ditulis ulang** dengan kata-kata sendiri sebelum submit ke jurnal.

Jika perlu bantuan penulisan, gunakan agent `SLR Writer`.

Contoh prompt:

```text
Gunakan SLR Writer. Baca seluruh output pipeline (synthesis_narrative.md, meta_analysis.json, themes_summary.json, research_gaps.md) dan bantu saya menulis draft manuscript SLR standar Scopus Q1/Q2.
```

### Tahap 15: Audit Manuscript

Jalankan:

```bash
python src/pipeline/15_audit_manuscript.py
```

Script ini menjalankan **substantive quality audit** otomatis pada draft manuscript yang telah ditulis. Audit ini bertindak sebagai "Reviewer 2" yang kritis dan skeptis untuk memastikan kualitas jurnal Scopus Q1/Q2.

**Output yang dihasilkan:**
- `data/output/audit_report.json` — laporan audit dalam format JSON
- `data/output/AUDIT_REPORT_en.md` — laporan audit formal English
- `data/output/AUDIT_REPORT_id.md` — laporan audit formal Indonesian
- `data/output/FULL_AUDIT_SUMMARY.txt` — ringkasan audit untuk referensi cepat

**Fokus Audit:**
1. **PRISMA 2020 Compliance** — memastikan semua item checklist PRISMA terpenuhi dengan rigor metodologis
2. **Validitas Meta-Analysis** — membedakan antara genuine meta-analysis vs. vote-counting
3. **Citation Narrative Integration** — memastikan sitasi terintegrasi dalam narasi, bukan hanya ditambahkan di akhir
4. **Limitation Magnitude & Consequence** — limitations dinyatakan dengan dampak kuantitatif
5. **Novelty Clarity** — kontribusi kebaruan jelas dan tidak overstated

**Audit Score Interpretation:**

| Score | Status | Action |
|-------|--------|--------|
| 90-100 | ✅ PASSED | Ready untuk human rewriting |
| 70-89 | ⚠️ NEEDS REVIEW | Minor substantive fixes needed |
| 50-69 | ❌ NEEDS REVISION | Major content revision required |
| <50 | ❌ FAILED | Complete restructuring needed |

Gunakan output audit ini sebagai guidance untuk rewriting manual manuscript. Setiap finding dari audit engine harus ditinjau dan ditindaklanjuti sebelum mengirim ke jurnal.

### Tahap 16: Generate HTML Viewer (v3.6)

Jalankan:

```bash
python src/pipeline/16_generate_viewer.py
```

Sistem otomatis membuat dashboard interaktif untuk melihat seluruh hasil SLR Anda dalam satu tampilan web profesional.

**Hasil:**
- `data/output/viewer/index.html` — Dashboard utama & PRISMA
- `data/output/viewer/strategy.html` — Blueprint & Matrix
- `data/output/viewer/screening.html` — Hasil seleksi
- `data/output/viewer/extraction.html` — Tabel data lengkap
- `data/output/viewer/synthesis.html` — Narasi & Meta-Analisis
- `data/output/viewer/manuscript.html` — Draf artikel akademik
- `data/output/viewer/health.html` — Health check, error/warning, dan paper yang perlu perhatian

Tahap ini juga menjadi tahap finalisasi figure:
- `data/output/figure/prisma/prisma_2020_flow.png`
- `data/output/figure/prisma/prisma_2020_flow.svg`
- `data/output/figure/prisma/prisma_2020_flow.json`
- `data/output/figure/meta_analysis/forest_plot_*.png`
- `data/output/figure/meta_analysis/forest_plot_*.json`
- `data/output/figure/descriptive/publication_trend.(png|svg|json)`
- `data/output/figure/descriptive/country_distribution.(png|svg|json)`
- `data/output/figure/descriptive/top_journals.(png|svg|json)`
- `data/output/figure/manifest.json`

Prinsip kerja figure:
- Sistem membuat figure sesuai data yang tersedia di pipeline, bukan berdasarkan instruksi bebas tanpa sumber data.
- Forest plot berasal dari Tahap 12 lalu dikemas ulang di Tahap 16.
- PRISMA 2020 final dibuat di Tahap 16 agar memakai counts yang sudah direkonsiliasi.
- Chart deskriptif dibuat dari `data/output/synthesized/stats_summary.json`.

---

## 13. Kapan Boleh Menjalankan Semua Tahap Sekaligus?

Anda boleh mencoba menjalankan pipeline utama hanya jika semua persiapan awal sudah siap.

Jalankan:

```bash
python src/main.py
```

Lakukan ini hanya jika:
- `config/config.yaml` sudah final atau hampir final
- `data/output/protocol.md` sudah ada
- `data/output/search_strings.md` sudah ada
- file CSV sudah tersedia di `data/input/`

Untuk pemula, lebih aman menjalankan script satu per satu daripada langsung menjalankan semuanya.

## 14. Checklist Praktis untuk Pengguna Awam

Sebelum mulai, pastikan:

- [ ] VS Code sudah terpasang
- [ ] Python dan dependency sudah terinstall
- [ ] `config/config.yaml` sudah diisi minimal
- [ ] `analysis_mode` sudah dipilih sejak awal
- [ ] topik penelitian sudah ada
- [ ] Anda sudah memanggil `Journal Strategist`
- [ ] `JOURNAL_BLUEPRINT.md` sudah dibuat
- [ ] Kebijakan AI jurnal target sudah dicek
- [ ] Anda sudah memanggil `Protocol Designer`
- [ ] `data/output/protocol.md` sudah dibuat
- [ ] isi protokol sudah disinkronkan ke `config/config.yaml`
- [ ] Anda sudah memanggil `Search Strategy`
- [ ] `data/output/search_strings.md` sudah dibuat
- [ ] CSV hasil Publish or Perish sudah ada di `data/input/`
- [ ] Sebelum synthesis/writing, `python src/utils/health_check.py --fix-prisma` sudah dijalankan
- [ ] Jika health status `FAIL`, paper bermasalah sudah direkonsiliasi dengan `python src/utils/exclude_problem_papers.py`
- [ ] `data/output/health_report.md` sudah dicek dan tidak ada error lineage

## 15. Kesalahan yang Paling Sering Terjadi

### Masalah 1: User langsung menjalankan pipeline tanpa protokol

Akibatnya:
- RQ belum jelas
- kriteria belum jelas
- screening menjadi tidak konsisten

Solusi:
- mulai dari `Protocol Designer` dulu

### Masalah 2: `protocol.md` sudah berubah tetapi `config/config.yaml` belum diperbarui

Akibatnya:
- screening, extraction, dan writing bisa memakai aturan yang tidak sinkron

Solusi:
- samakan isi penting di kedua file tersebut

### Masalah 3: CSV tidak terbaca

Penyebab umum:
- file tidak diletakkan di `data/input/`
- format export dari Publish or Perish bermasalah

### Masalah 4: Screening, QA, atau extraction berhenti

Penyebab umum:
- LLM API key belum tersedia (belum set `LLM_API_KEY` di `.env`)
- file tahap sebelumnya belum ada

Solusi:
- gunakan agent terkait di Copilot Chat
- atau siapkan API key (lihat bagian 4) lalu jalankan ulang script

### Masalah 5: Tidak yakin format JSON output benar

Gunakan schema validator:

```bash
python src/utils/validate_schema.py
```

Validator akan memeriksa semua file output dan melaporkan jika ada field yang kurang atau format yang salah. Berguna terutama jika Anda membuat JSON secara manual melalui agent Copilot.

Untuk validasi stage tertentu saja:

```bash
python src/utils/validate_schema.py --stage 4
```

### Masalah 6: Angka paper berbeda antar-stage atau PRISMA terasa tidak sinkron

Contoh gejala:
- Screening menunjukkan 39 paper include, tetapi extraction hanya 37.
- Viewer menampilkan angka berbeda dari `quality_assessed.json`.
- Ada paper yang tidak punya PDF/full-text tetapi masih masuk synthesis.

Gunakan health check:

```bash
python src/utils/health_check.py --fix-prisma
```

Baca:

```text
data/output/health_report.md
```

Jika status `FAIL`, jangan lanjut ke synthesis/manuscript. Jika paper bermasalah memang harus dikeluarkan:

```bash
python src/utils/exclude_problem_papers.py
python src/utils/health_check.py --fix-prisma
python src/pipeline/16_generate_viewer.py
```

Setelah itu cek ulang `health_report.md`. Target minimal sebelum lanjut adalah `WARN` tanpa error lineage.

## Bagian D. Setelah Draft Selesai (Turnitin & Submission)

## 16. Apa yang Harus Dilakukan Setelah Draft Manuscript Jadi?

Draft yang dihasilkan sistem ini adalah **scaffold AI**. **JANGAN** langsung submit ke jurnal.

Baca file `data/output/REWRITING_GUIDE.md` yang otomatis dibuat bersama manuscript. File ini berisi panduan lengkap menulis ulang.

### Langkah wajib setelah draft jadi:

1. **Baca draft + data mentah** — pahami ide setiap paragraf
2. **Tutup draft, tulis ulang dari nol** — jangan copy-paste dari draft AI
3. **Masukkan data spesifik** — angka, nama author, tahun dari `extraction_table.json`
4. **Edit ritme kalimat** — baca keras, pecah kalimat monoton
5. **Verifikasi semua referensi** — buka DOI, pastikan klaim benar
6. **Cek Turnitin AI** — target: 0% AI Generated, < 20% similarity
7. **Final proofread** — grammar, terminologi, format APA 7th

### Target sebelum submission:

- [ ] Turnitin AI Writing Indicator: **0%**
- [ ] Turnitin Similarity Index: **< 20%**
- [ ] Semua `[AUTHOR MUST REWRITE]` sudah dihapus
- [ ] Semua `[AUTHOR INPUT NEEDED]` sudah diisi
- [ ] Semua referensi sudah diverifikasi manual
- [ ] Word count sesuai target jurnal
- [ ] English proficiency level C1+ (IELTS 7.0+ equivalent)

### English Proficiency untuk Jurnal Q1/Q2

Untuk jurnal Scopus Q1/Q2, standar bahasa Inggris sangat tinggi. Sistem ini hanya menyediakan scaffold, tetapi kualitas bahasa akhir tetap bergantung pada penulis:

**Standar minimum:**
- Academic English level: C1+ (IELTS 7.0+ atau TOEFL 100+)
- Grammar dan spelling: 100% error-free
- Terminologi akademis: tepat dan konsisten
- Flow dan cohesion: alur logika yang natural

**Rekomendasi:**
1. **Gunakan jasa proofreading profesional** sebelum submit
   - Layanan seperti Elsevier Author Services, Springer Nature Author Services, atau native speaker proofreader
   - Biaya: USD 50-200 per artikel (tergantung panjang dan layanan)

2. **Self-check sebelum proofreading:**
   - Gunakan Grammarly Premium atau ProWritingAid
   - Baca manuscript dengan keras untuk check naturalness
   - Pastikan tidak ada kalimat dengan struktur monoton

3. **Cek terminologi:**
   - Gunakan istilah yang konsisten dengan literature di field tersebut
   - Hindari kata-kata yang terlalu umum ("many", "several")
   - Gunakan kata spesifik dan teknis yang tepat

## 17. Reset Project untuk Memulai SLR Baru

Jika Anda sudah menyelesaikan satu project SLR dan ingin memulai project baru, sistem menyediakan script reset dengan dua pilihan.

### Cara Reset Project

Jalankan:

```bash
python src/reset_project.py
```

Script akan menampilkan menu dengan dua pilihan reset:

### [1] Reset Biasa

Menghapus semua artefak project tetapi mempertahankan environment dan file input. Cocok untuk memulai topik baru tanpa install ulang.

**Yang dihapus:**
- Semua file di `data/output/` (extracted, screened, synthesized, viewer, figure)
- Semua file di `data/fulltext_pdfs/` dan `data/fulltext_markdown/`
- Semua konten wiki (authors, journals, methods, papers, gaps, comparisons, log.md)
- `JOURNAL_BLUEPRINT.md`, `GAP_MATRIX.md`, `protocol.md`, `search_strings.md` (root & output)
- `pipeline_state.json` di-reset ke default

**Yang TIDAK dihapus:**
- `.venv/` (Python environment tetap terinstall)
- `data/input/` (file input tetap dipertahankan pada reset biasa)
- Source code dan template sistem

### [2] Reset Full

Menghapus SEMUA termasuk Python environment, kembali ke kondisi awal setelah clone/unzip. Cocok jika ingin clean slate total.

**Yang dihapus — semua dari Reset Biasa, PLUS:**
- `.venv/` (Python environment akan dihapus!)
- `data/input/` (file input project)

**Hasil akhir:** sama dengan kondisi setelah clone/unzip baru. Anda perlu:
1. Jalankan `python setup.sh` ulang (install .venv)
2. Masukkan ulang file input ke `data/input/`
3. Lanjutkan seperti project baru

### Konfirmasi Keamanan

Script meminta konfirmasi dengan mengetik **"YA"** sebelum menghapus data. Reset Full memiliki konfirmasi terpisah karena sifatnya yang lebih destructif.

### Apa yang Di-reset Ulang

- `config/config.yaml` akan dibuat ulang dari `config/config.default.yaml`
- Ini berarti struktur default konfigurasi tetap dipertahankan untuk project baru, tetapi isi spesifik project aktif akan kembali ke template default
- Jika ingin mengubah perilaku default reset ke depan, edit `config/config.default.yaml`, bukan hanya `config/config.yaml`
- Blok `figures:` di `config/config.default.yaml` sekarang menentukan perilaku default untuk project baru, termasuk PRISMA, forest plot packaging, dan descriptive charts

### Setelah Reset

Setelah reset selesai, Anda bisa memulai project baru dengan langkah yang sama:
1. Tinjau `config/config.yaml` hasil reset, karena file ini baru dibuat ulang dari `config/config.default.yaml`
2. Update `config/config.yaml` dengan topik baru
3. Panggil `Protocol Designer` untuk protokol baru
4. Panggil `Search Strategy` untuk search string baru
5. Siapkan CSV baru di `data/input/`
6. Jalankan pipeline

## 18. Ringkasan Singkat

Jika Anda hanya punya topik, urutan paling aman:

1. Isi dasar `config/config.yaml`
2. Panggil `Protocol Designer`
3. Finalkan `protocol.md`
4. Sinkronkan hasilnya ke `config/config.yaml`
5. Panggil `Search Strategy`
6. Cari paper dan export CSV ke `data/input/`
7. Jalankan pipeline tahap 3 sampai 14
8. Baca `REWRITING_GUIDE.md` dan tulis ulang draft
9. Cek Turnitin, submit ke jurnal

**Untuk memulai project baru:** Jalankan `python src/reset_project.py` untuk membersihkan data project sebelumnya.

Mulai dari topik saja sudah cukup. Sistem ini dirancang agar agent membantu Anda membentuk RQ, scope, criteria, dan tahapan SLR secara bertahap.
