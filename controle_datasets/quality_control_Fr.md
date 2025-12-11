## 📘 Dataset Preparation & Quality Control (Mildew Risk Model)

Ce projet repose sur deux jeux de données synthétiques spécialement conçus pour entraîner un modèle
**Mistral 7B** à prédire le risque de mildiou de la vigne à partir de cinq variables
(Stade phénologique, Humectation, Température, Pluie, Inoculum).

L'objectif a été d'obtenir des datasets :
- **réalistes agronomiquement**, 
- **statistiquement équilibrés**, 
- **sans doublons**, 
- et **cohérents sur le plan agro-météorologique**.

---

### 1. 🎯 Constitution des datasets

Deux jeux de données ont été générés :

| Dataset | Taille initiale | Taille finale | Objectif |
|--------|------------------|---------------|----------|
| **Train** | 1500 lignes | 1500 lignes | Fine-tuning (SFT) |
| **Validation** | 150 lignes | 149 lignes | Suivi de la convergence |

Les sorties du modèle suivent strictement les classes :
- **40 %** de risques *faibles*  
- **40 %** de risques *moyens*  
- **20 %** de risques *élevés*

La structure des lignes respecte le format JSONL de type chat (`messages`).

---

### 2. 🌧️ Contraintes agronomiques appliquées

La génération respecte un ensemble de règles garantissant la plausibilité biologique :

- **Pluie 0–40 mm**, distribuée selon 4 plages représentatives d’un climat méditerranéen.
- **Humectation continue** dépendante du cumul de pluie et de la température.
- **Température moyenne** alignée sur les périodes d’infection de *Plasmopara viticola*.
- **Exceptions orageuses estivales réalistes** (forte pluie + séchage rapide) limitées à **10 %** des cas pour la tranche 20–40 mm.
- **Inoculum discret** : 0.3 / 0.5 / 0.7 / 0.9, dérivé du nombre d’années infectées.
- **Stades phénologiques** modulant implicitement le risque (floraison, jeunes feuilles, autres).

Ces règles garantissent des combinaisons réalistes et évitent les incohérences (ex : pluie >0 mm + humectation = 0).

---

### 3. 📊 Analyse statistique du dataset d’entraînement (1500 lignes)

Aucune occurrence de doublon : **0 doublons stricts détectés et validés visuellement**.

**Répartition de la pluie (tranches 5 mm)** :  
- 0–5 mm : **29.47 %**  
- 5–10 mm : **25.20 %**  
- 10–15 mm : **15.20 %**  
- 15–20 mm : **12.73 %**  
- 20–25 mm : **5.80 %**  
- 25–30 mm : **3.80 %**  
- 30–35 mm : **3.60 %**  
- 35–40 mm : **3.33 %**  
- 40–45 mm : **0.87 %**

**Répartition des humectations (tranches 4h)** :  
- 0–4 h : **9.20 %**  
- 4–8 h : **32.87 %**  
- 8–12 h : **32.73 %**  
- 12–16 h : **13.93 %**  
- 16–20 h : **7.87 %**  
- 20–24 h : **2.87 %**  
- 24–28 h : **0.53 %**

**Répartition des températures (tranches 5°C)** :  
- 9–14°C : **15.93 %**  
- 14–19°C : **30.80 %**  
- 19–24°C : **30.07 %**  
- 24–29°C : **23.07 %**  
- 29–34°C : **0.13 %**

Les statistiques montrent une distribution :
- réaliste du point de vue agro-météorologique,  
- diversifiée,  
- adaptée à l’apprentissage d’un modèle prédictif.

---

### 4. 🧪 Analyse du dataset de validation (initialement 150 lignes)

- Aucune incohérence interne.
- Même distribution qualitative que le train.
- **Un seul doublon strict détecté** par comparaison croisée avec le dataset train :
  - Ligne dupliquée identifiée puis **supprimée manuellement**.
- Taille finale : **149 lignes**

Cet ajustement n’impacte pas la validation :
- l’écart représente **3.3 %** de la classe *élevé*,  
- négligeable statistiquement,
- conforme à un usage réaliste en validation.

---

### 5. 🧹 Résultat final : datasets propres et validés

- **Train : 1500 lignes**, 0 doublon, distributions réalistes  
- **Validation : 149 lignes**, 0 doublon avec train  
- Conformité stricte au format Mistral `messages`  
- Cohérence biologique / météorologique soigneusement maîtrisée  
- Prêt pour Fine-Tuning LoRA / QLoRA

La rigueur du contrôle qualité garantit que le modèle apprendra sur des données :
- variées,  
- plausibles,  
- équilibrées,  
- non redondantes,  
- scientifiquement cohérentes.

Ces propriétés sont essentielles pour entraîner un modèle robuste et exploitable en agronomie.
