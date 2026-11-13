<div align="center">

# 🖨️ AM-QED
### Additive Manufacturing Quality-Energy Dataset

**Process parameters, structural quality and energy consumption of 243 PLA specimens printed by Material Extrusion**

[![Paper](https://img.shields.io/badge/Paper-J.%20Intell.%20Manuf.%202026-1c5cab?style=flat-square)](https://doi.org/10.1007/s10845-026-02905-4)
[![Paper](https://img.shields.io/badge/Paper-J.%20Manuf.%20Process.%202024-1c5cab?style=flat-square)](https://doi.org/10.1016/j.jmapro.2024.08.061)
[![License: CC BY 4.0](https://img.shields.io/badge/License-CC%20BY%204.0-2a78d6?style=flat-square)](https://creativecommons.org/licenses/by/4.0/)
![Specimens](https://img.shields.io/badge/Specimens-243-3987e5?style=flat-square)
![Format](https://img.shields.io/badge/Format-CSV-6da7ec?style=flat-square)

[Overview](#-overview) •
[Experimental Setup](#-experimental-setup) •
[Data Format](#-data-format) •
[Quick Start](#-quick-start) •
[Use Cases](#-use-cases) •
[Publications](#-related-publications) •
[Citation](#-citation)

</div>

---

## 🔍 Overview

**AM-QED** is an experimental dataset collected on an industrial **Material Extrusion (FFF)** 3D printer. Each printed specimen links its **process parameters** to two outputs that are rarely measured together:

- 🧱 **Structural quality**: void fraction, measured by microscope image analysis
- ⚡ **Energy consumption**: electrical energy used to print the specimen

The dataset follows a **full factorial design** under controlled laboratory conditions. This makes it suitable for reproducible research on how printing parameters drive both structural risk and energy use.

### ✨ At a glance

| | |
|---|---|
| 🧪 **Specimens** | 243 (81 parameter combinations × 3 replicas) |
| 🎛️ **Process parameters** | 4 parameters × 3 levels (full factorial 3⁴ design) |
| 🎯 **Targets** | Void fraction (%) and energy consumption (J), plus ready-to-use class labels |
| 🖨️ **Printer** | CreatBot F430 (industrial, Material Extrusion) |
| 🧵 **Material** | PLA |
| 🔬 **Quality measurement** | HIROX RH-2000 digital microscope |
| 📐 **Specimen geometry** | 20 mm × 20 mm squares, 0.225 mm layer thickness |

---

## 🧪 Experimental Setup

### Process parameters

| Parameter          | Column          | Levels                     |
|--------------------|-----------------|----------------------------|
| 🌡️ Bed temperature    | `Bed_Temp`      | 20 · 45 · 70 °C            |
| 🔥 Nozzle temperature | `Printing_Temp` | 195 · 205 · 215 °C         |
| 💧 Material flow rate | `Flow_rate`     | 90 · 100 · 110 % of nominal |
| 🚀 Printing speed     | `Speed`         | 20 · 50 · 80 mm/s          |

All experiments were carried out under constant environmental conditions, with printer calibration before each batch.

### Build plate layout

Each print contains **27 specimens** arranged in three 3 × 3 chessboards (quadrants **I**, **II**, **III**), one per nozzle temperature (195, 205 and 215 °C). Within each chessboard, columns correspond to the **flow rate** (90, 100, 110 %) and rows to the **printing speed** (20, 50, 80 mm/s). Numbers are the specimen positions (`Position` column).

<div align="center">
  <img src="images/build_plate.png" alt="Build plate layout with 27 specimen positions in three quadrants" width="720">
</div>

**Bed temperature** is used as a blocking factor: because of the bed's thermal inertia it stays constant for a whole print. The plate is printed at 20, 45 and 70 °C, three times each (3 bed temperatures × 3 replicas × 27 specimens = 243).

### Measured outputs

- **Void fraction (%)**: share of empty area relative to the specimen's total area, from image analysis of micrographs acquired with the HIROX RH-2000 digital microscope.
- **Energy consumption (J)**: total electrical energy used to print a single specimen, computed by numerical integration of the active power over time. Power is acquired separately for the printhead motion system, the bed movement system and the nozzle and bed heating units.

<div align="center">
<picture>
  <source media="(prefers-color-scheme: dark)" srcset="images/energy_heatmap_dark.png">
  <img src="images/energy_heatmap_light.png" alt="Heatmap of energy consumption per specimen by nozzle temperature, bed temperature and printing speed" width="860">
</picture>
</div>

---

## 📊 Data Format

- **File:** `AM-QED.csv`
- **Separator:** semicolon (`;`)
- **Rows:** 243, one per specimen
- **Columns:** 15

| Column                | Type        | Description |
|-----------------------|-------------|-------------|
| `Type`                | categorical | Material (always `PLA`) |
| `Bed_Temp`            | input       | Bed temperature (°C) |
| `Printing_Temp`       | input       | Nozzle temperature (°C) |
| `Flow_rate`           | input       | Material flow rate (% of nominal) |
| `Speed`               | input       | Printing speed (mm/s) |
| `Time`                | derived     | Process time per specimen (s), set by the printing speed |
| `Energy_Consumption`  | output      | Energy consumed to print the specimen (J) |
| `Void_Fraction`       | output      | Void fraction (%) |
| `Void_Fraction_Class` | label       | Three-level void fraction class (`Low` / `Medium` / `High`) |
| `Void_Fraction_bin`   | label       | Binary quality label: `Low` = SAFE (≤ 5%), `High` = RISKY (> 5%) |
| `Consumption_Class`   | label       | Energy class: `Low` (≤ 1328 J), `Medium` (1328–2708 J), `High` (> 2708 J) |
| `energy binariz`      | label       | Binary energy label (filled only for specimens printed at 20 mm/s) |
| `replicas`            | design      | Replica number (1, 2, 3) |
| `Position`            | design      | Specimen position on the build plate (1–27) |
| `Quadrant`            | design      | Chessboard the specimen belongs to (1–3, one per nozzle temperature) |

### Class distribution

| Task | Class | Samples | Share |
|------|-------|--------:|------:|
| 🧱 Quality risk (`Void_Fraction_bin`) | ✅ SAFE (≤ 5%)  | 221 | 90.95% |
|                                      | ⚠️ RISKY (> 5%) | 22  | 9.05%  |
| ⚡ Energy (`Consumption_Class`)       | 🟢 Low          | 54  | 22.22% |
|                                      | 🟡 Medium       | 90  | 37.04% |
|                                      | 🔴 High         | 99  | 40.74% |

> ⚠️ The quality task is **strongly imbalanced** (about 1 RISKY specimen in 11). Use stratified splits and metrics such as F1-score, balanced accuracy or MCC rather than plain accuracy.

---

## 🚀 Quick Start

```python
import pandas as pd

df = pd.read_csv("AM-QED.csv", sep=";")

inputs = ["Bed_Temp", "Printing_Temp", "Flow_rate", "Speed"]
print(df[inputs].describe())
print(df["Void_Fraction_bin"].value_counts())
```

**Example: predicting quality risk from process parameters**

```python
from sklearn.ensemble import RandomForestClassifier
from sklearn.model_selection import StratifiedKFold, cross_val_score

X = df[inputs]
y = (df["Void_Fraction_bin"] == "High").astype(int)   # 1 = RISKY

clf = RandomForestClassifier(class_weight="balanced", random_state=42)
cv = StratifiedKFold(n_splits=5, shuffle=True, random_state=42)
scores = cross_val_score(clf, X, y, cv=cv, scoring="f1")
print(f"F1: {scores.mean():.3f} ± {scores.std():.3f}")
```

---

## 💡 Use Cases

AM-QED can be used for:

- 🧱 **Quality risk classification**: predicting whether a parameter set leads to a SAFE or RISKY specimen
- ⚡ **Energy prediction**: regression or classification of energy consumption from process parameters
- 🎯 **Multi-objective process optimization**: finding parameter sets that keep quality high while reducing energy use
- 🔗 **Causal and statistical modelling**: studying parameter effects with a full factorial design (e.g. ANOVA, causal models)
- 🛡️ **Robustness and security of ML in manufacturing**: for example, evaluating data poisoning attacks such as label flipping

---

## 📁 Repository Structure

```
AM-QED/
├── AM-QED.csv                     # Main dataset (243 specimens, 15 columns)
├── images/
│   ├── build_plate.png            # Build plate layout
│   ├── energy_heatmap_light.png   # Energy overview (light theme)
│   └── energy_heatmap_dark.png    # Energy overview (dark theme)
└── README.md
```

---

## 📖 Related Publications

1. F. Devito, M. Mazzarisi, M. Dassisti, F. Lavecchia. *Causal technological model for predicting void fraction and energy consumption in material extrusion process of polylactic acid*. **Journal of Manufacturing Processes**, 129, 187–201, 2024. [DOI: 10.1016/j.jmapro.2024.08.061](https://doi.org/10.1016/j.jmapro.2024.08.061)
2. F. Devito, V. Gattulli, D. Impedovo, L. Musti. *A dual-module machine learning framework for joint quality risk assessment and energy-aware process optimization in additive manufacturing*. **Journal of Intelligent Manufacturing**, 2026. [DOI: 10.1007/s10845-026-02905-4](https://doi.org/10.1007/s10845-026-02905-4)
3. S. Bello, F. Devito, D. Impedovo, L. Musti. *Label Flipping Attack in Additive Manufacturing: an Underrated Threat*. **Proc. 35th International Conference on Artificial Neural Networks (ICANN 2026)**, Padua, Italy, September 2026, to appear.
4. S. Bello, F. Devito, D. Impedovo. *G-code Injection Attacks on a Closed-Firmware 3D Printer: A Case Study on the Bambu Lab H2D*. **Proc. Joint National Conference on Cybersecurity (ITASEC & SERICS 2027)**, Siena, Italy, February 2027, to appear.

---

## 📚 Citation

If you use AM-QED in your research, please cite:

```bibtex
@article{devito2026dual,
  author    = {Devito, Fabrizia and Gattulli, Vincenzo and Impedovo, Donato and Musti, Luca},
  title     = {A dual-module machine learning framework for joint quality risk assessment and energy-aware process optimization in additive manufacturing},
  journal   = {Journal of Intelligent Manufacturing},
  year      = {2026},
  publisher = {Springer},
  doi       = {10.1007/s10845-026-02905-4}
}

@article{devito2024causal,
  author    = {Devito, Fabrizia and Mazzarisi, Marco and Dassisti, Michele and Lavecchia, Fulvio},
  title     = {Causal technological model for predicting void fraction and energy consumption in material extrusion process of polylactic acid},
  journal   = {Journal of Manufacturing Processes},
  volume    = {129},
  pages     = {187--201},
  year      = {2024},
  publisher = {Elsevier},
  doi       = {10.1016/j.jmapro.2024.08.061}
}
```

<details>
<summary>BibTeX for the other related publications</summary>

```bibtex
@inproceedings{bello2026label,
  author    = {Bello, S. and Devito, Fabrizia and Impedovo, Donato and Musti, Luca},
  title     = {Label Flipping Attack in Additive Manufacturing: an Underrated Threat},
  booktitle = {Proceedings of the 35th International Conference on Artificial Neural Networks (ICANN 2026)},
  address   = {Padua, Italy},
  month     = sep,
  year      = {2026},
  note      = {To appear}
}

@inproceedings{bello2027gcode,
  author    = {Bello, S. and Devito, Fabrizia and Impedovo, Donato},
  title     = {G-code Injection Attacks on a Closed-Firmware 3D Printer: A Case Study on the Bambu Lab H2D},
  booktitle = {Proceedings of the Joint National Conference on Cybersecurity (ITASEC \& SERICS 2027)},
  address   = {Siena, Italy},
  month     = feb,
  year      = {2027},
  note      = {To appear}
}
```

</details>

---

## 📝 License

This dataset is released under the [Creative Commons Attribution 4.0 International (CC BY 4.0)](https://creativecommons.org/licenses/by/4.0/) license. You are free to share and adapt it, provided you give appropriate credit by citing the publications above.

---

## 📬 Contact

For questions, issues or collaborations, please [open an issue](../../issues) in this repository.

<div align="center">
<sub>⭐ If you find AM-QED useful, consider starring the repository!</sub>
</div>
