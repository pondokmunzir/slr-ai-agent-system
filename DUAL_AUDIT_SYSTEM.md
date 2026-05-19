# Dual Audit System untuk Manuscript AI Detection

## Overview

Sistem ini mengimplementasikan **dual audit** otomatis untuk memastikan manuscript.docx sudah lolos audit AI sebelum submission ke jurnal.

### Audit Layers

```
┌─────────────────────────────────────────────────────────┐
│ STAGE 11: WRITE MANUSCRIPT (AI Scaffold)                │
│ - Generate draft manuscript from synthesis results      │
│ - Output: draft_manuscript.md, manuscript.docx          │
│ - Every paragraph marked [AUTHOR MUST REWRITE]          │
│ - Script: 14_write_manuscript.py                        │
└─────────────────────────────────────────────────────────┘
                          ↓
┌─────────────────────────────────────────────────────────┐
│ STAGE 12: AUDIT MANUSCRIPT (Python Engine) ⭐           │
│ - Substantive quality audit on markdown draft           │
│ - Run: python src/pipeline/15_audit_manuscript.py       │
│ - Cek PRISMA 2020 compliance (detail)                   │
│ - Validasi meta-analysis vs. vote-counting              │
│ - Review citation narrative integration                 │
│ - Assess limitation magnitude & consequence             │
│ - Novelty clarity check                                 │
│ - Generate AUDIT_REPORT.md (formal)                     │
│ - Audit Score: 0-100                                    │
│ - Output: AUDIT_REPORT.md, AUDIT_SUMMARY.txt, .json     │
└─────────────────────────────────────────────────────────┘
                          ↓
┌─────────────────────────────────────────────────────────┐
│ HUMAN REWRITING (Manual Per Audit Findings)             │
│ - Human author rewrites per audit guidance              │
│ - Improve PRISMA compliance items                       │
│ - Strengthen Discussion & Limitations sections          │
│ - Integrate citations narratively                       │
│ - Follow REWRITING_GUIDE.md rules                       │
│ - Time: 3-5 days (typical for Q1 journal)               │
└─────────────────────────────────────────────────────────┘
                          ↓
┌─────────────────────────────────────────────────────────┐
│ OPTIONAL: AI PATTERN DETECTION (Post-Rewrite)           │
│ - Run pattern detection on final human-written text     │
│ - Detect 29+ AI-generated writing patterns              │
│ - Generate AI_PATTERN_REPORT.md                         │
│ - Ensure Turnitin readiness (<5% AI, <20% similarity)   │
│ - Purpose: Ensure human-style writing before submit     │
└─────────────────────────────────────────────────────────┘
                          ↓
┌─────────────────────────────────────────────────────────┐
│ FINAL: SUBMIT TO JOURNAL                                │
│ - Convert to final format (Word, PDF)                   │
│ - Verify all references and citations                   │
│ - Submit to target journal                              │
└─────────────────────────────────────────────────────────┘
```

---

## Output Files

### Stage 11: Write Manuscript
1. **draft_manuscript.md** - Markdown draft (AI scaffold)
   - Setiap paragraph ditandai `[AUTHOR MUST REWRITE]`
   - Sudah structured per section (Intro, Methods, Results, Discussion, Conclusion)

2. **manuscript.docx** - DOCX draft (formatted)
   - Proper heading styles
   - Ready untuk human rewrite
   
3. **REWRITING_GUIDE.md** - Step-by-step rewriting guide
   - 7 langkah dari baca draft sampai submission
   - Pola AI yang harus dihindari
   - Teknik human-style writing

### Stage 12: Audit Manuscript ⭐ (BARU)
1. **AUDIT_REPORT.md** - Formal substantive quality report
   - Audit Score (0-100) based on PRISMA 2020 compliance, meta-analysis validity, citation integration
   - Detailed findings per section (Methods, Results, Discussion, Limitations)
   - Status: ✅ PASSED / ⚠️ NEEDS REVIEW / ❌ NEEDS REVISION
   - Actionable recommendations for human rewriting
   - Use as guidance for manuscript improvement
   
2. **AUDIT_SUMMARY.txt** - Quick reference summary
   - High-level scores
   - Key findings at a glance
   
3. **audit_report.json** - Machine-readable format
   - Detailed metrics in JSON
   - For tool integration

### After Human Rewriting (Optional)
4. **AI_PATTERN_REPORT.md** - AI pattern detection report
   - Pattern detection on final human-written text
   - Grouped by severity: High, Medium, Low
   - Use before final Turnitin check
   - Ensures human-style writing

### Final Submission
5. **manuscript_final.docx** - Production-ready document
   - All rewriting complete
   - All references verified
   - Turnitin check passed (0% AI, <20% similarity)
   - Ready for journal submission

---

## Audit Criteria (Stage 12)

---

---

## Audit Focus Areas (Stage 12 - Substantive Quality)

### 🔴 High Priority (Must Address)
**PRISMA 2020 Compliance**
- Missing or incomplete Search Strategy reproducibility (keywords, dates, databases)
- Inconsistent PRISMA Flow Diagram numbers vs. Results text
- Missing Risk of Bias assessment integration into Discussion
- Inclusion/Exclusion criteria not clearly listed

**Meta-Analysis Validity**
- Claiming "meta-analysis" when only vote-counting (e.g., "60% of studies showed...")
- Missing forest plots, effect size confidence intervals, or heterogeneity (I²) reporting
- Pooled estimates without proper statistical justification

**Limitations Reporting**
- Limitations listed without impact magnitude (e.g., "Study was limited to published papers" — impact: medium, lost ~30% of evidence base)
- No consequence discussion (e.g., "Small sample size in 2 studies → may underestimate effect")

**Novelty Clarity**
- Contribution unclear or overstated (e.g., "This is the first systematic review..." without evidence)
- No differentiation from prior SLRs in the field

### 🟡 Medium Priority (Should Address)
**Citation Integration**
- Citations appended without narrative integration (e.g., "Previous studies show X (Smith, 2020; Jones, 2021)." should be "Smith (2020) demonstrated X, while Jones (2021) found...")
- Results section repeating findings without interpretation
- Missing back-reference to data in extraction table

**Discussion Depth**
- Discussion merely summarizes results instead of interpreting them
- No synthesis across studies (e.g., why did some studies show opposite results?)
- Missing comparison to theoretical framework or prior models

### 🟢 Low Priority (Optional)
**Writing Style**
- Minor grammatical issues not affecting meaning
- Formatting inconsistencies (spacing, heading levels)
- Citation format variations (APA style consistency)

---

## Workflow untuk Penulis (Substantive Audit Focus)

### Step 1: Review Audit Report
```bash
1. Baca AUDIT_REPORT.md (formal)
   - Lihat Audit Score (0-100)
   - Status: ✅ PASSED / ⚠️ NEEDS REVIEW / ❌ NEEDS REVISION
   - Baca "High Priority" section — ini yang wajib diperbaiki
   
2. Baca AUDIT_SUMMARY.txt (quick ref)
   - Overview temuan utama
   - Quick scores per section

3. Cross-reference dengan data
   - Buka extraction_table.json
   - Buka synthesis_narrative.md
   - Verifikasi claims dengan data asli
```

### Step 2: Identify & Prioritize Fixes
```
Priority 1 (MUST FIX):
- ✗ PRISMA Flow Diagram numbers inconsistent → fix with actual counts
- ✗ Meta-analysis claimed but only vote-counting → downgrade to "systematic review with narrative synthesis"
- ✗ Limitations without impact → add magnitude (e.g., "Small sample in 3 studies (~15% of evidence)")
- ✗ Novelty unclear → state clearly vs. which prior SLRs

Priority 2 (SHOULD FIX):
- Citations not integrated narratively → restructure sentences
- Discussion only summarizes → add interpretation & comparison across studies
- Results not back-referenced to tables → add in-text citations to extraction table

Priority 3 (OPTIONAL):
- Minor grammar/style → polish after priority items done
```

### Step 3: Rewrite Manuscript Per Audit
```
1. Read REWRITING_GUIDE.md (7 steps)
2. For each "High Priority" finding:
   - Locate in draft_manuscript.md
   - Rewrite per guidance
   - Verify with data (extraction table, syntheses)
3. For each "Medium Priority" finding:
   - Improve narrative flow
   - Integrate citations
   - Strengthen interpretation
4. Maintain academic third-person tone throughout
```

### Step 4: Re-audit (Optional)
```
1. Save final_manuscript.md
2. Run: python src/pipeline/15_audit_manuscript.py
3. Compare Audit Score (target: 90+)
4. If score improved, ready for Turnitin check
```

---

## Legacy: AI Patterns Detected (29+)

### Phrase-Level Patterns
- "It is worth noting that..."
- "In recent years, there has been..."
- "This paper aims to..."
- "Furthermore, it is important to..."
- "The findings suggest that..."
- "In conclusion, this study..."
- "It is evident that..."
- "Delve into..."
- "The landscape of..."
- "Shed light on..."
- "It can be seen that..."
- "It should be noted..."
- "A number of studies..."
- "Many researchers have found..."
- "Consistently demonstrated..."
- "Overall it can be said..."
- "The present study..."

### Structure-Level Patterns
- Passive voice overuse
- Repetitive sentence structure (3+ same length)
- Generic opening sentences
- Hedging overuse

### Vocabulary-Level Patterns
- Vague quantifiers: "most", "several", "numerous"
- Repetitive words: "demonstrated", "evidence", "findings"
- Generic transitions: "Furthermore", "However"

---

## Integration dengan Pipeline

### Automatic Execution
```python
# Dijalankan otomatis saat `python src/pipeline/14_write_manuscript.py`

1. Generate manuscript (AI)
2. AUDIT 1: Pre-DOCX Markdown Audit
   - Deteksi patterns
   - Generate AI_PATTERN_REPORT.md
3. Convert ke DOCX (Pandoc)
4. AUDIT 2: Post-DOCX Final Audit
   - Extract text dari DOCX
   - Deteksi patterns
   - Generate AUDIT_REPORT.md
5. Generate REWRITING_GUIDE.md
```

### Output Summary
```
[AUDIT SUMMARY]
  Pre-DOCX Audit:  Audit Score 75/100 - ❌ FAILED
  Post-DOCX Audit: Audit Score 75/100 - ❌ FAILED

[PENTING] Draft ini adalah scaffold. JANGAN submit langsung ke jurnal.
          1. Baca AUDIT_REPORT.md untuk status AI audit formal
          2. Baca AI_PATTERN_REPORT.md untuk detail pola AI
          3. Baca REWRITING_GUIDE.md dan tulis ulang
          4. Target: Turnitin AI Writing Indicator = 0%
```

---

## Turnitin Readiness Estimate

Audit system memberikan estimate Turnitin AI Writing Indicator berdasarkan audit score:

```
Audit Score 90-100 → Estimated Turnitin AI: 0-5%
Audit Score 70-89  → Estimated Turnitin AI: 5-15%
Audit Score 50-69  → Estimated Turnitin AI: 15-30%
Audit Score <50    → Estimated Turnitin AI: >30%
```

**Catatan:** Ini adalah estimate berdasarkan pattern detection. Actual Turnitin score bisa berbeda tergantung:
- Kualitas human rewriting
- Specific data dan citations
- Vocabulary variation
- Sentence structure variation

---

## Best Practices

### ✅ DO
- Baca AUDIT_REPORT.md sebelum mulai rewriting
- Fokus pada high-severity patterns dulu
- Variasi sentence length dan structure
- Gunakan specific numbers dan citations
- Baca manuscript aloud untuk check naturalness
- Re-audit setelah rewriting untuk verify improvement

### ❌ DON'T
- Submit DOCX langsung tanpa human rewriting
- Ignore high-severity patterns
- Copy-paste dari draft AI
- Gunakan vague quantifiers ("most", "many")
- Tulis 3+ sentences dengan struktur sama
- Paraphrase per-kalimat (tulis ulang dari nol)

---

## Troubleshooting

### Audit Score Terlalu Rendah
**Penyebab:** Banyak high-severity patterns terdeteksi
**Solusi:**
1. Fokus pada high-severity patterns (lihat AUDIT_REPORT.md)
2. Ganti dengan suggested alternatives
3. Re-audit untuk verify improvement

### DOCX Audit Gagal tapi Markdown Audit Passed
**Penyebab:** Kemungkinan issue dengan DOCX conversion
**Solusi:**
1. Check DOCX formatting
2. Verifikasi Pandoc installation
3. Re-run script

### Pattern Terdeteksi tapi Tidak Terlihat di Text
**Penyebab:** Pattern matching case-insensitive atau partial match
**Solusi:**
1. Search di manuscript untuk pattern
2. Ganti dengan suggested alternative
3. Verify dengan re-audit

---

## Technical Details

### Pattern Detection Engine
- **File:** `src/utils/ai_pattern_detector.py`
- **Class:** `AIPatternDetector`
- **Methods:**
  - `detect_patterns(text)` — Deteksi semua patterns
  - `get_summary_stats(text)` — Get summary statistics
  - `audit_manuscript(text, threshold)` — Run formal audit
  - `generate_audit_report(text, threshold, filename)` — Generate report
  - `generate_report(text)` — Generate detailed pattern report

### Integration Points
- **Pre-DOCX:** `generate_manuscript_per_section()` + `main()`
- **Post-DOCX:** `main()` after `markdown_to_docx()`
- **Reports:** Saved to `data/output/`

---

## Version History

- **v1.0** (May 2, 2026)
  - Implemented dual audit system
  - 29+ AI pattern detection
  - Formal audit reports with Turnitin estimate
  - Integration dengan pipeline

---

## Contact & Support

Untuk questions atau improvements, lihat:
- `src/utils/ai_pattern_detector.py` — Pattern detection logic
- `src/pipeline/14_write_manuscript.py` — Pipeline integration
- `REWRITING_GUIDE.md` — Human rewriting guide
