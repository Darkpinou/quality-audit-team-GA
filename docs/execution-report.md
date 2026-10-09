# Rapport d'exécution

Campagne menée le 09/10/2026 sur QuickOrder V3.1 (commit `d4606da`), avec une base remise à zéro par `python seed.py`. Environnement : Windows 11 25H2, Python 3, Chrome 154 avec les DevTools, et Swagger UI. Les cas sont dans [le fichier des cas de test](test-cases.xlsx) et les anomalies dans le dossier [bug-reports](bug-reports/).

## Résultats

Sur les 24 cas exécutés, 15 sont en Pass (62,5 %) et 9 en Fail (37,5 %). Aucun cas n'a été bloqué ni sauté. Les cas API en Pass (TC-N06, L02, L03, L04 et E03) ont été rejoués avec curl en fin de campagne et donnent le même résultat ([verif-cas-api.txt](evidence/verif-cas-api.txt)).

| Type de cas | Total | Pass | Fail |
|---|:-:|:-:|:-:|
| Nominal | 6 | 6 | 0 |
| Limite | 6 | 4 | 2 |
| Erreur | 6 | 4 | 2 |
| Règle métier | 6 | 1 | 5 |

| Priorité | Total | Pass | Fail |
|---|:-:|:-:|:-:|
| P1 | 10 | 4 | 6 |
| P2 | 12 | 10 | 2 |
| P3 | 2 | 1 | 1 |

Quand on suit le parcours normal, tout marche (6 cas nominaux sur 6). Par contre, 5 règles métier sur 6 échouent, et 6 cas P1 sur 10 (paiement, commande, API) sont en Fail. En regardant le code, on voit que la plupart de ces défauts viennent du serveur, qui ne vérifie pas les règles avant d'enregistrer la commande.

## Anomalies

J'ai ouvert 14 bugs. Deux sont critiques : la double commande avec PayPal (BUG-01) et l'API qui agit au nom d'Alice sans authentification (BUG-07). Sept sont majeurs (BUG-02 à 06, 08 et 09), quatre sont moyens (BUG-10, 12, 13 et 14) et un est mineur (BUG-11). BUG-07 et BUG-11 à 14 ont été trouvés en test exploratoire, en dehors des 24 cas.

J'ai aussi relevé des points de sécurité que je n'ai pas transformés en bug report. Après connexion, le paramètre `next` accepte une URL externe (`/login?next=https://exemple.com`), ce qui permet une redirection ouverte utilisable pour du phishing. Un compte créé par `/register` reçoit une adresse par défaut vide qu'on peut quand même choisir au checkout. Enfin, il n'y a pas de protection CSRF sur les formulaires et la déconnexion se fait en GET.

## Principaux risques

Le plus gros risque est financier. Un client qui paie avec PayPal est débité deux fois (BUG-01), un paiement en espèces passe même si le montant donné est trop faible (BUG-02), et l'API applique un coupon expiré (BUG-06).

Il y a aussi un risque opérationnel : le site accepte des commandes que le restaurant ne pourra pas honorer, parce qu'il est fermé, que le minimum n'est pas atteint ou que le produit est en rupture (BUG-03, 04 et 05).

Côté sécurité, n'importe qui avec la clé API publique peut lire, créer et annuler les commandes d'un vrai client (BUG-07), et l'API calcule les remises sur un montant envoyé par le client (BUG-14). Pour finir, la promo ATLAS15 annonce -15 % mais n'en donne que 10 (BUG-09), et un admin peut faire revenir une commande livrée en arrière (BUG-08). Les notes des restaurants ne sont pas fiables non plus, puisque n'importe quel compte peut laisser autant d'avis qu'il veut sans avoir commandé (BUG-12).

## Recommandation : No-Go

Les critères de sortie fixés dans la [stratégie](test-strategy.md) ne sont pas atteints. J'ai 62,5 % de Pass alors que je visais au moins 90 %, et il reste 2 anomalies critiques et 5 majeures sur le paiement, la commande et la sécurité.

Pour passer en Go, il faudrait corriger et revalider BUG-01 à BUG-07, rejouer les 24 cas en régression, et ajouter des tests pytest sur les règles du checkout (minimum, restaurant ouvert, stock, espèces, coupon expiré, PayPal) pour que ces bugs ne reviennent pas.

BUG-08 à BUG-14 peuvent attendre après la mise en production. Pour BUG-09 je conseille quand même un correctif rapide, vu que c'est de la publicité trompeuse.
