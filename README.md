<img src="https://upload.wikimedia.org/wikipedia/en/c/c3/Flag_of_France.svg" width="100px" height="auto" />

# Lyra_Mildew_Mistral7B  
Modèle LoRA pour l’évaluation du risque de mildiou de la vigne  
➡️ [English version / Version anglaise](./README_En.md)

---

# 🏷️ Badges

![Mistral AI](https://img.shields.io/badge/Model-Mistral%207B-blue.svg)
![Europe](https://img.shields.io/badge/Origin-Europe-blue)
[![HuggingFace](https://img.shields.io/badge/HuggingFace-Model-yellow.svg)](https://huggingface.co/jeromex1/lyra_Mildew_mistral7B_LoRA)

---

# 🌿 Présentation générale

**Lyra_Mildew_Mistral7B** est un modèle **LoRA** basé sur **Mistral 7B**, spécialisé dans la **prédiction du risque de mildiou de la vigne (*Plasmopara viticola*)**, à partir de variables agro-météorologiques standard :

- `Stade_phenologique`
- `Humectation_continue` (h)
- `Temperature_moyenne` (°C)
- `Pluie_24h` (mm)
- `Inoculum` (0.1 à 0.9)

Le modèle retourne un format clair et structuré :

```
Risque : [faible / moyen / élevé]
Recommandation : [1 à 3 phrases, spécifique et actionnable]
```

Ce projet s’inscrit dans une démarche d’**IA appliquée à l’agronomie**, adaptée aux besoins d’aide à la décision dans les systèmes de production viticole.

---

# 📁 Arborescence du dépôt

```
├── README.md                         # Documentation complète en français
├── README_En.md                      # Documentation anglaise (résumé du projet)
│
├── code_prompt/
│   ├── dataset_building_rules.txt    # Règles guidant la génération du dataset
│   ├── Lyra_Mildew_7B.py             # Script Python d'entraînement LoRA
│   └── system_prompt.txt             # Prompt système utilisé pour le modèle
│
├── controle_dataset/
│   ├── quality_control_En.md         # Rapport QC dataset (anglais)
│   ├── quality_control_Fr.md         # Rapport QC dataset (français)
│   ├── moisture_statistics.png       # Répartition des durées d'humectation
│   ├── rain_statistics.png           # Répartition du cumul de pluie
│   └── temperature_statistics.png    # Répartition des températures moyennes
│
├── datasets/
│   ├── dataset_mildiou_1500_train.jsonl       # Dataset d'entraînement (1500 lignes)
│   ├── dataset_mildiou_validation_149.jsonl   # Dataset de validation (149 lignes)
│   └── Mildew_dataset_middle_30.jsonl         # Dataset de calibration ciblée (30 lignes)
│
├── pre_test_7B_8B/
│   ├── playground_7b_8b.txt          # Batterie de prompts de test (modèles non entraînés)
│   └── comparaison_7B_8B.xlsx        # Tableau comparatif Mistral 7B vs 8B
```

---

# 📚 Datasets utilisés

| Dataset | Taille | Rôle |
|--------|--------|------|
| `dataset_mildiou_1500_train.jsonl` | 1500 | Entraînement principal LoRA |
| `dataset_mildiou_validation_149.jsonl` | 149 | Validation |
| `Mildew_dataset_middle_30.jsonl` | 30 | Calibration ciblée (floraison extrême) |

Les datasets utilisent un **format JSONL de type chat**, compatible Mistral et Hugging Face.

---

# ⚙️ Hyperparamètres d’entraînement

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

### 🔧 Calibration spécialisée (30 lignes)
Une mini-passe supplémentaire a permis d’ajuster la détection du risque élevé en floraison.

**Loss (steps 1→4)** :  
`2.38 → 1.30 → 1.00 → 0.90`

---

# 📊 Résultats d’entraînement

| Epoch | Training Loss | Validation Loss | Entropy | Mean Token Accuracy |
|-------|---------------|-----------------|---------|----------------------|
| 1     | 0.2809        | 0.2760          | 0.2861  | 0.9200               |
| 2     | 0.2760        | 0.2722          | 0.2819  | 0.9226               |
| 3     | 0.2693        | 0.2696          | 0.2766  | 0.9233               |

**Convergence stable**, très faible écart train/validation.

---

# 🧪 Évaluation sur 12 scénarios agro-météo

Un panel de 12 situations représentatives (faible / moyen / élevé), incluant plusieurs cas limites, a été utilisé.

## 🎯 Taux d'exactitude

| Critère | Score | Commentaire |
|---------|--------|-------------|
| Exactitude stricte | **75 %** | Correspondance exacte |
| Aide à la décision | **83 %** | Surévaluations prudentes acceptées |
| Exactitude agronomique réaliste | **≈ 92 %** | Le modèle évite les sous-estimations, ce qui est recherché |

Le modèle adopte une posture **prudente**, utile pour limiter les faux négatifs dans un contexte phytosanitaire.

---

# 🌦️ Ressources utilisées (Google Colab Pro)

- GPU : **A100 40GB**  
- VRAM utilisée : **20.87 GB**  
- RAM utilisée : **9.20 GB**  
- Temps d'exécution : quelques minutes pour la calibration

---

# 🔗 Lien vers le modèle Hugging Face

👉 https://huggingface.co/jeromex1/lyra_Mildew_mistral7B_LoRA

---

# 🚀 Exemple d’utilisation (Python)

```python
from transformers import AutoModelForCausalLM, AutoTokenizer

name = "jeromex1/lyra_Mildew_mistral7B_LoRA"
tok = AutoTokenizer.from_pretrained(name)
model = AutoModelForCausalLM.from_pretrained(name)

messages = [
    {"role": "system", "content": "Tu es un agronome spécialisé dans le mildiou…"},
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

Ce modèle constitue une **aide à la décision**.  
Il ne remplace pas l’expertise agronomique, les observations de terrain ou les réglementations locales concernant l’usage de produits phytosanitaires.

---

# 📄 Licence

Usage autorisé pour la recherche, l'enseignement, la démonstration et l’expérimentation agronomique non-commerciale.
