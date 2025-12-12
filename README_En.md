<img src="https://upload.wikimedia.org/wikipedia/en/c/c3/Flag_of_France.svg" width="100px" height="auto" />

# Lyra_Mildew_Mistral7B  
LoRA model for grapevine downy mildew risk assessment  
➡️ This is the English documentation (French version available in `README.md`)

---

# 🏷️ Badges

![Mistral AI](https://img.shields.io/badge/Model-Mistral%207B-blue.svg)
![Europe](https://img.shields.io/badge/Origin-Europe-blue)
[![HuggingFace](https://img.shields.io/badge/HuggingFace-Model-yellow.svg)](https://huggingface.co/jeromex1/lyra_Mildew_mistral7B_LoRA)

---

# 🌿 Overview

**Lyra_Mildew_Mistral7B** is a **LoRA fine-tuned Mistral 7B model** designed to estimate the **risk of grapevine downy mildew (*Plasmopara viticola*)** using standard agro-meteorological variables:

- `Stade_phenologique` (phenological stage)  
- `Humectation_continue` (continuous leaf wetness, h)  
- `Temperature_moyenne` (mean temperature, °C)  
- `Pluie_24h` (24h rainfall, mm)  
- `Inoculum` (0.1 to 0.9)

The model outputs a structured response:

```
Risk : [low / medium / high]
Recommendation : [1–3 actionable agronomic sentences]
```

This project contributes to **applied AI in agronomy**, supporting viticulture decision-making.

---

# 📁 Repository Structure

```
├── README.md                         # Complete French documentation
├── README_En.md                      # English documentation
│
├── code_prompt/
│   ├── dataset_building_rules.txt    # Prompt used for dataset generation
│   ├── Lyra_Mildew_7B.py             # Python training script (LoRA)
│   └── system_prompt.txt             # System prompt used for pre-tests and training
│
├── controle_dataset/
│   ├── quality_control_En.md         # Dataset quality control (English)
│   ├── quality_control_Fr.md         # Dataset quality control (French)
│   ├── moisture_statistics.png       # Leaf wetness duration distribution
│   ├── rain_statistics.png           # Rainfall distribution
│   └── temperature_statistics.png    # Temperature distribution
│
├── datasets/
│   ├── dataset_mildiou_1500_train.jsonl       # Training dataset (1500 rows)
│   ├── dataset_mildiou_validation_149.jsonl   # Validation dataset (149 rows)
│   └── Mildew_dataset_middle_30.jsonl         # Calibration dataset (30 rows)
│
├── pre_test_7B_8B/
│   ├── playground_7b_8b.txt          # Prompt battery for raw-model comparison
│   └── comparaison_7B_8B.xlsx        # Mistral 7B vs 8B comparison table
```

---

# 📚 Datasets

| Dataset | Size | Purpose |
|---------|------|---------|
| `dataset_mildiou_1500_train.jsonl` | 1500 | Main LoRA training |
| `dataset_mildiou_validation_149.jsonl` | 149 | Validation |
| `Mildew_dataset_middle_30.jsonl` | 30 | Targeted calibration (extreme flowering conditions) |

All datasets use **chat-style JSONL** compatible with Mistral and Hugging Face.

---

# ⚙️ Training Hyperparameters

```python
output_dir="./lyra_Mildew_LoRA"
num_train_epochs=3
per_device_train_batch_size=2
per_device_eval_batch_size=2
gradient_accumulation_steps=8
learning_rate=2e-4
fp16=True
logging_steps=10
eval_strategy="epoch"
save_strategy="epoch"
lr_scheduler_type="cosine"
warmup_ratio=0.03
weight_decay=0.0
report_to="none"
```

### 🔧 Targeted Calibration (30 lines)

A small additional fine-tuning step was performed to improve high-risk detection during flowering.

**Loss progression (steps 1→4):**  
`2.38 → 1.30 → 1.00 → 0.90`

---

# 📊 Training Results

| Epoch | Training Loss | Validation Loss | Entropy | Mean Token Accuracy |
|-------|---------------|-----------------|---------|----------------------|
| 1     | 0.2809        | 0.2760          | 0.2861  | 0.9200               |
| 2     | 0.2760        | 0.2722          | 0.2819  | 0.9226               |
| 3     | 0.2693        | 0.2696          | 0.2766  | 0.9233               |

Training shows **stable convergence** and strong alignment between train/validation.

---

# 🧪 Evaluation on 12 Expert Scenarios

A curated set of 12 scenarios (low/medium/high), including borderline agro-meteorological conditions, was used for testing.

## 🎯 Accuracy Levels

| Criterion | Score | Notes |
|----------|--------|-------|
| Strict match | **75%** | Exact predicted = expected |
| Decision-support accuracy | **83%** | Conservative overestimation accepted |
| Agronomically realistic accuracy | **≈ 92%** | Includes borderline low→medium reclassifications |

This final score best reflects real-world usage:  
**prioritizing the avoidance of false negatives** in viticulture risk management.

---

# 🌦️ Training Resources (Google Colab Pro)

- GPU : **A100 40GB**  
- VRAM used : **20.87 GB**  
- RAM used : **9.20 GB**  
- Training time : a few minutes for calibration

---

# 🔗 Hugging Face Model

👉 https://huggingface.co/jeromex1/lyra_Mildew_mistral7B_LoRA

---

# 🚀 Usage Example (Python)

```python
from transformers import AutoModelForCausalLM, AutoTokenizer

name = "jeromex1/lyra_Mildew_mistral7B_LoRA"
tok = AutoTokenizer.from_pretrained(name)
model = AutoModelForCausalLM.from_pretrained(name)

messages = [
    {"role": "system", "content": "You are an agronomist specialized in downy mildew…"},
    {"role": "user", "content": "Stade_phenologique: floraison
Humectation_continue: 12
Temperature_moyenne: 20
Pluie_24h: 10
Inoculum: 0.7"}
]

text = tok.apply_chat_template(messages, tokenize=False, add_generation_prompt=True)
inputs = tok(text, return_tensors="pt")
outputs = model.generate(**inputs, max_new_tokens=120)
print(tok.decode(outputs[0], skip_special_tokens=True))
```

---

# ⚠️ Disclaimer

This model is an **AI-based decision-support tool**.  
It does *not* replace agronomic expertise, field observations, or local regulations regarding plant protection.

---

# 📄 License

Free for research, education, environmental experimentation, and non-commercial agronomic applications.

