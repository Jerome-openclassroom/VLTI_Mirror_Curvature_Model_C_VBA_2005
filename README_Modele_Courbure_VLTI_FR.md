# Modélisation pression-courbure d’un miroir à courbure variable – Stage LAM (2005)

Ce projet a été réalisé entre février et avril 2005 au **Laboratoire d'Astrophysique de Marseille (LAM)**, unité mixte de recherche **CNRS / Université de Provence**. Il s’inscrit dans le cadre du **VLTI (Very Large Telescope Interferometer)**, programme de l’**ESO (European Southern Observatory)**.  
🔗 Voir aussi : [Très Grand Télescope (VLT) – Wikipédia](https://fr.wikipedia.org/wiki/Tr%C3%A8s_Grand_T%C3%A9lescope)

---

## 🎯 Objectif du stage

Développer et valider un **modèle mathématique** simulant la relation entre la **pression appliquée** sur une fine lame d’acier et la **courbure résultante** du miroir, en tenant compte de l’**hystérèse**.

Ce modèle devait être testé expérimentalement et implémenté sous deux environnements :

- En **C ANSI** sous Linux (avec graphiques via PGPLOT)
- En **VBA** sous Excel pour Windows (avec affichage dynamique)

---

## 🔬 Contexte scientifique

Le **VLTI**, installé sur le mont Paranal (Chili), combine les faisceaux de **quatre télescopes de 8,2 m** pour simuler un instrument virtuel de très grande taille. L’ajustement fin des chemins optiques repose sur des **miroirs à courbure variable (VCM)**, installés sur des chariots dans des lignes à retard de 65 m.

Ces miroirs doivent :
- Ajuster leur courbure en temps réel selon la pression appliquée.
- Compenser les variations dues à la rotation terrestre.
- Maintenir la cohérence des fronts d’onde.

---

## 🧮 Modélisation

### Courbe de montée en pression
La relation pression-courbure est modélisée par un polynôme d’ordre 5 :
```math
P = A₁(C - C₀) + A₃(C - C₀)³ + A₅(C - C₀)⁵
```
> Où :  
> - \(P\) est la pression appliquée,  
> - \(C\) la courbure,  
> - \(C₀\) la courbure à vide,  
> - \(A_1\), \(A_3\), \(A_5\) des constantes caractéristiques du miroir.

### Courbe de descente avec hystérèse

Une correction \(\Delta P\) est appliquée :
```math
ΔP = Δ₁·C + Δ₃·C³ + Δ₅·C⁵
```
> Coefficients dérivés de :
> - \(P_{seq}\) (pression maximale atteinte)
> - Paramètres \( lpha\), \(eta\), \(\gamma\)

---

## 💻 Développement logiciel

### Version Linux (C ANSI)
- Utilisation de `stdio.h`, `math.h`, `pgplot`.
- Fichiers de données : `fi_fi.dat`, `fizeau-mXX.dat`
- Affichage des courbes :
  - Montée en pression
  - Descente corrigée par hystérèse
  - Représentation de ΔP vs P

### Version Windows (VBA Excel)
- Interface via macros
- Sélection du miroir
- Entrée de \(P_{seq}\)
- Courbes interactives dans les cellules

---

## 🧪 Validation expérimentale

Deux instruments ont permis de tester le modèle :

- **Analyseur de Shack-Hartmann** : mesure CCD via laser He-Ne (50 points par test)
- **Analyseur de Fizeau** : mesure de référence indépendante

> Résultats :
> - Courbes expérimentales cohérentes avec les prédictions
> - Hystérèse bien reproduite
> - Modèle fiable sur plusieurs miroirs

---

## 🧰 Système embarqué (VLTI)

Les miroirs sont contrôlés par un système embarqué incluant :

- **Piezo-translateur** (résolution nanométrique)
- **Microprocesseur PIP6 (CMOS)** sous Linux Debian
- **LCU** sous VxWorks (liaison RS232 infrarouge)
- **Manomètre + encodeur + moteur à courant continu**
- **Valve de sécurité en cas de surpression**

---

## 📈 Résultats

- Calculs et tracés des courbes pression-courbure (montée et descente)
- Représentation de l’effet d’hystérèse
- Interface utilisateur multiplateforme
- **Validation expérimentale** confirmée

---

## ✅ Conclusion

Ce stage a permis :
- La **mise au point d’un modèle opérationnel** pour la commande de miroirs adaptatifs.
- Le développement d’**outils logiciels fiables** en C et VBA.
- La confrontation du modèle à des **données physiques réelles** dans un contexte de haute précision.

Le travail effectué contribue à la **précision optique** du VLTI et constitue une **expérience fondatrice en instrumentation scientifique**.

---

**Auteur** : Jérôme Frasson  
**Période** : Février – Avril 2005  
**Lieu** : LAM (CNRS – Université de Provence)
