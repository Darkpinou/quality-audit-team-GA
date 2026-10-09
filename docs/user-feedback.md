# Tests utilisateurs

Les sessions ont eu lieu le 09/10/2026 : un pré-test que j'ai fait moi-même (U0), puis deux testeurs (U1 et U2).

## Protocole

Chaque session dure 15 à 20 minutes, sur un PC avec Chrome 154 et l'application en local (`http://127.0.0.1:5000`). Je remets la base à zéro avant chaque testeur. La consigne donnée est : "Pensez à voix haute. Je ne peux pas vous aider, c'est l'application qu'on teste, pas vous."

Pendant la session, je note si le scénario est réussi (seul, avec aide, ou pas du tout), le temps, les hésitations, les erreurs et ce que dit le testeur.

## Scénarios

| # | Scénario | Consigne donnée | Réussi si |
|---|---|---|---|
| S1 | Trouver un restaurant pas cher | "Trouvez un restaurant avec une livraison à moins de 2,50 € et ajoutez un plat au panier." | Un produit de Green Kitchen ou Pizza Roma est dans le panier |
| S2 | Commander avec une promo | "Avec le compte alice@example.com, commandez chez Couscous Atlas pour au moins 30 € en utilisant la promo affichée." | La commande est confirmée et le testeur vérifie la réduction |
| S3 | Payer en espèces et suivre sa commande | "Commandez un plat chez Burger Factory, payez en espèces, puis retrouvez votre commande et annulez-la." | La commande est passée puis annulée |

## Pré-test (U0)

J'ai fait le pré-test moi-même pour valider le protocole et avoir des temps de référence. Ça ne compte pas comme un test utilisateur, puisque je connais déjà l'application et ses bugs.

J'ai fait deux passages. Le premier en allant droit au but (6 min pour les trois scénarios), le second en ouvrant tous les menus avant d'agir, pour simuler quelqu'un de moins à l'aise (11 min). Les trois scénarios ont été réussis les deux fois.

J'ai remarqué que pour S1, le filtre "livraison max 2,50 €" affiche des restaurants plus chers (BUG-10). Pour S2, la réduction ATLAS15 est de 10 % et pas de 15 % comme annoncé, et il m'a fallu sortir la calculatrice pour s'en rendre compte (BUG-09, voir la [capture](evidence/BUG-09-remise.png)). Pour S3, l'annulation se fait depuis le détail de la commande.

Du coup, pour U1 et U2, j'ai prévu 15 à 20 minutes chacun, et j'ai décidé d'observer si le testeur vérifie la remise de S2 sans calculatrice.

## Testeurs

| ID | Profil | Âge | Aisance avec le numérique | Contexte |
|---|---|---|---|---|
| U1 | Guillaume, l'auteur de ce dossier (il connaît donc l'application) | 20 ans | Élevée | PC de test, Chrome 154 |
| U2 | Christelle, extérieure au projet | 61 ans | Faible à moyenne | PC de test, Chrome 154 |

Je n'ai pas demandé aux testeurs s'ils utilisent d'habitude des applications de livraison.

## Résultats

J'ai chronométré les trois scénarios d'un bloc, pas un par un. U1 a tout fini en 6 minutes et U2 en 11 minutes.

| Scénario | U1 | U2 |
|---|---|---|
| S1 | Réussi sans blocage | Réussi avec difficulté : le libellé du filtre est mal compris (F1) |
| S2 | Réussi sans blocage | Réussi avec difficulté : ne trouve pas le champ coupon avant le paiement (F2) |
| S3 | Réussi sans blocage | Réussi |

## Retours

| # | Testeur | Canal | Scénario | Contexte | Problème observé | Fréquence | Impact | Verbatim | Piste d'amélioration | Priorité | Action |
|---|---|---|---|---|---|---|---|---|---|---|---|
| F1 | U2 | Observation sur place | S1, étape 2 | Filtre de l'accueil | Comprend "Livraison max" comme le prix maximum de la commande, pas comme les frais de livraison | 1/2 | Filtre mal utilisé, temps perdu, en plus du filtre inversé (BUG-10) | - | Renommer en "Frais de livraison max (€)" avec un exemple, et corriger le filtre | P2 | Ajouté à A7 |
| F2 | U2 | Observation sur place | S2, étape 4 | Saisie du code ATLAS15 | Cherche le champ coupon avant le checkout, retourne sur la page du restaurant pour copier le code, ne trouve le champ qu'à la fin du checkout | 1/2 | Allers-retours, risque d'abandon ou de commande sans la promo | "J'ai réussi mais c'était compliqué" (noté par l'observateur) | Champ coupon dès le panier, bouton "Appliquer ce code" sur le bandeau promo | P2 | Nouvelle amélioration A10 |
| F3 | U0 | Pré-test | S2 | Vérification de la remise | Remise ATLAS15 de 10 % au lieu de 15 %, vue seulement avec une calculatrice | 1 passage | Un client ne peut pas voir qu'il est lésé (BUG-09) | - | Afficher le taux appliqué à côté de la remise, et corriger le coupon | P2 | A6 |
| F4 | U1 | Observation sur place | S1 à S3 | Parcours complet | Aucun blocage. Trouve le parcours intuitif, mais pense qu'il peut être difficile pour une personne âgée | 1/2 | Aucun pour ce profil, mais U2 le confirme | - | Voir F1 et F2 | - | - |

## Ce qu'on surveillait

J'avais noté à l'avance quelques points liés aux bugs connus, sans en parler aux testeurs. Pour S1, est-ce que le testeur remarque que le filtre affiche des restaurants plus chers (BUG-10) ? Pour S2, est-ce qu'il compare la réduction au bandeau -15 % (BUG-09) ? Pour S3, que se passe-t-il s'il donne un montant d'espèces trop faible ou vide (BUG-02), et trouve-t-il comment annuler ?

## Ce qu'on en retient

Les deux testeurs ont réussi les trois scénarios. Par contre, U2 a eu du mal sur deux d'entre eux et a mis presque deux fois plus de temps que U1 (11 minutes contre 6). D'après mes observations, ce temps en plus a été perdu sur le filtre (S1) et sur la recherche du champ coupon (S2).

Le premier problème est un problème de vocabulaire. "Livraison max" n'est pas clair pour quelqu'un qui n'a pas l'habitude (F1), et comme en plus le filtre est inversé (BUG-10), il ne sert à rien en l'état.

Le second concerne l'emplacement du coupon. Le code est annoncé sur la page du restaurant, mais on ne peut le saisir qu'à la fin du checkout (F2). L'utilisateur fait des allers-retours et risque d'abandonner ou de payer sans la promo.

Aucun des deux testeurs n'a remarqué que la remise ATLAS15 est de 10 % au lieu de 15 % (BUG-09). Si on ne fait pas le calcul, on ne le voit pas, et c'est justement ce qui rend cette publicité trompeuse risquée.

Ces résultats ont des limites. U1 est l'auteur du dossier et connaît l'application, et je n'ai pas chronométré les scénarios un par un. Il faudrait refaire une session avec deux autres testeurs extérieurs pour confirmer.

Ces retours sont repris dans le [plan d'amélioration](improvement-plan.md) : A6 pour F3, A7 pour F1 et A10 pour F2.
