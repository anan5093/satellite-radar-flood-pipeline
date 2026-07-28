# 🛰️ Planetary-Scale AI Disaster Response Pipeline

**Automated SAR-Based Flood Detection & Population Exposure Analytics for India**

[![Platform](https://img.shields.io/badge/Platform-Google%20Earth%20Engine-4285F4?logo=googleearth&logoColor=white)](https://earthengine.google.com/)
[![Python](https://img.shields.io/badge/Python-3.8%2B-3776AB?logo=python&logoColor=white)](https://www.python.org/)
[![Data Source](https://img.shields.io/badge/Data-Sentinel--1%20SAR-orange)](https://sentiwiki.copernicus.eu/web/s1-mission)
[![License](https://img.shields.io/badge/License-MIT-green.svg)](#license)
[![Status](https://img.shields.io/badge/Status-Active-success)](#)
[![Live Demo](https://img.shields.io/badge/Live%20Demo-View%20Map-brightgreen)](https://anan5093.github.io/satellite-radar-flood-pipeline/)

🔗 **[View the Live Interactive Flood Map](https://anan5093.github.io/satellite-radar-flood-pipeline/)**

An automated geospatial machine learning application that processes space-borne Synthetic Aperture Radar (SAR) data to detect macro-regional flood extents and calculate human population exposure across India. Built on Google Earth Engine's petabyte-scale catalog, the pipeline turns raw satellite radar into actionable disaster-response intelligence — without a single cloud-free optical image required.

---

## 📖 Table of Contents

- [Overview](#-overview)
- [Key Features](#-key-features)
- [How It Works](#-how-it-works)
- [Tech Stack & Architecture](#️-tech-stack--architecture)
- [Results Summary (Monsoon Baseline)](#-results-summary-monsoon-baseline)
- [Repository Structure](#-repository-structure)
- [Getting Started](#-getting-started)
- [Usage](#-usage)
- [Methodology Details](#-methodology-details)
- [Limitations & Caveats](#-limitations--caveats)
- [Roadmap](#-roadmap)
- [Contributing](#-contributing)
- [License](#-license)
- [Acknowledgements & Data Sources](#-acknowledgements--data-sources)
- [Contributors](#-contributors)

---

## 🌊 Overview

Monsoon flooding is one of the most recurring and destructive natural hazards in South Asia, affecting tens of millions of people annually across the Ganga-Brahmaputra basin. Traditional optical satellite imagery is often useless during active flood events because the same storm systems that cause the flooding also blanket the region in cloud cover.

This project solves that problem by relying entirely on **Synthetic Aperture Radar (SAR)**, which transmits its own microwave energy and can image the Earth's surface **day or night, through clouds, rain, and haze**. The pipeline automates the full workflow — from raw radar ingestion to a final, quantified disaster report — for ten flood-prone Indian states, and cross-references detected flood polygons against high-resolution population grids to estimate human exposure.

The end goal is a **repeatable, serverless, planetary-scale template** that can be pointed at any monsoon window, any region, and any population dataset to produce a rapid flood-impact assessment in minutes rather than days.

---

## 🚀 Key Features

- **All-Weather Ingestion** — Utilizes Sentinel-1 (C-band SAR) imagery to see through cloud cover and storm systems during monsoon seasons, eliminating the "cloud gap" that cripples optical-only disaster response.
- **Macro-Regional Pipelines** — Scales data processing across 10 flood-prone Indian states (including Uttar Pradesh, Bihar, and Assam) using Google Earth Engine's distributed cloud computing, avoiding local downloads of multi-gigabyte radar scenes.
- **Automated Change Detection** — Implements a radar backscatter thresholding engine (**-16 dB**) to isolate new surface floodwater from permanent water bodies (rivers, lakes, reservoirs).
- **Socioeconomic Impact Analytics** — Integrates WorldPop high-resolution gridded population datasets to map and count human population exposure inside detected disaster zones.
- **Zero Local Infrastructure** — The entire pipeline runs against Earth Engine's servers; no GPU, no local satellite data storage, and no manual scene downloading is required.
- **Reproducible Notebook Workflow** — The full analysis is captured in a single, well-commented Jupyter notebook so results can be audited, re-run, or adapted to new regions/time windows.

---

## 🧠 How It Works

At a high level, the pipeline follows a classic remote-sensing change-detection pattern, executed entirely inside Google Earth Engine:

```
┌──────────────────┐     ┌───────────────────┐     ┌────────────────────┐
│ 1. Data Ingestion │ --> │ 2. Preprocessing   │ --> │ 3. Change Detection │
│ Sentinel-1 GRD    │     │ Speckle filtering, │     │ Pre/post-flood      │
│ (VH polarization) │     │ orbit filtering,   │     │ backscatter diff,   │
│                   │     │ AOI clipping       │     │ dB threshold (-16)  │
└──────────────────┘     └───────────────────┘     └──────────┬─────────┘
                                                               │
┌──────────────────┐     ┌───────────────────┐     ┌──────────▼─────────┐
│ 6. Reporting      │ <-- │ 5. Population       │ <-- │ 4. Permanent Water  │
│ Area (km²),        │     │ Exposure Analysis   │     │ Masking              │
│ population exposed │     │ WorldPop overlay,   │     │ Removes rivers/lakes │
│ per state/region    │     │ zonal sum           │     │ from flood layer      │
└──────────────────┘     └───────────────────┘     └────────────────────┘
```

1. **Ingest** Sentinel-1 Ground Range Detected (GRD) SAR imagery for a pre-flood ("dry") baseline period and a post-flood ("wet") monsoon period over the target states.
2. **Preprocess** the radar collections — filtering by orbit pass direction and polarization (VH), and applying speckle-noise reduction to clean up radar "salt-and-pepper" noise.
3. **Detect change** by comparing backscatter intensity between the dry and wet composites. Pixels whose backscatter drops below the **-16 dB** threshold are flagged as candidate open water, since smooth water surfaces reflect radar energy away from the sensor (specular reflection) rather than back to it.
4. **Mask permanent water** (rivers, canals, reservoirs already present in the dry baseline) so that only *newly inundated* land is retained as "flood extent."
5. **Overlay population data** from the WorldPop Global Density dataset onto the flood mask and compute a zonal sum to estimate the number of people living inside the flooded area.
6. **Aggregate and report** total inundated area (km²) and estimated population exposed, broken down by state/region for rapid situational awareness.

---

## 🛠️ Tech Stack & Architecture

| Component | Choice |
|---|---|
| **Language** | Python 3 |
| **Cloud Platform** | Google Earth Engine (GEE API) |
| **Primary Data Source** | European Space Agency (ESA) Copernicus **Sentinel-1 GRD** (C-band SAR) |
| **Population Data Source** | **WorldPop** Global Density Dataset |
| **Core Libraries** | [`ee`](https://developers.google.com/earth-engine/guides/python_install) (Earth Engine Python API), [`geemap`](https://geemap.org/) |
| **Execution Environment** | Jupyter Notebook / Google Colab |
| **Output** | Static HTML map export, tabular summary statistics |

### Why Google Earth Engine?

Rather than downloading terabytes of raw Sentinel-1 scenes, GEE hosts the full Copernicus archive and executes computation server-side next to the data. This is what makes a "planetary-scale" macro-regional analysis — spanning 10 states — feasible from a single notebook running on a laptop-class machine.

---

## 📊 Results Summary (Monsoon Baseline)

| Metric | Value |
|---|---|
| **Target Area** | Northern & Northeastern India (10 flood-prone states, incl. Uttar Pradesh, Bihar, Assam) |
| **Total Detected Flood Inundation** | **3,966 sq km** |
| **Estimated Human Population Exposed** | **28,710 citizens** |

> ⚠️ **Note:** These figures represent a single monsoon baseline run and are sensitive to the chosen date range, cloud/incidence-angle artifacts, backscatter threshold, and the resolution of the underlying WorldPop layer. Re-running the pipeline against a different time window will produce different results — treat these numbers as a **demonstration output**, not a certified damage assessment. See [Limitations & Caveats](#-limitations--caveats).

---

## 📁 Repository Structure

```
satellite-radar-flood-pipeline/
├── Radar_Flood_Mapping_Project.ipynb   # Main analysis notebook: ingestion → detection → reporting
├── index.html                          # Exported/static map visualization of flood results
└── README.md                           # Project documentation (this file)
```

---

## ⚙️ Getting Started

### Prerequisites

- A [Google Earth Engine account](https://signup.earthengine.google.com/) (free for research/non-commercial use)
- Python 3.8+
- Jupyter Notebook, JupyterLab, or Google Colab

### Installation

```bash
# Clone the repository
git clone https://github.com/anan5093/satellite-radar-flood-pipeline.git
cd satellite-radar-flood-pipeline

# (Recommended) create a virtual environment
python -m venv venv
source venv/bin/activate      # On Windows: venv\Scripts\activate

# Install dependencies
pip install earthengine-api geemap jupyter
```

### Authenticate with Earth Engine

The first time you run the notebook, you'll need to authenticate against your GEE account:

```python
import ee
ee.Authenticate()   # opens a browser window for one-time OAuth login
ee.Initialize(project="your-gee-project-id")
```

---

## ▶️ Usage

1. Launch the notebook:
   ```bash
   jupyter notebook Radar_Flood_Mapping_Project.ipynb
   ```
   *(or open it directly in Google Colab)*
2. Set your **Area of Interest (AOI)** — by default, the notebook targets the 10 flood-prone Indian states referenced above; swap in your own state/district FeatureCollection to retarget the analysis.
3. Set the **pre-flood** and **post-flood** date ranges to match the monsoon window you want to assess.
4. Run all cells. The notebook will:
   - Pull and filter the Sentinel-1 GRD collection
   - Apply speckle filtering and compute the backscatter difference
   - Threshold at -16 dB and mask out permanent water
   - Overlay WorldPop population density
   - Print/export the total inundated area and estimated population exposed
5. View the exported interactive map in `index.html` locally, or check out the already-deployed version here: **[anan5093.github.io/satellite-radar-flood-pipeline](https://anan5093.github.io/satellite-radar-flood-pipeline/)**

---

## 🔬 Methodology Details

**Why -16 dB?**
Radar backscatter is highly sensitive to surface roughness. Smooth, open water reflects incoming microwave energy specularly (away from the sensor), producing very low return signal. Empirically, VH-polarized Sentinel-1 backscatter below roughly **-16 dB** is a well-established proxy threshold in flood-mapping literature for discriminating open water from vegetated or built-up land. This pipeline uses that threshold as a first-order classifier; regions with dense aquatic vegetation, urban radar shadow, or very calm non-flood water bodies can still introduce false positives, which is why the permanent-water mask step is essential.

**Why VH polarization?**
Cross-polarized (VH) backscatter tends to show a starker contrast between water and non-water surfaces than co-polarized (VV) data, making it a common default for automated water-detection workflows.

**Why WorldPop over other population datasets?**
WorldPop provides globally consistent, high-resolution (100m) gridded population estimates, making it straightforward to perform a zonal-statistics overlay directly inside Earth Engine without needing to reproject or resample manually.

---

## ⚠️ Limitations & Caveats

- **Threshold sensitivity:** A fixed -16 dB cutoff is a simplification. Optimal thresholds can shift with incidence angle, local terrain, and land cover, and a locally-calibrated (e.g., Otsu) threshold may outperform a static one in production settings.
- **False positives:** Radar shadow (from mountains/buildings), very calm non-flood water, and some paved surfaces can be misclassified as "flood" by backscatter alone.
- **False negatives:** Flooding under dense forest canopy or in dense urban cores can be underestimated, since SAR backscatter behaves differently in these environments than over open, flooded land.
- **Population estimate accuracy:** WorldPop is itself a *modeled* estimate, not a census in real time — actual exposed population may differ, especially in informal settlements or areas with recent demographic shifts.
- **Not a substitute for ground validation:** This pipeline is designed as a rapid, wide-area **first-pass** situational-awareness tool for disaster response teams — outputs should be corroborated with ground reports, higher-resolution imagery, or in-situ sensors before being used for resource allocation or policy decisions.

---

## 🗺️ Roadmap

- [ ] Automate periodic (e.g., daily/weekly) reruns via Earth Engine batch tasks or Cloud Functions for near-real-time monsoon monitoring
- [ ] Add dynamic/adaptive thresholding (Otsu's method) in place of the fixed -16 dB cutoff
- [ ] Expand macro-regional coverage beyond the current 10 states to all of India
- [ ] Add district-level and sub-district-level breakdowns of exposure statistics
- [ ] Integrate additional exposure layers (agricultural land, road networks, critical infrastructure)
- [ ] Publish a lightweight public dashboard for non-technical disaster-response stakeholders

---

## 🤝 Contributing

Contributions, issue reports, and feature suggestions are welcome.

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/your-feature`)
3. Commit your changes (`git commit -m "Add your feature"`)
4. Push to the branch (`git push origin feature/your-feature`)
5. Open a Pull Request describing your change and its motivation

Please open an issue first for larger changes so the approach can be discussed before implementation.

---

## 📄 License

This project is licensed under the **MIT License**. See the `LICENSE` file for details (add one to the repo root if not already present).

---

## 🙏 Acknowledgements & Data Sources

- **[European Space Agency (ESA) / Copernicus Programme](https://sentiwiki.copernicus.eu/web/s1-mission)** — Sentinel-1 SAR GRD imagery
- **[Google Earth Engine](https://earthengine.google.com/)** — Planetary-scale geospatial analysis platform
- **[WorldPop](https://www.worldpop.org/)** — Global gridded population density datasets
- **[geemap](https://geemap.org/)** — Python package for interactive mapping with Earth Engine

---

## 👤 Contributors

- **[Anand Raj](https://github.com/anan5093)** — Project author, pipeline architecture, SAR change-detection methodology, and analysis notebook

Want to see your name here? Check the [Contributing](#-contributing) section above and open a pull request!
