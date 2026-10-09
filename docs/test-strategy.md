# Stratégie de test

QuickOrder est une application web de commande de repas (Flask, SQLAlchemy, SQLite) avec une interface web et une API REST documentée en OpenAPI. Le but de la campagne est de dire si l'application peut partir en production. J'ai mis l'effort sur les parcours qui touchent à l'argent, aux données personnelles et aux droits d'accès.

## Périmètre

Je teste les fonctionnalités suivantes :

- l'authentification : inscription, connexion, déconnexion et redirection après login
- le catalogue : liste des restaurants, recherche, filtres (catégorie, végétarien, frais de livraison max) et tris
- la fiche restaurant : produits, stock, restaurant fermé, favoris, avis
- le panier : ajout, modification, suppression, vidage, et la règle "un seul restaurant par panier"
- les coupons : validité, expiration, montant minimum, plafond de réduction, cohérence avec la promo affichée
- le checkout et le paiement : adresse, carte, PayPal, espèces avec rendu de monnaie, minimum de commande, calcul des totaux
- les commandes : liste, détail, annulation, "recommander", et le fait qu'un client ne voit pas les commandes des autres
- l'administration : accès réservé, tableau de bord, changement de statut
- l'API REST : clé API, restaurants, produits, validation de coupon, création, lecture et annulation de commande, favoris

## Hors périmètre

Je ne fais pas de tests de charge ni de stress : l'application tourne en local sur SQLite avec un seul processus, ça ne dirait rien de la prod. Le paiement est simulé, donc je vérifie seulement les règles métier autour.

Je laisse aussi de côté le déploiement Docker et l'infra, un vrai pentest (je regarde juste les contrôles d'accès les plus évidents), l'accessibilité et l'affichage mobile, faute de temps. Pour la compatibilité, je me suis limité à Chrome sur PC.

## Parcours critiques

1. Commander et payer : restaurant, panier, coupon, checkout, paiement, puis confirmation.
2. Se connecter et accéder aux pages protégées.
3. Suivre sa commande et l'annuler quand le statut le permet.
4. Côté admin, faire avancer le statut d'une commande.
5. Commander via l'API, avec un total recalculé par le serveur.

## Risques

| ID | Risque | Conséquence |
|---|---|---|
| R1 | Total ou réduction mal calculé | Perte d'argent, litiges |
| R2 | Paiement en double, ou accepté sans être couvert | Client débité deux fois, perte de caisse |
| R3 | Commande sur un restaurant fermé, sous le minimum ou sans stock | Commande impossible à livrer, remboursements |
| R4 | Accès aux données ou aux actions d'un autre client | Fuite de données, fraude |
| R5 | Statuts de commande incohérents | Suivi faux, remboursements compliqués |
| R6 | Recherche ou filtres faux | Les clients ne trouvent pas, moins de commandes |
| R7 | Promo affichée différente de la promo appliquée | Publicité trompeuse |
| R8 | Avis non vérifiés | Notes manipulables |

## Types de tests

| Type | Sur quoi | Outils |
|---|---|---|
| Smoke | Démarrage, `/api/health`, accueil, login | Navigateur, curl |
| Fonctionnels UI (nominal, limite, erreur, règle métier) | Parcours critiques | Chrome et DevTools (Network, Console) |
| API | Codes HTTP, erreurs, contrôles côté serveur | Swagger, curl, collection Postman fournie |
| Sécurité ciblée | Contrôle d'accès, données envoyées par le client, redirections | Swagger, DevTools, modification d'URL |
| Régression | Cas en Fail, rejoués après correction | test-cases.xlsx |
| Exploratoire | API et fonctions secondaires (recherche, avis), hors des 24 cas | Swagger, curl, navigateur |
| Tests utilisateurs | 3 scénarios, 2 personnes | Observation et grille |

## Priorisation

Le détail est dans la [matrice des risques](risk-matrix.md). J'exécute d'abord les P1 (paiement, checkout, API commandes), puis les P2 (auth, admin, coupons), et en dernier les P3 (catalogue, avis, favoris).

## Critères d'entrée et de sortie

Je démarre la campagne quand l'application tourne, que la base a été remise à zéro avec `python seed.py`, que les comptes de démo marchent et que les smoke tests passent.

Pour dire Go, il faut que tous les cas P1 aient été exécutés, qu'aucun bug critique ou majeur ne reste ouvert sur le paiement, la commande ou la sécurité, et au moins 90 % de Pass au total.

## Environnement et données

Windows 11 25H2, Python 3, Chrome 154. La base SQLite est remise à zéro avant chaque session avec `seed.py`.

Les données de départ comptent 5 restaurants ouverts et un fermé (Night Sushi), quelques produits à stock 0 (Veggie Burger, Gyozas, Quinoa Bowl...) et les coupons `WELCOME10`, `SAVE20`, `LUNCH5`, `TOKYO30`, `ATLAS15` et `EXPIRED10`.
