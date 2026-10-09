# BUG-11 : La recherche ignore la description des restaurants

Sévérité mineure, priorité P3. Cas de test lié : aucun, trouvé en test exploratoire. Statut : ouvert.

## Contexte
Visiteur, interface web et API.

## Préconditions
Green Kitchen a pour description "Salades, wraps et options végétariennes." Son nom ne contient pas "salade".

## Étapes de reproduction
1. Sur l'accueil, rechercher "salade" (`/?q=salade`).
2. Dans Swagger, appeler `GET /api/restaurants?q=salade`.

## Résultat attendu
Green Kitchen apparaît dans les deux cas : un client qui cherche un type de plat doit trouver les restaurants qui le proposent.

## Résultat obtenu
Aucun restaurant trouvé, dans l'interface comme dans l'API (`count: 0`).

## Preuve
Exécution du 09/10/2026 avec curl : [BUG-11-recherche.txt](../evidence/BUG-11-recherche.txt). La fiche API de Green Kitchen contient bien "Salades" dans `description`, mais la recherche `q=salade` renvoie `count: 0`, et la page d'accueil `/?q=salade` ne contient pas Green Kitchen.

## Cause probable
`app/routes/main.py` l.20-21 et `app/routes/api.py` l.72-73 : le filtre porte seulement sur `Restaurant.name`.
