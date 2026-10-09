# BUG-06 : L'API de commande applique un coupon expiré

Sévérité majeure, priorité P1. Cas de test lié : TC-E05. Statut : ouvert.

## Contexte
Swagger, X-API-Key: quickorder-demo-key.

## Préconditions
Coupon EXPIRED10 expiré depuis 1 jour.

## Étapes de reproduction
1. POST /api/orders avec `{"items":[{"product_id":1,"quantity":2}],"coupon":"EXPIRED10"}`.

## Résultat attendu
Le coupon est ignoré (discount 0) ou la requête est refusée (400).

## Résultat obtenu
HTTP 201, `discount: 2.38`, total réduit. L'UI et /api/coupons/validate refusent pourtant ce coupon : comportement incohérent.

## Preuve
Exécution du 09/10/2026 avec curl : [BUG-06-api.txt](../evidence/BUG-06-api.txt).
- `POST /api/coupons/validate` avec EXPIRED10 : HTTP 404, "Coupon invalide ou expiré".
- `POST /api/orders` avec le même coupon : HTTP 201, commande #9, `discount: 2.38`, total 24,41 € au lieu de 26,79 €.

## Cause probable
`app/routes/api.py` l.167-168 : seul `active` est vérifié, pas `expires_at`.
