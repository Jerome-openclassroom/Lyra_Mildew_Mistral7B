## 📘 Dataset Preparation & Quality Control (Downy Mildew Risk Model)

This project relies on two carefully engineered synthetic datasets designed to fine-tune a
**Mistral-7B** model for predicting grapevine downy mildew (*Plasmopara viticola*) risk from five agronomic and meteorological variables.
The entire workflow places strong emphasis on **data realism**, **statistical balance**, and **quality assurance**.

---

### 1. 🎯 Dataset Composition

Two datasets were generated:

| Dataset | Initial Size | Final Size | Purpose |
|--------|--------------|------------|---------|
| **Training** | 1500 samples | 1500 samples | Supervised Fine-Tuning |
| **Validation** | 150 samples | 149 samples | Monitoring convergence & evaluation |

The output classes follow a controlled distribution:
- **40%** low risk  
- **40%** medium risk  
- **20%** high risk  

All samples use a strict chat-style JSONL format (`messages`).

---

### 2. 🌧️ Agronomic Constraints Embedded in Data Generation

The synthetic data generation procedure implements detailed, domain-specific rules ensuring biological and meteorological plausibility:

- **Rainfall 0–40 mm**, distributed across four intervals typical of Mediterranean climates.
- **Leaf wetness duration** conditioned by rainfall, temperature, and realistic drying scenarios.
- **Temperature profiles** aligned with known infection dynamics of *P. viticola*.
- **Realistic summer storm exceptions** (heavy rain + rapid drying) restricted to **10%** of samples within the 20–40 mm class.
- **Discrete inoculum levels** (0.3, 0.5, 0.7, 0.9) reflecting infection frequency across previous seasons.
- **Phenological stage weighting**: flowering > young leaves > other stages.

These constraints prevent unrealistic combinations (e.g., heavy rain with zero wetness, prolonged wetness at very high temperature, etc.).

---

### 3. 📊 Training Dataset Statistics (1500 samples)

A full statistical audit confirmed **zero strict duplicates**, including manual inspection to resolve LLM false positives.

**Rainfall distribution (per 5 mm bins):**
- 0–5 mm: **29.47%**  
- 5–10 mm: **25.20%**  
- 10–15 mm: **15.20%**  
- 15–20 mm: **12.73%**  
- 20–25 mm: **5.80%**  
- 25–30 mm: **3.80%**  
- 30–35 mm: **3.60%**  
- 35–40 mm: **3.33%**  
- 40–45 mm: **0.87%**  

**Leaf wetness duration (per 4-hour bins):**
- 0–4 h: **9.20%**  
- 4–8 h: **32.87%**  
- 8–12 h: **32.73%**  
- 12–16 h: **13.93%**  
- 16–20 h: **7.87%**  
- 20–24 h: **2.87%**  
- 24–28 h: **0.53%**

**Temperature distribution (per 5°C bins):**
- 9–14°C: **15.93%**  
- 14–19°C: **30.80%**  
- 19–24°C: **30.07%**  
- 24–29°C: **23.07%**  
- 29–34°C: **0.13%**

These statistics strongly match real Mediterranean spring–summer conditions and produce a dataset well adapted for mildew risk modeling.

---

### 4. 🧪 Validation Dataset Quality Check (initially 150 samples)

- No internal inconsistencies detected.
- Same qualitative distributions as the training set.
- **One strict duplicate** was identified by cross-checking with the training dataset and **removed manually**.
- Final size: **149 samples**.

This adjustment has no adverse effect on validation quality:  
the removed line represents only **3.3%** of the “high risk” class (29 vs. 30 samples).

---

### 5. 🧹 Final Data Integrity Summary

- **Training:** 1500 samples — clean, diverse, meteorologically consistent  
- **Validation:** 149 samples — no overlap with training  
- Strict adherence to the Mistral `messages` format  
- High agronomic realism and internal coherence  
- Suitable for robust LoRA / QLoRA fine-tuning

This rigorous data preparation ensures that the model learns from:
- balanced and realistic scenarios,  
- non-redundant examples,  
- biologically meaningful patterns,  
- and high-quality synthetic samples engineered for scientific reliability.

The datasets form a solid foundation for accurate, reproducible mildew-risk modeling in viticulture.
