# scPassport 🧬

> A passport system for single-cell objects. Stamp your Seurat, SingleCellExperiment, or SummarizedExperiment data with full metadata, lineage tracking, and processing logs — all stored inside the `.rds` file itself.

---

## Installation

```r
# Install from GitHub
remotes::install_github("sedatkacar56/scPassport")
```

---

## What It Does

Every object gets a **passport** that travels with it forever:

| Object Type | Passport Location | Processing Log Location |
|---|---|---|
| Seurat | `@misc$passport` | `@misc$processing_log` |
| SingleCellExperiment | `metadata(obj)$passport` | `metadata(obj)$processing_log` |
| SummarizedExperiment | `metadata(obj)$passport` | `metadata(obj)$processing_log` |

| Section | Fields |
|---|---|
| Identity | object_id, rds_self (RDS number), created |
| Animal | animal_id, species, sex, age, condition, tissue |
| Experiment | project, researcher, date, notes |
| Lineage | parent_id, rds_parent, lineage chain, children, rds_children |
| Custom | anything you want to add |
| Processing Log | every step logged with cell count + timestamp |

---

## Quick Start

### Seurat

```r
library(scPassport)

# Stamp your object — popup opens, fill the form
WTHeme <- scPassport(WTHeme)

# Log processing steps
WTHeme <- NormalizeData(WTHeme)
WTHeme <- log_step(WTHeme, "NormalizeData",
           params = list(method = "LogNormalize"))

# Stamp a child subset — parent linked automatically
EndofrHeme <- subset(WTHeme, subset = cell_type == "Endothelial")
EndofrHeme <- scPassport(EndofrHeme, parent = WTHeme)

# Read passport anytime
read_passport(EndofrHeme)
```

### SingleCellExperiment

```r
library(scPassport)
library(SingleCellExperiment)

sce <- SingleCellExperiment(assays = list(counts = count_matrix))

# Same functions — passport goes into metadata(sce)$passport
sce <- scPassport(sce)
sce <- log_step(sce, "scran normalization")
read_passport(sce)
```

### SummarizedExperiment

```r
library(scPassport)
library(SummarizedExperiment)

se <- SummarizedExperiment(assays = list(counts = count_matrix))

# Same functions — passport goes into metadata(se)$passport
se <- scPassport(se)
se <- log_step(se, "DESeq2 normalization")
read_passport(se)
```

---

## Output of `read_passport()`

```
========== PASSPORT ==========
Object ID  : EndofrHeme
RDS Self   : 225
Created    : 2026-03-09 10:34:12
-------- Animal --------
Animal ID  : M01
Species    : Rattus norvegicus
Sex        : male
Age        : P60
Condition  : Heme
Tissue     : Lung - Endothelial
-------- Experiment --------
Project    : HEME rat PROJECT
Researcher : Sedat
Date       : 2026-03-09
Notes      : rpca integrated, subset from WTHeme
-------- Lineage --------
Parent     : WTHeme
RDS Parent : 224
Chain      : WTHeme
Children   : gCapC, gCapB, gCapD
RDS Children: 226, 227, 228
-------- Custom Fields --------
integration_type: rpca
ILMN_name      : ILMN_5564
======= PROCESSING LOG =======
[1] NormalizeData             | 54184 cells | 2026-03-09 ...
[2] RunPCA                    | 54184 cells | 2026-03-09 ...
[3] Subset Endothelial        | 12453 cells | 2026-03-09 ...
==============================
```

---

## Functions

| Function | Description |
|---|---|
| `scPassport(obj)` | Open popup to fill/update passport |
| `scPassport(obj, parent = WTHeme)` | Stamp child, auto-link to parent |
| `scPassport(obj, read = TRUE)` | Print passport to console |
| `read_passport(obj)` | Print passport to console |
| `log_step(obj, "step name", params = list(...))` | Log a processing step |

Works with **Seurat**, **SingleCellExperiment**, and **SummarizedExperiment** objects.

---

## Author

**Sedat Kacar**
Pulmonary Post Doc — Indiana University
*Praise be to Allah (SWT)*
