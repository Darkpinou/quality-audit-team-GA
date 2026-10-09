# BUG-12 : Un client peut noter un restaurant où il n'a jamais commandé

Sévérité moyenne, priorité P3. Cas de test lié : aucun, trouvé en test exploratoire. Statut : ouvert.

## Contexte
Client bob@example.com.

## Préconditions
Bob n'a aucune commande chez Pizza Roma.

## Étapes de reproduction
1. Se connecter avec bob@example.com.
2. Ouvrir la fiche Pizza Roma.
3. Poster un avis 1/5 avec le commentaire "Test QA sans commande".
4. Recommencer une deuxième fois.

## Résultat attendu
L'avis est refusé : seul un client qui a été livré par ce restaurant peut le noter, et une seule fois par commande.

## Résultat obtenu
Les deux avis sont acceptés avec le message "Merci pour votre avis !". N'importe quel compte peut donc laisser autant d'avis qu'il veut sur un restaurant où il n'a jamais commandé. À noter : la fiche restaurant n'affiche pas la liste des avis, et la note affichée est une valeur fixe en base qui ne bouge pas avec les avis.

## Preuve
Exécution du 09/10/2026 avec curl en session Bob : [BUG-12-avis.txt](../evidence/BUG-12-avis.txt). La page /orders de Bob ne contient aucune commande Pizza Roma, et les deux envois renvoient le message de confirmation.

## Cause probable
`app/routes/shop.py` l.89-90 : l'avis est enregistré sans vérifier de commande livrée ni d'avis déjà existant.
