# Bug reports

Tous les bugs ont été reproduits sur QuickOrder V3.1 (commit d4606da) en local (http://127.0.0.1:5000), sous Windows 11 25H2 avec Chrome 154, à partir d'une base remise à zéro par `python seed.py`. Chaque fiche précise seulement le compte et l'interface utilisés.

| ID | Titre | Sévérité | Priorité | Cas |
|---|---|---|---|---|
| [BUG-01](BUG-01-paypal-double-commande.md) | Un paiement PayPal crée deux commandes identiques et payées | Critique | P1 | TC-R04 |
| [BUG-02](BUG-02-especes-insuffisantes.md) | Paiement en espèces accepté avec un montant inférieur au total | Majeure | P1 | TC-E04 |
| [BUG-03](BUG-03-restaurant-ferme-commandable.md) | Un restaurant fermé peut recevoir des commandes | Majeure | P1 | TC-R02 |
| [BUG-04](BUG-04-minimum-commande-ignore.md) | Le minimum de commande affiché n'est pas appliqué | Majeure | P1 | TC-R03 |
| [BUG-05](BUG-05-stock-non-controle.md) | Le stock n'est pas contrôlé : produits épuisés commandables, stock négatif | Majeure | P1 | TC-L05 |
| [BUG-06](BUG-06-api-coupon-expire.md) | L'API de commande applique un coupon expiré | Majeure | P1 | TC-E05 |
| [BUG-07](BUG-07-api-identite-par-defaut.md) | L'API agit au nom d'Alice pour tout détenteur de la clé API | Critique | P1 | - |
| [BUG-08](BUG-08-transitions-statut-admin.md) | L'admin peut faire revenir une commande livrée à un statut antérieur | Majeure | P2 | TC-R06 |
| [BUG-09](BUG-09-atlas15-taux-errone.md) | Le code ATLAS15 annoncé à -15 % n'applique que -10 % | Majeure | P2 | TC-R05 |
| [BUG-10](BUG-10-filtre-livraison-inverse.md) | Le filtre "frais de livraison max" renvoie les restaurants les plus chers | Moyenne | P2 | TC-L06 |
| [BUG-11](BUG-11-recherche-description.md) | La recherche ignore la description des restaurants | Mineure | P3 | - |
| [BUG-12](BUG-12-avis-sans-commande.md) | Un client peut noter un restaurant où il n'a jamais commandé | Moyenne | P3 | - |
| [BUG-13](BUG-13-api-restaurants-fermes.md) | L'API liste les restaurants fermés comme les autres | Moyenne | P2 | - |
| [BUG-14](BUG-14-coupon-subtotal-client.md) | La validation de coupon fait confiance au montant envoyé par le client | Moyenne | P2 | - |

Pour la sévérité, j'ai classé de critique (perte d'argent ou faille de sécurité) à mineure, en passant par majeure (règle métier cassée) et moyenne. Pour la priorité, P1 veut dire à corriger avant la mise en production, P2 au sprint suivant et P3 plus tard.
