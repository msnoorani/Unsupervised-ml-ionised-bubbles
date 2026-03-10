# Unsupervised Pattern Discovery in Astrophysical Emission Data
### MSc Dissertation: Clustering algorithms to detect H II regions in simulated galaxy data


## 📌 Overview

This project develops and evaluates an **unsupervised machine learning framework** for automatically identifying ionised hydrogen regions (H-II regions) in synthetic galaxy images — the glowing structures formed around massive stars where ultraviolet radiation ionises surrounding gas.

Four methods are systematically compared across **five simulated galaxies** from the Richings et al. (2022) FIRE-2 simulations, applied to synthetic Hα (hydrogen-alpha) emission maps.

> This is the same segmentation problem found in medical imaging (tumour detection) and satellite image analysis (feature extraction) — just applied to astrophysics data.

---

## 🔭 Why This Matters

H-II regions are fundamental tracers of **star formation activity** in galaxies. Traditional detection methods rely on manual expert segmentation or intensity thresholding — neither of which scales to the volumes produced by modern surveys like PHANGS-MUSE. This work develops an **automated, data-driven pipeline** suitable for large-scale astronomical surveys.

---

## 🌌 Dataset

**Source:** Richings et al. (2022) — FIRE-2 galaxy simulations with non-equilibrium chemistry and local stellar radiation

| Galaxy | Halo Mass | Image Size 
|--------|-----------|------------|
| m1e10 | 1×10¹⁰ M☉ | 2500×2500 
| m3e10 | 3×10¹⁰ M☉ | 2500×2500 
| m1e11 | 1×10¹¹ M☉ | 2500×2500 
| m3e11 | 3×10¹¹ M☉ | 2500×2500 
| m1e12 | 1×10¹² M☉ | 2500×2500 

Halo mass naming convention: `m1e10` = 1×10¹⁰ solar masses. Higher mass galaxies show extended spiral structures; lower mass galaxies show compact central emission.

**Preprocessing:**
- Logarithmic scaling applied to raw Hα maps (dynamic range spans several orders of magnitude)
- Only pixels exceeding the **95th percentile** of raw intensity retained for clustering — reduces noise while preserving significant emission structures
- Coordinates normalised to [0,1] range for algorithm compatibility

---

## ⚙️ Methods

### 1. KMeans Clustering
- Partitions spatial pixel coordinates into k disjoint clusters
- Optimal k determined via **elbow method** (k = 2–12 evaluated)
- Result: 4–5 arbitrary spatial partitions per galaxy — no physical basis

### 2. DBSCAN
- Density-based clustering — no need to specify number of clusters
- Parameters: ε = 0.15 (normalised coordinate space), min_samples = 20
- Result: 1–8 regions per galaxy — too coarse for individual H-II region cataloguing

### 3. Agglomerative Hierarchical Clustering ⭐ Best Method
- Bottom-up approach — each pixel starts as its own cluster, merged iteratively
- `distance_threshold = 0.03` (3% of normalised image extent)
- Result: **1,839–2,479 individual structures per galaxy** — finest segmentation

### 4. Blob Extraction (Connected-Component Analysis)
- Applied directly to binary bright pixel mask
- Result: Highly variable (36–3,758 blobs) — reflects emission connectivity, not individual H-II regions

---

## 📊 Results

### Structures Identified Per Galaxy

| Galaxy | KMeans | DBSCAN | **Hierarchical** | Blob Extraction |
|--------|--------|--------|-----------------|-----------------|
| m1e10 | 5 | 8 | **1,839** | 36 |
| m3e10 | 4 | 1 | **1,909** | 124 |
| m1e11 | 5 | 1 | **2,146** | 892 |
| m3e11 | 4 | 1 | **2,460** | 1,847 |
| m1e12 | 5 | 2 | **2,479** | 3,758 |
| **Mean** | **4.6** | **2.6** | **2,146 ± 249** | **1,331** |

### Key Finding

**Hierarchical clustering is the most effective method**, consistently identifying ~2,000 H-II region candidates per galaxy with a coefficient of variation of just 11.6% — demonstrating robustness across diverse galaxy morphologies and halo masses.

The flux and area distributions of identified clusters follow **log-normal profiles** that are consistent across all five galaxies, suggesting physically meaningful and reproducible detections regardless of host galaxy environment.

---

## 💡 Discussion

| Method | Verdict | Reason |
|--------|---------|--------|
| KMeans | ❌ Not suitable | Produces arbitrary spatial partitions with no astrophysical meaning |
| DBSCAN | ❌ Too coarse | Merges most emission into a single region at practical parameters |
| **Hierarchical** | ✅ **Recommended** | Fine-scale, consistent, physically interpretable segmentation |
| Blob Extraction | ⚠️ Complementary | Useful for connectivity analysis but highly variable across galaxies |

Hierarchical clustering is consistent with the **dendrogram-based approach of McLeod et al. (2021)**, who successfully catalogued several hundred H-II regions in NGC 300 using SCIMES — validating the hierarchical segmentation strategy on real observational data.

---

## 🔬 Astrophysical Insights

- **m3e10** shows a distinctive **ring-like morphology** — the 1,909 clusters trace a ring structure likely representing a starburst ring driven by bar-induced gas inflows
- **m1e12** (most massive) shows clear **spiral arm features** — the 2,479 identified structures (highest count) are distributed along the spiral arms, consistent with the known correlation between spiral structure and star formation
- The similarity of flux distributions across all five galaxies suggests **universal H-II region properties** independent of host galaxy mass

---

## 🚀 Transferability

The segmentation pipeline developed here is directly applicable to:
- **Medical imaging** — tumour/lesion detection in MRI/CT scans uses identical connected-component and hierarchical clustering approaches
- **Satellite imagery** — pothole and land feature detection from aerial images
- **Industrial inspection** — defect detection in manufacturing quality control

This cross-domain applicability makes the methodology relevant beyond astrophysics.

---

## 🛠️ Tech Stack

- **Python** — NumPy, Pandas, Matplotlib
- **Machine Learning** — Scikit-learn (KMeans, AgglomerativeClustering, DBSCAN)
- **Image Processing** — SciPy (connected-component analysis, KDE)
- **Visualisation** — Matplotlib (log-scaled emission maps, cluster overlays, distribution plots)
- **Data** — FITS/array format Hα emission maps from FIRE-2 simulations

---

## 📁 Repository Structure

```
├── Unsupervised-ml-ionised-bubbles.ipynb   # Full analysis notebook
├── README.md                                # Project documentation
└── data/
    └── README.md                            # Dataset instructions
```

---

## 📥 Dataset

The synthetic Hα emission maps are from the **Richings et al. (2022)** FIRE-2 galaxy simulations.

> Richings, A.J., Faucher-Giguère, C.A., Gurvich, A.B., Schaye, J. and Hayward, C.C., 2022. The effects of local stellar radiation and dust depletion on non-equilibrium interstellar chemistry. *Monthly Notices of the Royal Astronomical Society*, 519(3), pp.3733–3758.

Please contact the authors or access via the FIRE simulations data portal for the raw emission maps.

---

## 📚 Key References

- McLeod et al. (2021) — Hierarchical H-II region identification in NGC 300 using SCIMES dendrograms
- Emsellem et al. (2022) — PHANGS-MUSE survey of spatially-resolved emission data
- Hunt & Reffert (2021) — Clustering algorithms for stellar structure identification in Gaia data
- Hausen & Robertson (2020) — Deep learning for pixel-level astronomical image analysis

---

## 👤 Author

**Muhammad Salahuddin**  
MSc Artificial Intelligence & Data Science — University of Hull  
[GitHub](https://github.com/msnoorani) | [LinkedIn](https://linkedin.com/in/msnoorani)
