# 📐 Méthodologie — Scouting Data TFC

## Vue d'ensemble du Framework

Le framework repose sur **3 couches de scoring** appliquées séquentiellement pour passer d'un pool brut de 175 joueurs à une shortlist de recommandations actionnables.

```
Pool L1 U25 (175 joueurs)
    │
    ▼
[Couche A] Score Opportunité — Fit sportif par archétype
    │
    ▼
[Couche B] Score Faisabilité — Réalisme d'accès au joueur
    │
    ▼
[Couche C] Score Final TFC — Compromis orienté faisabilité
    │
    ▼
Shortlist → Market Check → Recommandations Plan A/B/C
```

---

## Couche A — Score Opportunité (Fit Sportif)

### Principe

Chaque archétype de joueur est défini par des seuils de centiles sur des KPI précis. Les centiles sont calculés **au sein du pool Ligue 1 U25, par poste**, ce qui garantit une comparaison équitable.

### Archétypes & Filtres

#### 1. Créateur entre les lignes
- **Postes éligibles** : MF, MF-FW
- **Filtres d'entrée** : xAG_Pctl ≥ 80 ET PrgP_Pctl ≥ 60
- **Logique** : on cherche un joueur qui génère des occasions de but (xAG élevé) tout en ayant une capacité de progression par la passe (PrgP)
- **Score Opportunité** : combinaison pondérée des centiles (détail dans les formules DAX du dashboard)

#### 2. Porteur / Casseur de lignes
- **Postes éligibles** : MF, MF-FW, FW, FW-MF
- **Filtres d'entrée** : PrgC_Pctl ≥ 80 ET PrgP_Pctl ≥ 60
- **Logique** : profil qui casse les lignes adverses par la conduite de balle (PrgC très élevé) avec un complément de passes progressives
- **Score Opportunité** : combinaison pondérée des centiles

#### 3. Attaquant complet
- **Postes éligibles** : FW, FW-MF
- **Filtres d'entrée** : npxG_Pctl ≥ 50 ET xAG_Pctl ≥ 20
- **Logique** : attaquant qui marque (npxG) mais contribue aussi à la création (xAG minimum)
- **Formule Opportunité** :
  ```
  Opportunité_Attaquant = 0.75 × npxG_Pctl + 0.25 × xAG_Pctl
  ```

### Bonus d'Âge (Score_Age)

Un barème par tranche d'âge accorde un bonus aux joueurs les plus jeunes, reflétant :
- Le potentiel de progression
- La valeur de revente future
- L'alignement avec un projet de développement moyen terme

---

## Couche B — Score Faisabilité (Réalisme d'Accès)

### Principe

Un joueur peut être un fit sportif parfait, mais s'il est inaccessible au TFC (budget, club vendeur trop fort, joueur verrouillé), la recommandation n'a aucune valeur opérationnelle. La faisabilité évalue la probabilité réaliste de recruter.

### Composantes

```
Faisabilité ≈ 1/3 × Club_Tier_Score + 1/3 × Disponibilité + 1/3 × Score_Age
```

| Composante | Ce qu'elle mesure | Exemples |
|-----------|-------------------|----------|
| **Club Tier Score** | Force / exigence du club vendeur | PSG (Fort) → faible faisabilité vs Angers (Faible) → haute faisabilité |
| **Disponibilité** | Statut du joueur en club | Rotation / écarté → plus accessible que titulaire indiscutable |
| **Score Âge** | Réutilisé ici car les plus jeunes sont souvent plus accessibles via prêt | 19 ans → bonus vs 24 ans → neutre |

---

## Couche C — Score Final TFC

### Formule Globale

```
Score_Final_TFC = 0.70 × Faisabilité + 0.30 × Opportunité
```

### Justification du 70/30

Le compromis est **volontairement orienté faisabilité** :
- Le TFC opère avec un budget transfert ≤ 5M€
- Un joueur exceptionnel mais hors de portée ne sert pas le projet
- Un bon joueur réellement accessible a plus de valeur opérationnelle
- Le 30% d'opportunité garantit que le fit sportif reste un facteur significatif

### Résultat

La shortlist de la page 4 du dashboard = **Top 3 par archétype** après application du Score Final TFC.

---

## Market Check — Validation Business (Post-Shortlist)

Après la shortlist data-driven, une étape manuelle de validation marché est réalisée sur les joueurs retenus :

| Donnée vérifiée | Source | Objectif |
|-----------------|--------|----------|
| Valeur marchande (€) | Sources publiques (Transfermarkt, presse) | Vérifier la compatibilité budget |
| Statut en club | Observation / presse | Confirmer la disponibilité |
| Club vendeur (force) | Contexte sportif et financier | Évaluer le rapport de force en négociation |
| Fin de contrat | Sources publiques | Identifier les fenêtres d'opportunité |

### Classification en Plans A / B / C

- **Plan A** = Deal actionnable maintenant (transfert ≤ 5M€ ou prêt logique)
- **Plan B** = Bon fit mais dépend d'une fenêtre (pré-accord, été 2026, club tiers)
- **Plan C** = Watchlist / benchmark (trop cher ou peu probable, surveiller le contexte)

### Indice de Confiance

Chaque recommandation est assortie d'un niveau de confiance :
- **Haute** : données convergentes, deal réaliste, peu d'incertitude
- **Moyenne** : fit confirmé mais dépendant d'un facteur externe (fenêtre, club tiers)
- **Basse** : fit sportif intéressant mais deal peu probable en l'état
- **Très basse** : benchmark uniquement, à surveiller si le contexte évolue

---

## Résumé du Pipeline

```
175 joueurs L1 U25
    → Filtre 270 min
    → Calcul centiles par poste
    → Score Opportunité par archétype
    → Score Faisabilité (club + dispo + âge)
    → Score Final TFC (70% faisabilité / 30% opportunité)
    → Top 3 par archétype
    → Market Check manuel
    → Recommandations Plan A / B / C
    → 3 héros "Plan A" + 5 joueurs en suivi
```
