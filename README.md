# Planetary-Scale AI Disaster Response Pipeline (Google Earth Engine & Python)

An automated geospatial machine learning application that processes space-borne Synthetic Aperture Radar (SAR) data to detect macro-regional flood extents and calculate human population exposure across India.

## 🚀 Key Features
* **All-Weather Ingestion:** Utilizes Sentinel-1 Satellite Radar (C-band SAR) to see through cloud cover and storm systems during monsoon seasons.
* **Macro-Regional Pipelines:** Scales data processing across 10 flood-prone Indian states (including Uttar Pradesh, Bihar, and Assam) using distributed cloud computing.
* **Automated Change Detection:** Implements a radar backscatter thresholding engine (-16 dB) to isolate new surface floodwater from permanent water bodies.
* **Socioeconomic Impact Analytics:** Integrates WorldPop high-resolution datasets to map and count human population exposure inside disaster zones.

## 🛠️ Tech Stack & Architecture
* **Language:** Python
* **Cloud Platform:** Google Earth Engine (GEE API)
* **Data Sources:** European Space Agency (ESA) Copernicus Sentinel-1 GRD, WorldPop Global Density Dataset
* **Libraries:** `ee`, `geemap`

## 📊 Results Summary (Monsoon Baseline)
* **Target Area:** Northern & Northeastern India
* **Total Detected Flood Inundation:** [3966 sq km] sq km
* **Estimated Human Population Exposed:** [28710] citizens
*
