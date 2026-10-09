# BUG-07 : L'API agit au nom d'Alice pour tout détenteur de la clé API

Sévérité critique, priorité P1. Cas de test lié : aucun, trouvé en test exploratoire. Statut : ouvert.

## Contexte
Swagger ou curl, sans session utilisateur.

## Préconditions
Seule la clé publique `quickorder-demo-key` est fournie (aucune connexion).

## Étapes de reproduction
1. GET /api/orders.
2. POST /api/orders/{id}/cancel sur une commande d'Alice.
3. POST /api/orders.

## Résultat attendu
401 : une action sur des commandes doit exiger l'authentification d'un utilisateur.

## Résultat obtenu
Les commandes et l'adresse d'Alice sont lues. Ses commandes peuvent être créées et annulées.

## Preuve
Exécution du 09/10/2026 avec curl, sans cookie de session : [BUG-07-api.txt](../evidence/BUG-07-api.txt).
- `GET /api/orders` : HTTP 200, les 9 commandes d'Alice listées.
- `POST /api/orders/9/cancel` : HTTP 200, commande d'Alice passée à `cancelled` (vérifié en base : user = alice@example.com).
- `POST /api/orders` : HTTP 201, commande #9 créée au nom d'Alice (voir [BUG-06-api.txt](../evidence/BUG-06-api.txt)).

Au passage : après l'annulation, `payment_status` reste `paid`, ce qui veut dire qu'aucun remboursement n'est tracé.

## Cause probable
`app/routes/api.py` l.27-31 : `api_customer()` retombe sur alice@example.com quand personne n'est connecté.
