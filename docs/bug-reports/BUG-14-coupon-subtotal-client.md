# BUG-14 : La validation de coupon fait confiance au montant envoyé par le client

Sévérité moyenne, priorité P2. Cas de test lié : aucun, trouvé en test exploratoire. Statut : ouvert.

## Contexte
Swagger, X-API-Key: quickorder-demo-key.

## Préconditions
Aucun panier. Coupon SAVE20 : 20 %, minimum 35 €, plafond 12 €.

## Étapes de reproduction
1. `POST /api/coupons/validate` avec `{"code":"SAVE20","subtotal":1000}`.
2. Recommencer avec `"subtotal":35`.

## Résultat attendu
Le serveur calcule la remise à partir du vrai panier, ou refuse quand il n'y en a pas.

## Résultat obtenu
Le coupon est déclaré valide (`valid: true`) avec une remise de 12 € pour 1000 € et de 7 € pour 35 €, alors qu'aucun panier n'existe. Le client peut afficher une remise qui ne correspond à rien, et l'endpoint permet de tester les seuils de tous les coupons.

## Preuve
Exécution du 09/10/2026 avec curl : [BUG-14-coupon-subtotal.txt](../evidence/BUG-14-coupon-subtotal.txt). Les deux appels renvoient HTTP 200 et `valid: true`, avec `discount: 12.0` pour 1000 € et `discount: 7.0` pour 35 €.

## Cause probable
`app/routes/api.py` l.116-117 : `subtotal` est lu dans le JSON de la requête au lieu d'être recalculé côté serveur.
