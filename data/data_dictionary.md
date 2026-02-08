# 📖 Dictionnaire de Données

## Vue d'ensemble

| Paramètre | Valeur |
|-----------|--------|
| Population | Joueurs Ligue 1, U25 |
| Saison | 2025–2026 |
| Taille du dataset | ~175 joueurs |
| Unité d'analyse | Joueur |
| Filtre de fiabilité | ≥ 270 minutes jouées |

---

## Variables du Dataset

### Identité & Contexte

| Variable | Type | Description |
|----------|------|-------------|
| `Player` | Texte | Nom du joueur |
| `Age` | Entier | Âge du joueur (≤ 25 ans) |
| `Club` | Texte | Club actuel en Ligue 1 |
| `Pos` | Texte | Poste principal (MF, MF-FW, FW, FW-MF, etc.) |
| `Min` | Entier | Minutes jouées sur la saison (filtre ≥ 270) |

### Centiles de Performance (calculés par poste, pool L1 U25)

| Variable | Type | Description |
|----------|------|-------------|
| `xAG_Pctl` | Décimal [0–100] | Centile Expected Assisted Goals — capacité de création de chances |
| `PrgP_Pctl` | Décimal [0–100] | Centile Passes progressives — progression verticale par la passe |
| `PrgC_Pctl` | Décimal [0–100] | Centile Conduites progressives — progression verticale balle au pied |
| `npxG_Pctl` | Décimal [0–100] | Centile Non-penalty Expected Goals — qualité des occasions créées pour soi |

### Scores Calculés

| Variable | Type | Description |
|----------|------|-------------|
| `Score_Age` | Décimal | Bonus jeunesse (barème par tranche d'âge, favorise les plus jeunes) |
| `Score_Opportunite` | Décimal | Fit sportif par archétype (formule spécifique à chaque profil) |
| `Score_Faisabilite` | Décimal | Réalisme d'accès (~1/3 Club Tier + ~1/3 Disponibilité + ~1/3 Âge) |
| `Score_Final_TFC` | Décimal | Score composite (70% Faisabilité + 30% Opportunité) |
| `Club_Tier_Score` | Décimal | Classification du club vendeur (Fort / Moyen / Faible) |

### Market Check (données externes manuelles)

| Variable | Type | Description |
|----------|------|-------------|
| `Valeur_Marche` | Décimal (€) | Valeur marchande estimée (source : données publiques web) |
| `Statut_Club` | Texte | Titulaire régulier / Rotation / Écarté |
| `Club_Vendeur` | Texte | Force du club vendeur : Fort / Moyen / Faible |
| `Fin_Contrat` | Entier (année) | Année de fin de contrat |
| `Recommandation` | Texte | Plan A / B / C + type de deal (transfert / prêt) |
| `Confiance` | Texte | Haute / Moyenne / Basse / Très basse |
| `Notes` | Texte | Justification et actions recommandées |

---

## Notes sur la Qualité des Données

- Les centiles sont calculés **au sein du pool Ligue 1 U25 par poste** — un centile de 80 signifie "top 20% des U25 de Ligue 1 à ce poste"
- Certaines erreurs de classification de poste existent dans la source (ex : Fodé Doucouré classé MF au lieu de DD)
- Le seuil de 270 minutes (≈ 3 matchs complets) est un compromis entre représentativité statistique et couverture du pool
- Les valeurs marché et données contractuelles sont des estimations issues de sources publiques, collectées en janvier 2026
