# Matrice risque x impact

Chaque fonctionnalité reçoit une probabilité de défaut (de 1 = faible à 3 = forte) et un impact (de 1 = faible à 3 = critique). Le score est le produit des deux.

Un score de 9 donne une P1 et un score de 2 ou moins une P3. Pour les scores intermédiaires, j'ai ajusté selon le parcours, comme expliqué dans la [stratégie](test-strategy.md). Un score de 6 donne normalement une P2, mais je le passe en P1 quand le risque touche aux données ou aux actions d'un autre client. Un score de 4 donne une P3, sauf si la fonctionnalité est sur le parcours de commande, auquel cas je le passe en P2.

| Fonctionnalité | Probabilité | Impact | Score | Priorité | Pourquoi |
|---|:-:|:-:|:-:|:-:|---|
| Paiement (carte, PayPal, espèces) | 3 | 3 | 9 | P1 | De l'argent en jeu, un traitement différent par moyen de paiement |
| Checkout (minimum, restaurant ouvert, stock) | 3 | 3 | 9 | P1 | Beaucoup de règles métier, commande impossible à livrer si l'une saute |
| API de création de commande | 3 | 3 | 9 | P1 | Exposée, passe à côté de l'interface et recalcule les totaux |
| Contrôle d'accès API et commandes | 2 | 3 | 6 | P1 | Fuite de données, actions au nom d'un autre client |
| Coupons | 3 | 2 | 6 | P2 | Pourcentage, minimum, plafond, expiration : beaucoup de calculs |
| Statuts de commande (admin) | 2 | 3 | 6 | P2 | Suivi client et remboursements |
| Authentification | 2 | 3 | 6 | P2 | Porte d'entrée, redirection après login |
| Panier | 2 | 2 | 4 | P2 | Quantités, un seul restaurant par panier |
| Recherche, filtres, tris | 2 | 2 | 4 | P3 | Gêne pour trouver un restaurant |
| Avis et notes | 2 | 2 | 4 | P3 | Notes manipulables |
| Profil et adresses | 1 | 2 | 2 | P3 | Une adresse vide empêche la livraison |
| Favoris | 1 | 1 | 1 | P3 | Fonction secondaire |
| Affichage, responsive | 1 | 1 | 1 | P3 | Confort |

Placées dans la grille (impact en ligne, probabilité en colonne) :

| Impact \ Probabilité | 1 | 2 | 3 |
|---|---|---|---|
| 3 (critique) | | Auth, accès API, statuts admin | Paiement, checkout, API commandes |
| 2 | Profil | Panier, recherche, avis | Coupons |
| 1 | Favoris, affichage | | |

Plus une fonctionnalité est en haut à droite, plus je lui ai consacré de cas de test et de preuves. Au final, 22 des 24 cas portent sur des zones P1 ou P2.
