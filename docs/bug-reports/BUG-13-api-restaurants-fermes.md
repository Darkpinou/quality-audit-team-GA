# BUG-13 : L'API liste les restaurants fermés comme les autres

Sévérité moyenne, priorité P2. Cas de test lié : aucun, trouvé en test exploratoire. Statut : ouvert.

## Contexte
Swagger, X-API-Key: quickorder-demo-key.

## Préconditions
Night Sushi est fermé (`is_open = false`).

## Étapes de reproduction
1. `GET /api/restaurants?category=Japonais`.

## Résultat attendu
Night Sushi n'apparaît pas, ou un paramètre permet de filtrer les restaurants ouverts. Une appli cliente qui utilise cette liste ne doit pas proposer un restaurant où on ne peut pas commander.

## Résultat obtenu
Night Sushi est renvoyé au même niveau que Tokyo Bowl, et aucun paramètre ne permet de l'exclure. Combiné à BUG-03, une appli cliente peut faire commander dans un restaurant fermé.

## Preuve
Exécution du 09/10/2026 avec curl : [BUG-13-api-fermes.txt](../evidence/BUG-13-api-fermes.txt). La réponse renvoie `count: 2` : Tokyo Bowl (`"is_open": true`) et Night Sushi (`"is_open": false`).

## Cause probable
`app/routes/api.py` l.78-80 : aucun filtre sur `is_open` dans `GET /api/restaurants`.
