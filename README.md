# ⚽ Analyse de Recrutement Data-Driven — Ligue 1 U25 (Cas TFC)

> Identifier des profils "fit sportif", filtrer par réalisme marché, et produire un plan de recrutement actionnable pour le Toulouse FC — le tout dans un budget ≤ 5M€.

![Status](https://img.shields.io/badge/status-completed-brightgreen)
![Power BI](https://img.shields.io/badge/Power%20BI-F2C811?style=flat&logo=powerbi&logoColor=black)
![DAX](https://img.shields.io/badge/DAX-0078D4?style=flat&logo=microsoft&logoColor=white)
![Football Analytics](https://img.shields.io/badge/Football%20Analytics-2E8B57?style=flat)

---

## 👤 À propos de ce projet

Je suis **Antoine Moineaux**, en reconversion professionnelle vers la data analyse. Ce projet est le **3e de mon portfolio**, et le premier construit autour d'un sujet qui me passionne : le football.

Chacun de mes projets démontre une facette différente du métier de data analyst :

| # | Projet | Compétences démontrées |
|---|--------|----------------------|
| 1 | [Mobility Operations Performance](https://github.com/AntoineMoineaux/mobility-operations-performance) | SQL, Power BI, analyse opérationnelle, KPIs métier |
| 2 | [Fitness Retention Analysis](https://github.com/AntoineMoineaux/fitness-retention-analysis) | Product analytics, rétention utilisateur, analyse comportementale |
| **3** | **Ce projet — TFC Recruitment Analysis** | **Scoring framework custom, DAX avancé, aide à la décision, domain expertise** |

Ce 3e projet montre ma capacité à **concevoir un framework analytique de bout en bout** : de la définition du problème business jusqu'à des recommandations actionnables, en passant par la construction d'un modèle de scoring multi-couches en DAX. C'est aussi un projet passion qui illustre ce que je peux apporter quand je combine rigueur analytique et connaissance du domaine.

📫 **Me contacter** : [LinkedIn](https://www.linkedin.com/in/<www.linkedin.com/in/antoine-moineaux-37a0b6189>) · [Email](mailto:<antoine.moineaux@gmail.com>)

---

## TL;DR

**175 joueurs U25 de Ligue 1** analysés sur la saison 2025-2026 (mercato hivernal, janvier 2026). Un framework en 3 couches — opportunité sportive, faisabilité marché, score final — aboutit à **3 recommandations "Plan A"** actionnables immédiatement pour le TFC :

| Joueur | Club | Deal recommandé | Confiance |
|--------|------|-----------------|-----------|
| Rémy Labeau Lascary | Brest | Transfert ≤ 5M€ | 🟢 Haute |
| Mohamed Amine Sbai | Angers | Transfert ≤ 5M€ | 🟢 Haute |
| Quentin Ndjantou Mbitcha | PSG | Prêt sec | 🟢 Haute |

---

## 📋 Sommaire

- [Contexte & Question Business](#contexte--question-business)
- [Dataset & Périmètre](#dataset--périmètre)
- [Méthode : Pipeline en 4 Étapes](#méthode--pipeline-en-4-étapes)
- [Pages du Dashboard](#pages-du-dashboard)
- [Résultats Clés](#résultats-clés)
- [Limitations & Data Quality](#limitations--data-quality)
- [Next Steps](#next-steps)
- [Structure du Repo](#structure-du-repo)
- [How to Run](#how-to-run)
- [Compétences Démontrées](#compétences-démontrées)

---

## Contexte & Question Business

**Problème** : Comment un club à budget limité (TFC, enveloppe transfert ≤ 5M€) peut-il identifier et recruter des talents U25 en Ligue 1 pendant le mercato hivernal 2026, en combinant analyse de performance et réalisme de marché ?

**Approche** : Construire une méthode de scouting data qui :
1. Définit des archétypes de joueurs recherchés (créateur, porteur de balle, attaquant complet)
2. Score chaque joueur sur son fit sportif via des centiles de performance
3. Évalue la faisabilité réelle du recrutement (tier du club, statut, contrat, âge)
4. Produit une shortlist priorisée avec des recommandations de deal concrètes (transfert / prêt / watchlist)

**Output** : Dashboard Power BI de 6 pages + table Market Check avec recommandation par joueur.

---

## Dataset & Périmètre

| Paramètre | Valeur |
|-----------|--------|
| **Population** | Joueurs de Ligue 1, focus U25 |
| **Saison** | 2025–2026 (données au mercato hivernal, janvier 2026) |
| **Taille** | ~175 joueurs analysés |
| **Unité d'analyse** | Joueur (âge, minutes, poste, club, centiles de performance) |
| **Filtre de fiabilité** | Minimum 270 minutes jouées (≈ 3 matchs complets) |
| **Segmentation** | Par poste (MF, MF-FW, FW, FW-MF) |

---

## Méthode : Pipeline en 4 Étapes

### Étape 1 — Définir les archétypes cibles

Trois profils recherchés, chacun avec des seuils de centiles spécifiques (calculés au sein du pool Ligue 1 U25, par poste) :

| Archétype | Postes éligibles | Critères clés |
|-----------|-----------------|---------------|
| **Créateur entre les lignes** | MF, MF-FW | xAG_Pctl ≥ 80 **ET** PrgP_Pctl ≥ 60 |
| **Porteur / Casseur de lignes** | MF, MF-FW, FW, FW-MF | PrgC_Pctl ≥ 80 **ET** PrgP_Pctl ≥ 60 |
| **Attaquant complet** | FW, FW-MF | npxG_Pctl ≥ 50 **ET** xAG_Pctl ≥ 20 |

**Métriques utilisées** :
- **xAG_Pctl** — Centile Expected Assisted Goals (création de chances)
- **PrgP_Pctl** — Centile Passes progressives (progression verticale par la passe)
- **PrgC_Pctl** — Centile Conduites progressives (progression verticale par le dribble)
- **npxG_Pctl** — Centile Non-penalty Expected Goals (qualité des occasions)

### Étape 2 — Scorer l'opportunité sportive

Chaque archétype a sa propre formule de scoring. Exemple pour l'attaquant complet :

```
Opportunité Attaquant = 0.75 × npxG_Pctl + 0.25 × xAG_Pctl
```

Un bonus d'âge (Score_Age) récompense les profils les plus jeunes, à potentiel de revente ou de développement.

### Étape 3 — Évaluer la faisabilité

```
Faisabilité = ~1/3 Club Tier Score + ~1/3 Disponibilité + ~1/3 Score Âge
```

Le score de faisabilité estime la probabilité réaliste de recruter le joueur dans un contexte de budget limité (club vendeur fort/moyen/faible, joueur titulaire ou en rotation, durée de contrat restante).

### Étape 4 — Calculer le Score Final TFC

```
Score Final = 0.70 × Faisabilité + 0.30 × Opportunité
```

Le compromis est orienté faisabilité : dans un contexte TFC à budget contraint, un très bon joueur inaccessible vaut moins qu'un bon joueur réellement recrutables. La shortlist = Top 3 par archétype après ce scoring.

---

## Pages du Dashboard

Le dashboard raconte une histoire en 6 pages, du contexte global jusqu'à la recommandation finale :

### Page 1 — Synthèse Exécutive
Vue d'ensemble du projet, message clé et recommandation principale à destination de la direction sportive.

![Page 1 — Synthèse Exécutive](dashboard/page1_synthese.png)

### Page 2 — Profils Cibles & Critères
Définition des 3 archétypes, KPI utilisés, seuils retenus et justification des choix méthodologiques.

![Page 2 — Profils Cibles](dashboard/page2_profils.png)

### Page 3 — Opportunités Sportives
Classement "fit sportif" brut avant toute considération de marché. Visualisation des centiles par joueur et par archétype.

![Page 3 — Opportunités Sportives](dashboard/page3_opportunites.png)

### Page 4 — Shortlist Réaliste (Opportunité × Faisabilité)
Top 3 par archétype après croisement des scores. Règles de lecture et décisions Plan A / B / C.

![Page 4 — Shortlist Réaliste](dashboard/page4_shortlist.png)

### Page 5 — Validation Marché & Type de Deal
Ajout des signaux marché (valeur, statut, contrat, vendeur). 3 cartes "héros" pour les Plan A + table Market Check complète avec tooltips.

![Page 5 — Validation Marché](dashboard/page5_market.png)

### Page 6 — Shortlist Finale & Recommandations
Synthèse finalisée : qui prendre, comment (transfert/prêt), et pourquoi. Plan d'action priorisé.

![Page 6 — Shortlist Finale](dashboard/page6_finale.png)

---

## Résultats Clés

### 🏆 3 Recommandations "Plan A" — Actionnables immédiatement

**Rémy Labeau Lascary** (Brest) — Transfert ≤ 5M€
- Contrat expirant en 2026 = fenêtre d'opportunité claire
- Confiance : 🟢 Haute

**Mohamed Amine Sbai** (Angers) — Transfert ≤ 5M€
- Valeur estimée ~1.5M€, club en position de vendeur
- Confiance : 🟢 Haute

**Quentin Ndjantou Mbitcha** (PSG) — Prêt sec
- Logique de développement / besoin de minutes, profil cohérent pour un prêt
- Confiance : 🟢 Haute

### 📋 Market Check — Pool complet

| Joueur | Club actuel | Plan | Type de deal | Confiance |
|--------|-------------|------|-------------|-----------|
| Rémy Labeau Lascary | Brest | **A** | Transfert ≤ 5M€ | 🟢 Haute |
| Mohamed Amine Sbai | Angers | **A** | Transfert ≤ 5M€ | 🟢 Haute |
| Quentin Ndjantou Mbitcha | PSG | **A** | Prêt sec | 🟢 Haute |
| Dermane Karim | Lorient (prêt Lommel) | **B** | Transfert (pré-accord / fenêtre Lommel) | 🟡 Moyenne |
| Ibou Sané | Metz (prêt Amiens) | **B** | Transfert été 2026 (si descente Metz) | 🟡 Moyenne |
| Khalis Merah | Lyon | **C** | Prêt (peu probable, effectif OL restreint) | 🔴 Basse |
| Prosper Peter | Angers | **C** | Transfert (valeur réelle prob. > 10M€) | ⚫ Très basse |
| Kamory Doumbia | Brest | **C** | Transfert (trop cher > 5M€, benchmark) | ⚫ Très basse |

### Définition des Plans

- **Plan A** = Deal actionnable maintenant (transfert ≤ 5M€ ou prêt logique)
- **Plan B** = Bon fit mais dépend d'une fenêtre (pré-accord, été 2026, club tiers)
- **Plan C** = Watchlist / benchmark (trop cher ou peu probable, surveiller le contexte)

---

## Limitations & Data Quality

- **Erreurs de position** : certaines données de poste comportent des inexactitudes (ex : Fodé Doucouré classé MF alors qu'il joue plutôt DD). Des filtres visuels et une prudence sur la variable "Pos" ont été maintenus.
- **Seuil de minutes (270)** : réduit le bruit statistique mais n'élimine pas tous les biais liés aux petits échantillons.
- **Valeurs marché & contrats** : données externes estimatives (sources web), dépendantes du timing de collecte.
- **Modèle = aide à la décision** : ce framework est un outil d'aide au scouting, pas une vérité absolue. Il doit être complété par du visionnage vidéo et de l'expertise terrain.

---

## Next Steps

- Élargir le périmètre à d'autres ligues (Ligue 2, Eredivisie, Liga Portugal) pour augmenter le pool de comparaison
- Intégrer des données défensives (pressings, duels, récupérations) pour couvrir les profils défensifs
- Automatiser la collecte Market Check via API (Transfermarkt, FBref)
- Ajouter un axe "progression sur N saisons" pour évaluer les trajectoires de développement
- Inclure un module de comparaison directe (radar charts joueur vs archétype idéal)

---

## Structure du Repo

```
📂 tfc-recruitment-analysis/
├── 📄 README.md                  ← Ce fichier
├── 📂 dashboard/                 ← Screenshots des 6 pages du dashboard
│   ├── page1_synthese.png
│   ├── page2_profils.png
│   ├── page3_opportunites.png
│   ├── page4_shortlist.png
│   ├── page5_market.png
│   └── page6_finale.png
├── 📂 data/                      ← Sources et dictionnaire de données
│   └── data_dictionary.md
├── 📂 powerbi/                   ← Fichier Power BI
│   └── README.md
├── 📂 docs/                      ← Documentation méthodologique
│   ├── methodology.md
│   ├── kpi_definitions.md
│   └── market_check.md
└── 📂 assets/                    ← Logos, icônes, visuels
    └── README.md
```

---

## How to Run

1. **Cloner le repo**
   ```bash
   git clone https://github.com/AntoineMoineaux/tfc-recruitment-analysis.git
   ```

2. **Ouvrir le dashboard**
   - Ouvrir le fichier `.pbix` dans Power BI Desktop (si disponible dans `/powerbi/`)
   - Sinon, consulter les screenshots dans `/dashboard/`

3. **Explorer la documentation**
   - Méthodologie complète → [`docs/methodology.md`](docs/methodology.md)
   - Définitions des KPI → [`docs/kpi_definitions.md`](docs/kpi_definitions.md)
   - Table Market Check détaillée → [`docs/market_check.md`](docs/market_check.md)

---

## Compétences Démontrées

Ce projet illustre les compétences suivantes, directement transférables en poste de data analyst :

| Compétence | Application dans ce projet |
|-----------|---------------------------|
| **Power BI & DAX avancé** | Dashboard 6 pages, mesures DAX custom, scoring multi-couches |
| **Conception de framework analytique** | Modèle Opportunité × Faisabilité en 3 couches, pondérations argumentées |
| **Storytelling data** | Narration progressive du dashboard : contexte → analyse → recommandation |
| **Aide à la décision** | Recommandations Plan A/B/C avec niveaux de confiance et justifications |
| **Domain expertise** | Connaissance des métriques football (xAG, npxG, PrgC) et du contexte marché |
| **Rigueur méthodologique** | Documentation des limites, seuils de fiabilité, transparence sur la qualité des données |

---

> *Ce projet est un cas d'école en scouting data. Les recommandations sont basées sur un modèle analytique et ne constituent pas des vérités club. Les valeurs marché et données contractuelles sont des estimations issues de sources publiques.*
