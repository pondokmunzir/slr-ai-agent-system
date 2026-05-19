# CHANGELOG — SLR AI Agent System

---

## [V3.7.2] — 2026-05-18

### Ringkasan
Patch V3.7.2 menambahkan bridge VOSviewer generik untuk memperkuat visualisasi bibliometric lintas project. Project dengan `analysis_mode: bibliometric` atau `analysis_mode: hybrid` kini dapat mengekspor network internal ke format VOSviewer tanpa konfigurasi ulang manual.

### Perubahan Utama
- Menambahkan exporter VOSviewer generik untuk `co-authorship` dan `keyword co-occurrence`.
- Menghasilkan bundle `data/output/vosviewer/` berisi file `.json`, `_map.txt`, `_network.txt`, `manifest.json`, dan `README.md`.
- Mengintegrasikan ekspor VOSviewer ke Stage 16 viewer dan `data/output/figure/manifest.json`.
- Menambahkan konfigurasi default `bibliometric.vosviewer` agar fitur ikut tersedia pada project berikutnya.
- Memperbarui checklist dan protocol template bibliometric untuk mencantumkan handoff VOSviewer.

### File Terdampak
- `src/utils/vosviewer_exporter.py`
- `src/pipeline/16_generate_viewer.py`
- `src/utils/figure_builder.py`
- `src/reset_project.py`
- `config/config.default.yaml`
- `templates/bibliometric_project_checklist.md`
- `templates/protocol_bibliometric_template.md`

---

## [V3.7.1] — 2026-05-16

### Ringkasan
Patch V3.7.1 merapikan kontrak ekstraksi lintas mode agar `slr_only` tetap menyimpan metadata bibliografis dasar. Perbedaannya sekarang tegas: semua mode berbagi baseline metadata corpus yang sama, sementara `bibliometric` dan `hybrid` hanya menambah kedalaman analisis mapping.

### Perubahan Utama
- Menetapkan baseline metadata bibliografis lintas mode di `config/config.default.yaml`, `templates/extraction_template.json`, dan `src/utils/helpers.py`.
- Memperbarui prompt extractor abstract dan full text agar `slr_only` tetap menangkap author, journal/source, DOI, country, keywords, citation count, dan references bila tersedia.
- Menyinkronkan Stage 5 dan Stage 6 agar field baseline seperti `citation_count`, `references_count`, dan `author_affiliations` dapat terisi lebih konsisten untuk project baru.
- Menyelaraskan agent `Protocol Designer`, `Data Extractor`, `Synthesizer`, dan `SLR Orchestrator` supaya tidak lagi mengartikan `slr_only` sebagai corpus tanpa metadata bibliografis.
- Memperjelas panduan pengguna dan quick start bahwa `slr_only` tetap menghasilkan descriptive corpus profile, hanya tanpa science-mapping sebagai kontribusi utama.
- Menambahkan toolkit bibliometric project bootstrap:
  - `templates/protocol_bibliometric_template.md`
  - `templates/bibliometric_project_checklist.md`
  - `templates/bibliometric_e2e_test_scenario.md`
  - `src/utils/check_bibliometric_readiness.py`

### File Terdampak
- `config/config.default.yaml`
- `templates/extraction_template.json`
- `src/utils/helpers.py`
- `src/pipeline/05_extract_abstract.py`
- `src/pipeline/06_enrich_metadata.py`
- `src/pipeline/08_fulltext_processor.py`
- `src/reset_project.py`
- `src/utils/check_bibliometric_readiness.py`
- `.github/agents/protocol-designer.agent.md`
- `.github/agents/data-extractor.agent.md`
- `.github/agents/synthesizer.agent.md`
- `.github/agents/slr-orchestrator.agent.md`
- `QUICK_START_SLR_AI_AGENT_SYSTEM.md`
- `PANDUAN_PENGGUNAAN_SLR_AI_AGENT_SYSTEM.md`
- `templates/protocol_bibliometric_template.md`
- `templates/bibliometric_project_checklist.md`
- `templates/bibliometric_e2e_test_scenario.md`

---

## [V3.7.0] — 2026-05-16

### Ringkasan
Patch V3.7.0 memperkenalkan **analysis mode governance** sejak awal project. Pengguna sekarang memilih `slr_only`, `bibliometric`, `meta_analysis`, atau `hybrid` di tahap protocol/config, lalu pipeline, agent, viewer, dan manuscript mengikuti keputusan tersebut secara konsisten.

### Fitur Baru

#### Analysis Mode as First-Class Project Contract
- Menambahkan `analysis_mode` ke `config/config.yaml` dan `config/config.default.yaml`.
- Menambahkan utilitas normalisasi/gating mode analisis di `src/utils/helpers.py`.
- Pipeline sekarang mengenali empat mode resmi:
  - `slr_only`
  - `bibliometric`
  - `meta_analysis`
  - `hybrid`

#### Pipeline Gating yang Lebih Aman
- Stage 11 kini dapat di-skip secara metodologis untuk `bibliometric`.
- Stage 12 kini akan menulis payload status yang aman saat meta-analysis tidak diaktifkan oleh `analysis_mode`, alih-alih membiarkan stage downstream menebak.
- Stage 13 kini selalu menghasilkan output bibliometric:
  - `data/output/synthesized/bibliometric_summary.json`
  - `data/output/synthesized/bibliometric_report.md`

#### Writing, Viewer, dan Dashboard Selaras dengan Analysis Mode
- Stage 14 sekarang membaca `analysis_mode`, bibliometric summary/report, dan menyesuaikan governance PRISMA item yang memang tidak relevan untuk mode tertentu.
- Viewer synthesis kini menampilkan tab bibliometric khusus.
- Dashboard frontend memperbarui nama Stage 13 dan quick link ke bibliometric report.

#### Agent & Documentation Refresh
- Agent `Protocol Designer`, `Search Strategy`, `SLR Orchestrator`, `SLR Writer`, `Synthesizer`, dan `Meta-Analyst` diperbarui agar menjadikan `analysis_mode` sebagai keputusan awal yang mengikat.
- `QUICK_START_SLR_AI_AGENT_SYSTEM.md` dan `PANDUAN_PENGGUNAAN_SLR_AI_AGENT_SYSTEM.md` kini menjelaskan kapan Stage 11/12 wajib, opsional, atau di-skip sesuai mode.

### Bug Fixes & Consistency

- Memperbaiki header salah label pada `src/pipeline/15_audit_manuscript.py` dari “Stage 13” menjadi “Stage 15”.
- Menambahkan `meta_analysis.json` sebagai ringkasan status yang eksplisit untuk mengurangi mismatch antara Stage 12, Stage 14, validator, dan viewer.
- Memperluas validator schema agar mengenali payload meta-analysis baru dan output bibliometric.

### Perubahan File

#### Diubah
- `config/config.yaml`
- `config/config.default.yaml`
- `src/utils/helpers.py`
- `src/registry.py`
- `src/main.py`
- `src/pipeline/11_quality_assess.py`
- `src/pipeline/12_meta_analysis_engine.py`
- `src/pipeline/13_synthesize.py`
- `src/pipeline/14_write_manuscript.py`
- `src/pipeline/15_audit_manuscript.py`
- `src/pipeline/16_generate_viewer.py`
- `src/utils/validate_schema.py`
- `src/reset_project.py`
- `frontend/dashboard.html`
- `.github/agents/protocol-designer.agent.md`
- `.github/agents/search-strategy.agent.md`
- `.github/agents/slr-orchestrator.agent.md`
- `.github/agents/slr-writer.agent.md`
- `.github/agents/synthesizer.agent.md`
- `.github/agents/meta-analyst.agent.md`
- `QUICK_START_SLR_AI_AGENT_SYSTEM.md`
- `PANDUAN_PENGGUNAAN_SLR_AI_AGENT_SYSTEM.md`

---

## [V3.6.2] — 2026-05-15

### Ringkasan
Patch V3.6.2 memperkuat **PRISMA 2020 governance**, **submission-readiness control**, dan **AI policy governance** untuk target jurnal Q1/Q2 yang menerima penggunaan AI dengan disclosure yang tepat. Fokus patch ini adalah membuat workflow lebih jujur, lebih traceable, dan lebih aman terhadap mismatch antara pipeline operasional, pelaporan PRISMA, dan kebijakan AI jurnal target.

---

### Fitur Baru

#### PRISMA 2020 Governance & Compliance Artifacts
- Menambahkan template resmi internal `templates/prisma_2020_checklist_template.md`.
- Menambahkan artefak evaluasi:
  - `data/output/PRISMA_2020_COMPLIANCE_MATRIX.md`
  - `data/output/PRISMA_2020_PATCH_PLAN.md`
  - `data/output/PRISMA_2020_CHECKLIST_FILLED.md`
- Stage 14 sekarang meregenerasi snapshot checklist PRISMA 2020 dari artefak project aktual.

#### Submission Readiness & Reporting Control
- Menambahkan evaluasi `submission readiness` terpisah dari sekadar keberhasilan generate manuscript.
- Menambahkan artefak:
  - `data/output/submission_readiness_report.md`
  - `data/output/review_process_log.json`
  - `data/output/protocol_amendments.md`
- Stage 14 sekarang dapat melakukan hard-stop jika metadata pelaporan penting belum cukup untuk klaim submission-ready.

#### Certainty & Sensitivity Traceability
- Menambahkan output certainty outcome-level:
  - `data/output/synthesized/certainty_of_evidence.json`
  - `data/output/synthesized/certainty_of_evidence.md`
- Menambahkan output sensitivity:
  - `data/output/synthesized/sensitivity_analysis.md`
- Menambahkan baseline robustness check lintas subset kualitas dan subset extraction confidence.

#### AI Policy Governance for Q1/Q2 Submission
- Menambahkan blok `ai_policy` ke `config/config.yaml` dan `config/config.default.yaml`.
- Menambahkan template:
  - `templates/ai_disclosure_templates.md`
  - `templates/journal_blueprint_template.md`
- Menambahkan artefak disclosure baru:
  - `data/output/AI_USE_LOG.json`
  - `data/output/AI_DISCLOSURE_PACKAGE.md`
- Stage 14 sekarang membangun disclosure package untuk penggunaan AI dalam writing process dan workflow review/metode.

#### Stage 0 Journal Policy Fit
- `Journal Strategist` sekarang wajib menilai `AI Policy Fit` jurnal target, bukan hanya aim/scope dan gap.
- `Protocol Designer` dan `SLR Orchestrator` sekarang memperlakukan hasil `AI Policy Fit` sebagai constraint metodologis dan governance checkpoint.

#### Reset & Config Default Preservation
- Menambahkan `config/config.default.yaml` sebagai source of truth untuk reset config project baru.
- `src/reset_project.py` sekarang membaca template default dari file eksternal itu.
- Reset kini mempertahankan fondasi config baru (PRISMA/readiness/AI policy), sambil tetap menghapus artefak project lama.

---

### Bug Fixes & Data Consistency

- Menyelaraskan terminology quality appraisal aktif ke basis CASP-adapted agar tidak bentrok dengan config pipeline.
- Memaksa Stage 14 memakai `data/output/prisma_counts_derived.json` sebagai source of truth PRISMA ketika tersedia.
- Memblok klaim meta-analysis jika source study IDs tidak cocok dengan `extraction_table.json`, dan fallback ke source kuantitatif yang tervalidasi.
- Memperjelas dokumentasi reset agar tidak lagi keliru menyatakan `config/config.yaml` tetap utuh setelah reset.
- Menambahkan pengingat pasca-reset bahwa `JOURNAL_BLUEPRINT.md`, `GAP_MATRIX.md`, dan blok `ai_policy` harus dibangun ulang untuk setiap project baru.

---

### Perubahan File

#### Baru
- `config/config.default.yaml`
- `templates/ai_disclosure_templates.md`
- `templates/journal_blueprint_template.md`
- `data/output/PRISMA_2020_COMPLIANCE_MATRIX.md`
- `data/output/PRISMA_2020_PATCH_PLAN.md`
- `data/output/PRISMA_2020_CHECKLIST_FILLED.md`
- `data/output/protocol_amendments.md`
- `data/output/review_process_log.json`
- `data/output/submission_readiness_report.md`
- `data/output/AI_USE_LOG.json`
- `data/output/AI_DISCLOSURE_PACKAGE.md`
- `data/output/synthesized/certainty_of_evidence.json`
- `data/output/synthesized/certainty_of_evidence.md`
- `data/output/synthesized/sensitivity_analysis.md`

#### Diubah
- `src/pipeline/12_meta_analysis_engine.py`
- `src/pipeline/14_write_manuscript.py`
- `src/reset_project.py`
- `config/config.yaml`
- `.github/agents/journal-strategist.agent.md`
- `.github/agents/protocol-designer.agent.md`
- `.github/agents/slr-orchestrator.agent.md`
- `PANDUAN_PENGGUNAAN_SLR_AI_AGENT_SYSTEM.md`
- `QUICK_START_SLR_AI_AGENT_SYSTEM.md`
- `templates/extraction_template.json`

---

### Workflow Baru yang Direkomendasikan

Sebelum mulai Stage 1 untuk project baru:

```bash
1. Jalankan Stage 0 dengan @journal-strategist
2. Validasi AI Policy Fit jurnal target
3. Sinkronkan hasilnya ke blok ai_policy di config/config.yaml
```

Sebelum mengklaim manuscript `submission-ready`:

```bash
1. Pastikan PRISMA checklist project sudah diregenerate
2. Pastikan submission_readiness_report.md tidak BLOCKED
3. Pastikan AI_DISCLOSURE_PACKAGE.md sesuai dengan kebijakan jurnal target
```

---

## [V3.6.1] — 2026-05-14

### Ringkasan
Patch V3.6.1 menambahkan **Reliability Backbone** untuk membuat pipeline lebih auditable, konsisten, dan aman sebelum masuk ke sintesis/manuskrip. Fokus utama patch ini adalah menghilangkan silent mismatch antar-stage, membangun manifest paper, menghitung ulang PRISMA dari data aktual, dan menyediakan health report yang bisa dibaca manusia maupun dashboard viewer.

---

### Fitur Baru

#### Paper Manifest & Derived PRISMA
- Menambahkan `data/output/paper_manifest.json` sebagai catatan lineage setiap paper dari import, screening, extraction, full-text, QA, sampai synthesis.
- Menambahkan `data/output/prisma_counts_derived.json` sebagai PRISMA yang dihitung ulang dari output aktual pipeline.
- `data/output/screened/prisma_counts.json` sekarang dapat diperbarui dari derived counts agar angka PRISMA tidak bergantung pada update manual yang rawan mismatch.

#### Pipeline Health Check
- Menambahkan CLI baru:
  ```bash
  python src/utils/health_check.py
  python src/utils/health_check.py --fix-prisma
  python src/utils/health_check.py --strict
  ```
- Health check mendeteksi:
  - paper `INCLUDE` di screening tetapi hilang dari extraction,
  - paper extracted tetapi belum QA,
  - QA PASS tetapi hilang dari data synthesis,
  - PDF/markdown full-text yang tidak match,
  - mismatch angka PRISMA,
  - model LLM stage yang tidak terdaftar,
  - schema warning dari output pipeline.
- Output baru:
  - `data/output/health_report.json`
  - `data/output/health_report.md`

#### Problem Paper Reconciliation
- Menambahkan CLI baru:
  ```bash
  python src/utils/exclude_problem_papers.py
  ```
- Script ini memindahkan paper bermasalah dari health report ke jalur `EXCLUDE`, membersihkan record terkait dari extraction/QA/fulltext cache, lalu memperbarui statistik dan themes summary.
- Output audit:
  - `data/output/exclusion_reconciliation_log.json`

#### Viewer Health Page
- Viewer sekarang memiliki halaman baru:
  - `data/output/viewer/health.html`
- Sidebar viewer menambahkan menu **Health**.
- Dashboard utama dan health page kini menampilkan angka corpus final yang sudah direkonsiliasi.

---

### Bug Fixes & Data Consistency

- Memperbaiki missing import `csv` di `src/pipeline/11_quality_assess.py`.
- Memperbaiki instruksi Stage 7 yang masih menunjuk script lama `08_convert_to_markdown.py`; sekarang menunjuk `08_fulltext_processor.py`.
- Memperbaiki ID model Stage 13/14 dari `deepseek-v4-pro` menjadi `deepseek/deepseek-v4-pro`.
- Memperbaiki `pico_synonyms` yang masih berisi domain pendidikan; sekarang sesuai domain broiler heat stress.
- Memperbaiki viewer PRISMA agar memakai `assessed_for_quality` ketika tersedia, bukan angka included dari screening.
- Meregenerasi PRISMA, health report, dan viewer setelah rekonsiliasi paper bermasalah.

---

### Perubahan File

#### Baru
- `src/utils/prisma_builder.py`
- `src/utils/health_check.py`
- `src/utils/exclude_problem_papers.py`
- `data/output/paper_manifest.json`
- `data/output/prisma_counts_derived.json`
- `data/output/health_report.json`
- `data/output/health_report.md`
- `data/output/exclusion_reconciliation_log.json`
- `data/output/viewer/health.html`

#### Diubah
- `src/pipeline/11_quality_assess.py`
- `src/pipeline/07_generate_download.py`
- `src/pipeline/16_generate_viewer.py`
- `config/config.yaml`
- `src/reset_project.py`
- `data/output/screened/prisma_counts.json`
- `data/output/screened/screened_results.json`
- `data/output/screened/excluded.json`
- `data/output/extracted/extraction_table.json`
- `data/output/screened/quality_assessed.json`
- `data/output/synthesized/stats_summary.json`
- `data/output/synthesized/themes_summary.json`

---

### Workflow Baru yang Direkomendasikan

Setelah Stage 8 dan Stage 11, sebelum synthesis/writing, jalankan:

```bash
python src/utils/health_check.py --fix-prisma
```

Jika status `FAIL` dan paper bermasalah memang harus dikeluarkan:

```bash
python src/utils/exclude_problem_papers.py
python src/utils/health_check.py --fix-prisma
python src/pipeline/16_generate_viewer.py
```

Jika status sudah `WARN` tanpa error lineage, pipeline boleh lanjut ke synthesis/manuscript. Warning schema tetap perlu dicermati, tetapi tidak selalu memblokir.

---

## [V3.6] — 2026-05-11

### Ringkasan
Rilis V3.6 berfokus pada **Peningkatan Kualitas Sintesis & Novelty Framing** (Pencegahan Halusinasi dan Penguatan Kontribusi). Pembaruan utama dilakukan pada instruksi `Synthesizer Agent` untuk memastikan *Systematic Literature Review* (SLR) yang dihasilkan memiliki standar *Structured Research Gap* dan *Integrative Novelty* yang layak untuk publikasi jurnal bereputasi internasional (Q1/Q2).

---

### Fitur Utama

#### Structured Research Gap Mapping
- **Gap Matrix Integration**: Mengubah pencarian gap dari sebatas "celah kosong" menjadi klasifikasi formal 4 tipe gap: *Theoretical*, *Methodological*, *Contextual*, dan *Inconsistency Gap*.
- **Anti Pseudo-Gap Filter**: Menambahkan perlindungan instruksi agar agen AI menolak *Pseudo-Gaps* (gap semu) yang hanya berargumen "belum ada penelitian di lokasi/tahun ini" tanpa memberikan landasan teoretis.

#### Novelty Framing & Contribution Statement
- **Integrative Novelty**: Agen tidak lagi sekadar mendata gap, tetapi diinstruksikan merumuskan model/kerangka konseptual baru sebagai *Synthesis Novelty*.
- **3-Element Contribution**: Agen kini diwajibkan menyusun *Contribution Statement* yang tegas (berisi: Diskursus Teori, Keterbatasan terdahulu, dan Pembaruan konseptual) untuk menghindari klaim kontribusi yang terlalu normatif.

#### Pencegahan Halusinasi Meta-Analisis (Fatal Flaw Fix)
- **Delegasi Perhitungan**: Melarang keras agen LLM untuk menghitung statistik kompleks (*Random-Effects Model*, Confidence Interval, $I^2$) secara mandiri guna mencegah halusinasi data.
- **Python-Engine Synergy**: Agent kini murni diinstruksikan untuk *membaca* dan *menginterpretasikan* narasi dari hasil perhitungan deterministik script Python (`12_meta_analysis_engine.py`).

#### Integrasi Quality Assessment & Data Overload
- **CASP/RoB Evaluator**: Agen diwajibkan membaca `quality_assessment_results.json` sehingga penentuan *Level of Evidence* berbasis pada validitas riset, bukan sekadar konsistensi hasil.
- **Data Overload Strategy**: Menambahkan penanganan kognitif LLM (*Lost in the Middle*) saat menghadapi ekstraksi data berskala raksasa.

---

### Perubahan File

#### `.github/agents/synthesizer.agent.md`
- Penulisan ulang **Tahap 2** (Meta-Analysis Interpretation).
- Penulisan ulang **Tahap 4** (Narrative Synthesis) untuk integrasi Quality Assessment dan pembedahan variasi PICO.
- Rombak total **Tahap 5** (Research Gap) menjadi format *Structured Research Gap & Novelty Framing*.
- Perbaikan urutan referensi tahapan pipeline di bagian persiapan.

---

## [V3.5] — 2026-05-10

### Ringkasan
Rilis V3.5 memperkenalkan **Gemini Native PDF Engine**, sebuah lompatan besar dalam akurasi ekstraksi data ilmiah. Dengan memanfaatkan model **Gemini 2.0 Flash**, sistem kini memproses file PDF secara langsung tanpa melalui konversi teks perantara (PyMuPDF). Hal ini memungkinkan rekonstruksi tabel statistik yang sempurna dan pemahaman layout yang jauh lebih baik, sesuai dengan standar tinggi jurnal Q1/Q2.

---

### Fitur Utama

#### Gemini Native PDF Processing (Stage 8)
- **Direct PDF Analysis**: Sistem mengirimkan file PDF (Base64) langsung ke Gemini 2.0 Flash via OpenRouter.
- **Smart Markdown Concept**: Menggantikan konversi teks verbatim dengan ringkasan cerdas yang berfokus pada **Section 7 Protocol.md**.
- **Table Reconstruction**: Menggunakan kemampuan vision Gemini untuk membangun ulang tabel ilmiah ke format Markdown yang akurat.
- **Merged Flow**: Menggabungkan proses konversi (lama Stage 7) dan ekstraksi (lama Stage 7.5) menjadi satu langkah efisien.

#### Pipeline Simplification
- **Stage Baru**: `stage_8_gemini_fulltext` (`08_gemini_fulltext_processor.py`)
- **Stage Deprecated**: `stage_7_convert_markdown` dan `stage_7_5_summarize` kini dinonaktifkan untuk menghindari redundansi.
- **Single Pass Extraction**: Semua field (naratif, kuantitatif, dan SLR-specific) diekstrak dalam satu panggilan LLM yang terpadu.

---

### Perubahan File

#### `config/config.yaml`
- Menambahkan model `google/gemini-2.0-flash-001` ke registry.
- Menambahkan konfigurasi `stage_8_gemini_fulltext`.
- Menonaktifkan stage lama di `pipeline_status`.

#### `src/pipeline/08_gemini_fulltext_processor.py` (BARU)
- Script utama untuk pemrosesan PDF native.
- Mendukung multimodal input (PDF data URI).
- Menghasilkan "Smart Markdown" dengan metadata YAML frontmatter.

#### `src/utils/helpers.py` & `src/pipeline/00_orchestrator.py`
- Memperbarui `PIPELINE_ORDER` untuk menyertakan stage baru.
- Memperbarui label laporan pipeline dan pemetaan script.
- Menghapus redundansi stage 7 & 7.5.

#### Dokumentasi
- **PANDUAN_PENGGUNAAN_SLR_AI_AGENT_SYSTEM.md**: Memperbarui alur kerja tahap 8-10.
- **QUICK_START_SLR_AI_AGENT_SYSTEM.md**: Menyederhanakan langkah-langkah bagi pengguna baru.
- **.github/agents/slr-orchestrator.agent.md**: Memperbarui basis pengetahuan agen orchestrator.

---

### Impact & Benefit

| Aspek | Sebelum (V3.4) | Sesudah (V3.5) |
|-------|---------|----------|
| **Akurasi Tabel** | Sering berantakan karena keterbatasan parser teks | **Sempurna**, karena Gemini melihat tabel secara visual |
| **Keutuhan Data** | Data statistik sering hilang saat PDF kompleks | Terjamin melalui integrasi vision-to-json |
| **Efisiensi Token** | Banyak token terbuang untuk teks sampah dari PDF | Fokus pada "Smart Markdown" yang relevan protokol |
| **Kemudahan** | Harus jalankan 2 script (Convert + Summarize) | Cukup **1 script** (Gemini Processor) |

---

## [V3.4] — 2026-05-08

### Ringkasan
Patch V3.4 menerapkan **Karpathy Principles** (Think Before Processing + Goal-Driven Execution) ke pipeline orchestrator. Setiap stage sekarang otomatis melakukan **preflight validation** sebelum run dan **success verification** sesudah selesai. Tujuan: surface ambiguity early, prevent wasted compute, dan ensure output quality.

---

### Fitur Baru

#### Principle 1: Think Before Processing — `preflight_check(stage_name)`
Validasi prerequisite dan config ambiguity **sebelum** stage dijalankan:

- **stage_3_import**: Ada file CSV/RIS di `data/input/`?
- **stage_4_screening**: `clean_corpus.json` exists? RQ + PICO filled in `config.yaml`?
- **stage_5_extraction**: Ada paper dengan `screening_decision=INCLUDE`?
- **stage_10_synthesis**: `extraction_table.json` exists? Cukup paper untuk sintesis?
- **stage_11_writing**: Output synthesis lengkap (`synthesis_narrative.md` + `themes_summary.json`)?

**Severity levels**:
- `CRITICAL` → Stage **diblokir**, perlu resolved sebelum lanjut
- `WARN` → Dicatat tapi tidak memblokir (user bisa continue)

**Output example**:
```
[PRE-FLIGHT] Screening
  [CRITICAL] CRITICAL: clean_corpus.json not found — run stage_3_import first
  [WARN]     WARN: PICO fields empty in config.yaml: comparison, outcome

[BLOCKED] Stage 'stage_4_screening' aborted — resolve CRITICAL issues above first.
```

#### Principle 4: Goal-Driven Execution — `verify_success(stage_name)`
Verifikasi output meets success criteria **setelah** stage completed:

- **stage_3_import**: Berapa paper terimport?
- **stage_4_screening**: INCLUDE/EXCLUDE balanced? Ada status MAYBE lolos? Semua EXCLUDE punya reason?
- **stage_5_extraction**: `novelty` field terisi? Ada `[REVIEW_NEEDED]`? Confidence level distribution?
- **stage_11_writing**: `manuscript.docx` terbuat?

**Severity levels**:
- `FAIL` → Stage terdeteksi broken, output tidak usable
- `WARN` → Output exist tapi ada anomali, perlu manual review
- `OK` → Output sesuai expected criteria
- `INFO` → Informational flags (e.g., papers flagged for review)

**Output example**:
```
[VERIFY] Screening
  [OK]   OK: INCLUDE=47, EXCLUDE=203 (total=250)
  [WARN] WARN: 3 EXCLUDE papers have no screening_reason
```

---

### Perubahan File

#### `src/pipeline/00_orchestrator.py` — Major Update
- **Import tambahan**: `import json` (untuk read/verify JSON outputs)
- **Fungsi baru** `preflight_check(stage_name: str) → (bool, list[str])`: implementasi Principle 1
  - Check file exist, data structure valid, config fields filled
  - Return (is_ok, list_of_issues)
- **Fungsi baru** `verify_success(stage_name: str) → (bool, list[str])`: implementasi Principle 4
  - Check output file created, data quality, field completeness
  - Return (is_ok, list_of_findings)
- **`run_stage_script()` refactored**: 
  - Panggil `preflight_check()` sebelum subprocess start — jika ada CRITICAL, block stage
  - Panggil `verify_success()` sesudah subprocess success — print findings untuk transparency
  - Formatted output dengan tag `[PRE-FLIGHT]`, `[VERIFY]`, `[CRITICAL]`, `[WARN]`, `[OK]`, `[FAIL]`, `[INFO]`

---

### Alur Eksekusi (Baru)

```
User: python src/pipeline/00_orchestrator.py run-stage stage_4_screening

↓ [PRE-FLIGHT]
  - Check clean_corpus.json exists
  - Check research_questions filled in config
  - Check PICO fields not all empty
  - Print [CRITICAL] issues (if any) → ABORT
  - Print [WARN] issues (if any) → LOG only

↓ [RUN STAGE]
  - Execute src/pipeline/04_screen.py (if preflight OK)
  - Capture returncode

↓ [VERIFY SUCCESS]
  - Check screened_results.json created
  - Check INCLUDE/EXCLUDE counts
  - Check no MAYBE status (violation detection)
  - Check all EXCLUDE have screening_reason
  - Print [OK] / [WARN] / [FAIL] findings
  - Update pipeline_state.json to "completed"

↓ Return exitcode
```

---

### Impact & Benefit

| Aspek | Sebelum | Sesudah |
|-------|---------|----------|
| **Error Detection** | Kerusakan output baru terdeteksi di tahap selanjutnya (terlambat) | Terdeteksi langsung, dengan jelas, sebelum compute stage berikutnya |
| **Config Validation** | Ambiguous config baru ketahuan saat LLM fail | Config ambigu flagged di preflight (seperti empty PICO) |
| **Data Quality Check** | Manual inspection eksternal perlu | Auto-check: counting, field presence, status violation |
| **User Experience** | Vague error messages | Clear structured output dengan severity level |
| **Pipeline Restart** | Sulit tahu stage mana yang truly OK | Explicit verification per stage — resume lebih confident |

---

## [V3.3.1] — 2026-05-08

### Ringkasan
Patch V3.3.1 memperbaiki **text truncation bug** yang menyebabkan 34/34 paper kehilangan data kuantitatif (`effect_size`, `p_values`, `mean_values`, dll.), dan menambahkan **CrossRef API enrichment** untuk mengisi metadata (`pages`, `publisher`, `country`, `cites`) dari DOI.

---

### Bug Fixes

| # | File | Bug | Fix |
|---|------|-----|-----|
| 1 | `09_summarize_fulltext.py` | `max_chars=28000` terlalu kecil — Results/Discussion/Limitations terpotong | Naikkan ke `60000` di `build_smart_context()` dan fallback `build_fulltext_extraction_prompt()` |
| 2 | `09.1_extract_from_fulltext.py` | `full_text[:15000]` terlalu kecil — semua statistical data kosong | Naikkan ke `60000` |

### Fitur Baru

#### `src/pipeline/06_enrich_metadata.py` — CrossRef Metadata Enrichment (BARU)
- Pipeline stage baru (`stage_5_5_enrich_metadata`) yang mengisi field kosong dari CrossRef API:
  - `pages` — range halaman
  - `publisher` — nama publisher
  - `country` — negara dari author affiliation
  - `cites` — jumlah citation (is-referenced-by-count)
- **Non-destructive**: hanya mengisi field yang kosong/null
- **Audit trail**: log enrichment disimpan ke `data/output/extracted/crossref_enrichment_log.json`
- Delay 0.5s antar request untuk menghindari rate limit
- Dapat dijalankan manual atau via orchestrator: `python src/pipeline/00_orchestrator.py --run stage_5_5_enrich_metadata`

#### `src/pipeline/00_orchestrator.py` — Pipeline Update
- Stage baru `stage_5_5_enrich_metadata` terdaftar di `STAGE_SCRIPTS`, `PIPELINE_ORDER`, dan `STAGE_NAMES`
- Posisi: setelah Stage 5 (Initial Extraction) dan sebelum Stage 6 (Download PDFs)

---

## [V3.3] — 2026-05-08

### Ringkasan
Rilis V3.3 adalah **refaktor arsitektur pipeline** yang menggabungkan Stage 7.5 (Summarize Full-Text) dan Stage 8 (Extract from Full-Text) menjadi satu stage terpadu. Tujuan utama: eliminasi pemrosesan duplikat, memperluas cakupan ekstraksi ke semua field `extraction_template.json`, dan menjadikan `extraction_table.json` sebagai **single source of truth** untuk seluruh data paper.

---

### Perubahan Arsitektur

#### Sebelum V3.3
```
Stage 7.5  →  fulltext_summaries.json  (hanya 10 field naratif)
                       ↓
Stage 8    →  extraction_table.json    (primary extractor, baca dari summaries)
```

#### Sesudah V3.3
```
Stage 7.5  →  extraction_table.json   (semua field, langsung update)
               fulltext_summaries.json (backward-compat, opsional)
Stage 8    →  [DEPRECATED — tidak ada di pipeline]
```

---

### Perubahan File

#### `src/pipeline/09_summarize_fulltext.py` — Major Refactor
- **Fungsi baru** `build_fulltext_extraction_prompt()`: prompt LLM yang mengekstrak **semua 30+ field** sekaligus dalam satu call, termasuk:
  - Semua field kuantitatif: `effect_size`, `mean_values`, `standard_deviation`, `standard_error`, `confidence_intervals`, `p_values`, `statistical_data`
  - Semua field naratif: `objective`, `methodology`, `results`, `conclusions`, `novelty`, `limitations`, `future_work`, dll.
  - Field SLR-spesifik: `rq_contributions`, `tags`, `study_design`, `research_gap`, `problem_statement`, `applications`, `challenges`
- **Fungsi baru** `extract_and_summarize_with_llm()`: menggantikan `summarize_with_llm()` lama. LLM call menggunakan `max_tokens: 8000` dan truncate full-text hingga 28.000 karakter
- **Fungsi baru** `_build_summary_subset()`: membangun subset naratif untuk `fulltext_summaries.json` (backward-compat)
- **Fungsi baru** `process_all_markdown_files()`: menggantikan `summarize_markdown_files()`, sekarang return `(stats, summaries, updated_extraction_lookup)`
- **`process_single_markdown()` diperbarui**: hasil LLM langsung di-merge ke `metadata` paper, update `extraction_table.json` in-place. Field yang sudah terisi dari abstract tidak ditimpa kecuali full text memberikan data lebih akurat
- **`save_summary_as_markdown()` diperluas**: dari 10 field → semua field termasuk statistik dan SLR classification (4 section: Narrative Summary, Sample & Variables, Statistical Data, SLR Classification)
- **`main()` diperbarui**: menulis `extraction_table.json` sebagai primary output, `fulltext_summaries.json` sebagai secondary/backward-compat
- **Bug fix**: Hapus `input()` blocking yang menyebabkan deadlock di dalam `ThreadPoolExecutor`
- **Bug fix**: `except` block LLM error sekarang fallback ke `summarize_with_fallback()` bukan return error dict kosong

#### `src/pipeline/09.1_extract_from_fulltext.py` — Deprecated
- Docstring diperbarui dengan notice **[DEPRECATED — MERGED INTO Stage 7.5]**
- `main()` sekarang default menampilkan redirect message ke Stage 7.5 dan `return 0`
- Tetap dapat dijalankan dengan flag `--validate-only` untuk re-process paper yang masih `[REVIEW_NEEDED]` atau `extraction_confidence < 5`

#### `src/pipeline/10_sync_to_wiki.py` — Minor Update
- `fulltext_summaries.json` sekarang **opsional** — jika tidak ada, Wiki Sync tetap berjalan dari `extraction_table.json`
- Pesan `[WARN]` untuk file tidak ditemukan diubah ke `[INFO]` (sudah by design)
- Docstring header diperbarui

#### `src/main.py` — Pipeline Update
- **Stage 8 dihapus** dari `STAGES` list — tidak lagi dijalankan otomatis
- Urutan pipeline baru:
  ```
  Stage 3  → Import & Deduplicate
  Stage 4  → Screening
  Stage 5  → Initial Extraction (Abstract)
  Stage 6  → Download PDFs
  Stage 7  → Convert PDF → Markdown
  Stage 7.5→ Full-Text Extraction & Summarize (Merged) ← BARU
  Stage 8 → Wiki Sync
  Stage 9  → Quality Assessment
  Stage 10 → Synthesis & Meta-Analysis
  Stage 11 → Write Manuscript
  Stage 12 → Audit Manuscript
  ```

#### `config/config.yaml` — Config Update
- Ditambah `stage_max_tokens.stage_7_5_summarize: 8000` (naik dari 4000 untuk cover semua field)
- `stage_8_extract_fulltext` di-set `false` (deprecated)
- `pipeline_status`: `stage_7_5_summarize` menggantikan `stage_8_summarize_fulltext` dan `stage_8_extract_fulltext`
- `stage_8_wiki_sync: true` di per-stage LLM control

#### `.github/agents/slr-orchestrator.agent.md` — Sinkronisasi Pipeline
- **Urutan Pipeline** diperbarui: Stage 8 dihapus, Stage 7.5 kini bertuliskan *"Full-Text Extraction & Summarize (Merged)"*
- **Status Check** diperbarui: `fulltext_summaries.json` ditandai opsional; indikator Stage 7.5 selesai adalah `extraction_table.json (updated with fulltext, all fields)`
- **Data Flow Stage 7.5** diperbarui: penjelasan lengkap merged stage, rule tidak overwrite field yang sudah terisi, fallback ke regex jika LLM tidak tersedia, deprecation notice Stage 8
- Section **Stage 8 Data Flow** dihapus (tidak ada lagi)

#### `.github/agents/data-extractor.agent.md` — Sinkronisasi Tahap 2
- Deskripsi agent diperbarui: Tahap 2 kini *"Full-Text Extraction & Summarize (Stage 7.5 Merged)"*
- Instruksi **Output Tahap 2** diperbarui: `python src/pipeline/09_summarize_fulltext.py` (bukan `09.1_extract_from_fulltext.py`)
- Output tambahan: `summaries_markdown/*.md` (human-readable per paper)
- Instruksi lanjut setelah Tahap 2: Stage 8 Wiki Sync (bukan langsung Stage 10)
- Deprecation notice untuk `09.1_extract_from_fulltext.py`

#### `src/utils/helpers.py` — State Tracking Update
- Default `pipeline_state` diperbarui:
  - Dihapus: `stage_8_summarize_fulltext`, `stage_8_extract_fulltext`
  - Ditambah: `stage_7_5_summarize`
- `get_pipeline_report()` `stage_names` dict diperbarui:
  - `"stage_7_5_summarize": "Full-Text Extract & Summarize"`
  - Dihapus entri Stage 8 lama

---

### Bug Fixes

| # | File | Bug | Fix |
|---|------|-----|-----|
| 1 | `main.py` | Stage 8 DEPRECATED masih dijalankan otomatis di pipeline loop | Dihapus dari `STAGES` |
| 2 | `09_summarize_fulltext.py` | `input()` di dalam `ThreadPoolExecutor` — deadlock/hang | Dihapus, auto-fallback ke regex |
| 3 | `09_summarize_fulltext.py` | LLM return broken JSON → paper di-skip tanpa data | `except` block sekarang `return summarize_with_fallback()` |
| 4 | `helpers.py` | Default pipeline state tidak ada `stage_7_5_summarize` | State diperbarui, nama lama dihapus |
| 5 | `10_sync_to_wiki.py` | `[WARN]` untuk file yang by design opsional | Diubah ke `[INFO]` |

---

### LLM Extraction Schema (V3.3)

Stage 7.5 sekarang mengekstrak field berikut dalam **satu LLM call**:

**Narrative Fields**
`tldr` · `summarized_abstract` · `summarized_introduction` · `objective` · `methodology` · `results` · `conclusions` · `novelty` · `limitations` · `future_work` · `key_findings` · `findings` · `contributions` · `research_gap` · `problem_statement` · `literature_survey` · `challenges` · `applications`

**Study Design & Sample**
`study_design` · `sample_size` · `sample_description` · `population_sample` · `dataset`

**Variables & Methods**
`technology_used` · `independent_variables` · `dependent_variables` · `outcomes_measured` · `methods_used` · `statistical_methods`

**Quantitative / Statistical**
`effect_size` · `mean_values` · `standard_deviation` · `standard_error` · `confidence_intervals` · `p_values` · `statistical_data`

**SLR Classification**
`tags` · `rq_contributions` · `extraction_notes`

---

### Konfigurasi yang Direkomendasikan

```yaml
# config/config.yaml
llm:
  use_external_llm: true
  base_url: 'https://openrouter.ai/api/v1'
  model: 'qwen/qwen3.6-plus'
  stage_max_tokens:
    stage_7_5_summarize: 8000
```

```env
# .env
USE_EXTERNAL_LLM=true
LLM_API_KEY=sk-or-v1-xxxxxxxx
LLM_BASE_URL=https://openrouter.ai/api/v1
LLM_MODEL=qwen/qwen3.6-plus
LLM_AUTO_CONFIRM=1
```

---

### Catatan Migrasi dari V3.2

Jika Anda sudah memiliki `pipeline_state.json` dari versi sebelumnya:
1. Hapus `data/output/pipeline_state.json` — akan dibuat ulang otomatis dengan schema baru
2. `data/output/extracted/fulltext_summaries.json` tetap valid dan dibaca oleh Wiki Sync
3. `data/output/extracted/extraction_table.json` tidak perlu dihapus — Stage 7.5 akan update in-place (tidak overwrite field yang sudah terisi)
4. Stage 8 tidak perlu dijalankan lagi

---

## [V3.2] — (Sebelum sesi ini)

Versi sebelum penggabungan Stage 7.5 dan Stage 8. Pipeline masih memiliki dua stage terpisah:
- Stage 7.5: `09_summarize_fulltext.py` → 10 field naratif → `fulltext_summaries.json`
- Stage 8: `09.1_extract_from_fulltext.py` → semua field → `extraction_table.json`
