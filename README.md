# Audit qualité de QuickOrder V3.1

Audit QA de l'application QuickOrder (Flask / SQLite) avant sa mise en production, fait dans le cadre du cours *Tests et qualité des solutions numériques*.

J'ai audité mon fork `quickorder-qa-project_GA` (commit `d4606da`) sans modifier son code. L'application tourne en local sur `http://127.0.0.1:5000`, avec le Swagger sur `/docs/` et la clé API `quickorder-demo-key`. Les comptes utilisés sont `alice@example.com` et `bob@example.com` (mot de passe `Password123!`) et l'admin `admin@quickorder.local` (`Admin123!`).

## Contenu du dossier

- [Stratégie de test](docs/test-strategy.md) : périmètre, parcours critiques, risques et types de tests
- [Matrice risque x impact](docs/risk-matrix.md)
- [Cas de test](docs/test-cases.xlsx) : 24 cas (6 nominaux, 6 limites, 6 erreurs, 6 règles métier) avec leur résultat
- [Rapport d'exécution](docs/execution-report.md) : bilan de la campagne et décision Go / No-Go
- [Bug reports](docs/bug-reports/) : 14 anomalies, preuves dans `docs/evidence/`
- [Tests utilisateurs](docs/user-feedback.md) : 3 scénarios passés par 2 testeurs, plus un pré-test
- [Plan d'amélioration](docs/improvement-plan.md) : 11 améliorations priorisées

## Démarche

J'ai commencé par lire le brief, la doc (README, API.md, openapi.yaml) et le code pour comprendre le produit. Ça m'a permis de lister les parcours critiques (auth, panier, coupons, checkout et paiement, commandes, admin, API) et de les classer avec la matrice risque x impact.

J'ai préparé la stratégie et écrit les cas de test sur les jours précédant la campagne, puis je les ai exécutés le 09/10/2026 dans le navigateur (avec les DevTools) et dans Swagger, parfois avec curl. Chaque anomalie a sa preuve et la ligne de code qui pose problème. J'ai fini par les tests utilisateurs, l'analyse des résultats et le plan d'amélioration.

## Recommandation

Je recommande de ne pas mettre en production pour l'instant (No-Go). J'ai trouvé des anomalies bloquantes sur le paiement (double commande avec PayPal, paiement en espèces accepté même avec un montant trop faible), sur les règles de commande (restaurant fermé, minimum, stock) et sur la sécurité de l'API. Le détail est dans le [rapport d'exécution](docs/execution-report.md).
