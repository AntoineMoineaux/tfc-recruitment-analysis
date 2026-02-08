# 📊 Définitions des KPI

## Métriques de Performance (Centiles)

Tous les centiles sont calculés **au sein du pool Ligue 1 U25, segmentés par poste**. Un centile de 80 signifie que le joueur performe mieux que 80% des U25 de Ligue 1 jouant au même poste.

---

### xAG_Pctl — Expected Assisted Goals (centile)

**Ce que ça mesure** : La capacité du joueur à créer des occasions de but pour ses coéquipiers, mesurée par la qualité des passes décisives attendues.

**Pourquoi c'est pertinent** : Identifie les créateurs — les joueurs qui génèrent des chances de qualité, indépendamment de la finition de leurs coéquipiers.

**Utilisé pour** : Archétypes "Créateur entre les lignes" et "Attaquant complet"

---

### PrgP_Pctl — Passes Progressives (centile)

**Ce que ça mesure** : Le volume de passes qui font avancer significativement le ballon vers le but adverse (passes progressives selon la définition StatsBomb/FBref).

**Pourquoi c'est pertinent** : Distingue les joueurs qui font avancer le jeu par la passe, des joueurs qui recyclent le ballon latéralement.

**Utilisé pour** : Tous les archétypes (critère transversal de progression)

---

### PrgC_Pctl — Conduites Progressives (centile)

**Ce que ça mesure** : Le volume de conduites de balle qui font avancer significativement le ballon vers le but adverse.

**Pourquoi c'est pertinent** : Identifie les "casseurs de lignes" — les joueurs capables de porter le ballon à travers les lignes adverses et de créer du danger par le dribble et la conduite.

**Utilisé pour** : Archétype "Porteur / Casseur de lignes"

---

### npxG_Pctl — Non-Penalty Expected Goals (centile)

**Ce que ça mesure** : La qualité et le volume des occasions de but du joueur, hors penalties. Reflète sa capacité à se créer ou à se retrouver dans des situations de frappe de qualité.

**Pourquoi c'est pertinent** : Évalue la menace offensive réelle du joueur, sans le biais des penalties qui dépendent de la hiérarchie au sein du club.

**Utilisé pour** : Archétype "Attaquant complet"

---

## Scores Composites

### Score_Age — Bonus Jeunesse

Barème par tranche d'âge qui accorde un bonus aux joueurs les plus jeunes. Reflète le potentiel de progression, la valeur de revente et l'alignement avec un projet de développement.

### Score_Opportunite — Fit Sportif

Score composite propre à chaque archétype, combinant les centiles pertinents avec des pondérations spécifiques. Exemple pour l'attaquant complet : `0.75 × npxG_Pctl + 0.25 × xAG_Pctl`.

### Score_Faisabilite — Réalisme d'Accès

Combinaison de ~1/3 Club Tier Score + ~1/3 Disponibilité + ~1/3 Score Âge. Estime la probabilité réaliste de recruter le joueur dans le contexte budgétaire du TFC.

### Score_Final_TFC — Score de Décision

`0.70 × Faisabilité + 0.30 × Opportunité` — compromis orienté faisabilité, reflétant la contrainte budgétaire du TFC.

---

## Seuils par Archétype (Résumé)

| Archétype | KPI principal | Seuil | KPI secondaire | Seuil |
|-----------|--------------|-------|----------------|-------|
| Créateur entre les lignes | xAG_Pctl | ≥ 80 | PrgP_Pctl | ≥ 60 |
| Porteur / Casseur de lignes | PrgC_Pctl | ≥ 80 | PrgP_Pctl | ≥ 60 |
| Attaquant complet | npxG_Pctl | ≥ 50 | xAG_Pctl | ≥ 20 |
