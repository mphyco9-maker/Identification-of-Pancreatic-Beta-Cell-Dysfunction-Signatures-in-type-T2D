# Pancreatic Beta-Cell Dysfunction in Type 2 Diabetes — Feasibility Checkpoint

**Project title:** Identification of Pancreatic Beta-Cell Dysfunction Signatures in Type 2 Diabetes Using Single-Cell Transcriptomic Analysis  
**Scope:** Seven-day dataset verification and reproducible exploratory workflow; **no differential-expression, pathway, or model-training claims** are made at this checkpoint.  
**Datasets:** GSE81608 (Xin et al., 2016) and GSE86469 (Lawlor et al., 2017). These datasets are analyzed **separately**.

## 1. Research purpose

Determine whether the available processed single-cell expression matrices and documented metadata support a defensible **donor-aware** comparison of pancreatic beta cells between non-diabetic (ND) and type 2 diabetes (T2D) groups. Specifically, verify donor mapping and disease labels, count annotated beta cells per donor, document expression units and missing metadata, and check original cell annotations using INS, GCG, SST and PPY expression. Squair et al. (2021) motivates avoiding pseudoreplication in any future differential-expression analysis.

## 2. Data sources and processed files

| GEO accession | Original study | Original deposited expression file | Input CSV used by notebook | Expression values |
|---|---|---|---|---|
| [GSE81608](https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE81608) | Xin et al. (2016) | `GSE81608_human_islets_rpkm.txt.gz` | `GSE81608_expression_RPKM.csv` | **RPKM** (already normalized; not raw counts) |
| [GSE86469](https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE86469) | Lawlor et al. (2017) | `GSE86469_GEO.islet.single.cell.processed.data.RSEM.raw.expected.counts.csv.gz` | `GSE86469_expression_RSEM_expected_counts.csv` | **RSEM expected counts** (not necessarily integer raw read counts) |

GEO Series Matrix metadata extracted to:

- `GSE81608_GEO_sample_metadata.csv`
- `GSE86469_GEO_sample_metadata.csv`

Official source URLs and field-by-field provenance are recorded in `dataset_inventory_final.csv`, `metadata_field_provenance.csv`, and `cell_manifest_with_provenance.csv`. The original compressed expression files were **not** hashed independently in this review; the recorded SHA256 values refer to the **converted expression CSVs** and were calculated in the user's Colab environment.

### Converted CSV integrity checks (SHA256)

```text
GSE81608_expression_RPKM.csv
8699e29653c0fa223b5d2ba577900a56a51fa857e878f3575cefeeaf6b244f57

GSE86469_expression_RSEM_expected_counts.csv
61aed6a8f8905f047232ef447f40e42623e6b5b4a2bc6d46e5914ac91bb73f43
```

Recalculate on the files used for reproduction; any difference indicates that the file bytes do not match the versions originally analyzed.

## 3. Recommended submission layout

```text
Thesis_Final_Submission/
├── README.md
├── requirements.txt
├── report/
│   ├── Findings_and_Feasibility_Report.docx
│   └── Supervisor_Three_Paper_Comparison.pdf
├── data/
│   ├── GSE81608_expression_RPKM.csv
│   ├── GSE86469_expression_RSEM_expected_counts.csv
│   ├── GSE81608_GEO_sample_metadata.csv
│   └── GSE86469_GEO_sample_metadata.csv
├── documentation/
│   ├── dataset_inventory_final.csv
│   ├── metadata_field_provenance.csv
│   ├── cell_manifest_with_provenance.csv
│   ├── donor_summary.csv
│   ├── matching_audit.csv
│   ├── expression_qc_summary.csv
│   ├── documentation_sources.md
│   └── verification_status_and_open_items.csv
├── notebooks/
│   └── Thesis_Exploratory_LOW_MEMORY_v3_EXECUTED.ipynb
└── figures/
    ├── beta_cells_per_donor.png
    ├── GSE81608_expression_distribution.png
    ├── GSE86469_expression_distribution.png
    ├── GSE81608_marker_check.png
    └── GSE86469_marker_check.png
```

This layout is a **submission organization suggestion**, not the working paths expected by the current notebook. In Colab, **upload the four required CSVs directly into `/content`**, because the notebook looks there. Keep datasets separate and preserve their original input filenames. If uploading the large expression CSVs to shared storage is not practical, include verified GEO links and clear retrieval instructions; do not claim the files are attached when they are not.

## 4. Software environment

These exact versions were recorded by `software_versions.json` from the user's successful LOW MEMORY v3 Colab run:

```text
Python      3.13.18
numpy       2.4.6
pandas      2.2.3
matplotlib  3.10.0
```

The `google.colab` module is provided by Google Colab and is **not** listed as a pip dependency.

For a compatible Python environment, use:

```bash
python -m pip install -r requirements.txt
```

Colab usually comes with the required libraries; running its pre-installed environment may have different package versions. Record the versions actually used in any fresh rerun rather than asserting they are unchanged.

## 5. Reproduce the exploratory analysis in Google Colab

1. Open **https://colab.research.google.com/**. Upload `Thesis_Exploratory_LOW_MEMORY_v3_EXECUTED.ipynb` (or the original `Thesis_Exploratory_LOW_MEMORY_v3.ipynb`). Make a copy in Drive if editing.
2. Start a **fresh runtime** (`Runtime > Disconnect and delete runtime`, then reconnect), so the rerun does not reuse earlier uploaded files or variables.
3. Run the first upload code cell: upload the two **expression** files, one at a time if necessary. If needed, run that upload cell again for the second file.
4. Run the second upload code cell: upload the two **metadata** files, likewise one at a time if necessary.
5. Ensure these **exact four filenames** appear in `/content`:
   - `GSE81608_expression_RPKM.csv`
   - `GSE86469_expression_RSEM_expected_counts.csv`
   - `GSE81608_GEO_sample_metadata.csv`
   - `GSE86469_GEO_sample_metadata.csv`
6. After the uploads, execute the remaining code cells **top to bottom**. Do **not** use `Runtime > Run all` after uploading if it will repeat file-upload prompts. The notebook streams expression data **one gene at a time** to limit RAM consumption.
7. Inspect `matching_audit.csv`: `header_match_verified` should be `True` for **both** datasets, and `unmatched_expression`, `unmatched_metadata`, duplicate metadata IDs and duplicate expression IDs should be zero. If it fails, inspect the diagnostic mismatch CSVs before proceeding.
8. Inspect `expression_qc_summary.csv`, `donor_summary.csv`, marker plots and warning messages. Confirm they agree with the audit values below. Save/download `Thesis_LOW_MEMORY_Results.zip`.
9. Save a **new executed notebook with visible outputs** using `File > Download > Download .ipynb`. Place it in the submission's `notebooks/` folder. Archive the output ZIP or extract its tables/figures into the matching folders.
10. For byte-level input integrity, compute SHA256 on the two converted expression CSVs and compare with §2. Example in Colab:

```python
from pathlib import Path
import hashlib
for name in ["GSE81608_expression_RPKM.csv", "GSE86469_expression_RSEM_expected_counts.csv"]:
    p = Path('/content') / name
    h = hashlib.sha256()
    with p.open('rb') as f:
        for block in iter(lambda: f.read(1024 * 1024), b''):
            h.update(block)
    print(name, h.hexdigest())
```

**A rerun is only fully validated when** the code completes without errors and the matching, donor summary and QC outputs agree. Merely saving a notebook file is not equivalent to a successful reproducibility test.

## 6. Expected verified checkpoint outputs

The following values come from the previously executed notebook and its saved result archive, **not from a new independent rerun**:

| Measure | GSE81608 | GSE86469 |
|---|---:|---:|
| Gene/feature rows | 39,851 | 26,616 |
| Expression cells / matched metadata rows | 1,600 / 1,600 | 638 / 638 |
| ND donors / T2D donors | 12 / 6 | 5 / 3 |
| ND cells / T2D cells | 651 / 949 | 380 / 258 |
| Annotated beta cells: ND / T2D | 207 / 296 | 168 / 96 |
| Total annotated beta cells | 503 | 264 |
| Missing/nonfinite or negative expression entries | 0 | 0 |
| Duplicate gene identifiers | 0 | 0 |
| Zero-expression fraction | 0.8563028845 | 0.6874225605 |
| Marker genes located in expression rows | INS, GCG, SST, PPY | INS, GCG, SST, PPY |

Overall: **2,238 matched cells, 26 donors, 767 annotated beta cells**. Original sample annotations (not a manually chosen INS threshold) determine beta-cell counts. All donor-level counts should remain dataset-specific; do not treat cells within the same donor as independent biological replicates.

Files produced by the notebook (within `/content/thesis_low_memory_results/` and ZIP): `matching_audit.csv`, `expression_qc_summary.csv`, `cell_manifest.csv`, `donor_summary.csv`, `marker_expression_long.csv`, `software_versions.json`, `README.txt`, and five PNG plots (`beta_cells_per_donor.png`, both expression distributions, both marker checks).

**Distribution-plot sampling:** the notebook uses up to **32 evenly spaced cells per gene** for expression histograms to avoid memory overload; the missingness/negative/zero checks scan **all** provided expression values. This distinction must be mentioned when interpreting plots.

## 7. Metadata provenance and mapping rules

- Each manifest row represents one **cell**; preserve the source GEO sample accession and original sample title in the provenance-enriched manifest.
- For GSE81608, the GEO title `Pancreatic islet cell sample N` is mapped to the expression header `Sample_N`; for GSE86469, the original sample title is matched directly. These transformations were checked for uniqueness and complete set agreement.
- Donor IDs, original disease labels and original cell-type annotations are traced to GEO sample characteristics in `metadata_field_provenance.csv` and `cell_manifest_with_provenance.csv`.
- GSE81608 **BMI and batch** are unavailable in the supplied sample metadata; GSE86469 **batch** is unavailable. Missing fields must stay missing; do not infer them from filenames.
- GSE86469 contains **21** cells with `None/Other` original annotation. These are not silently reclassified or dropped.

## 8. QC limitations, published-count discrepancies and interpretation

- **Different expression scales:** GSE81608 provides RPKM (normalized), while GSE86469 provides RSEM expected counts. These values are not directly interchangeable. RPKM must not be used as if it were raw counts; expected counts do not reproduce read-level quality metrics.
- **Annotation check, not beta-cell selection:** INS, GCG, SST and PPY checks support interpretation of existing labels, but are not a new definition of cell identity. The source gene-ID mappings should still be cross-checked with original annotation documentation before biological interpretation.
- **Few beta cells in some donors:** GSE81608 has `Non T2D 3` (10), `Non T2D 6` (7), `Non T2D 9` (7); GSE86469 has `ACEK420A` (10). Report these donors **before** any exclusion decision. Other donors have highly unequal contributions.
- **Xin count reconciliation:** GSE81608 has 1,600 GEO profiles but the Xin publication reports 1,492 analyzed cells. The precise sample-level reason for the **108-profile difference remains unresolved**. Do not assert that all were excluded for failed QC without evidence.
- **Lawlor count reconciliation:** GSE86469 has 638 GEO processed profiles, versus 617 specifically classified islet cells in the publication/annotation summary. The metadata include 21 `None/Other` cells. The published filtering path also documents QC and multiple-marker exclusions upstream; see `documentation_sources.md` and the count-reconciliation CSV.
- **Donor-aware inference:** 500 cells obtained from one donor are **not** 500 independent biological samples. In future T2D-versus-ND comparisons, donors/biological replicates must be handled appropriately (e.g. pseudobulk after confirming adequate per-donor cells). No differential expression or pathway analysis is part of this checkpoint.
- Original gene annotation crosswalk and some filtering provenance details have **not** been completely independently verified. They are listed as open items in `verification_status_and_open_items.csv`, rather than filled in by speculation.

## 9. Core literature

1. Xin et al. (2016). *RNA Sequencing of Single Human Islet Cells Reveals Type 2 Diabetes Genes*. Cell Metabolism. Dataset [GSE81608](https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE81608).
2. Lawlor et al. (2017). *Single-cell transcriptomes identify human islet cell signatures and reveal cell-type-specific expression changes in type 2 diabetes*. Genome Research. Dataset [GSE86469](https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE86469).
3. Squair et al. (2021). *Confronting false discoveries in single-cell differential expression*. Nature Communications.

See `Supervisor_Three_Paper_Comparison.pdf` and `Findings_and_Feasibility_Report.docx` for the written study comparison and checkpoint interpretation.

## 10. Submission / team handoff checklist

- [ ] Place the correct two expression and two metadata input CSVs in the shared folder or provide reproducible download sources.
- [ ] Include `dataset_inventory_final.csv` and `cell_manifest_with_provenance.csv`; keep `metadata_field_provenance.csv` as supporting evidence.
- [ ] Include donor summary, matching audit, QC table, corrected figures and **executed notebook with visible outputs**.
- [ ] Include the **two-page** findings/feasibility report and three-paper comparison.
- [ ] Include this `README.md` and `requirements.txt`.
- [ ] Repeat the notebook on a clean Colab runtime and record confirmation/results. **Not yet verified as a fresh rerun here.**
- [ ] Four team members can explain dataset organization, donor-level biological replication and limitations.

**Contact/maintainer:** Team of four thesis members; add names only if required by the supervisor.
