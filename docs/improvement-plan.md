# Plan d'amélioration

P1 veut dire à faire avant la mise en production, P2 au sprint suivant, P3 plus tard. Pour l'effort, S correspond à moins d'une demi-journée, M à environ une journée et L à plus de deux jours.

| # | Amélioration | Bugs liés | Priorité | Effort | Pourquoi |
|---|---|---|:-:|:-:|---|
| A1 | Regrouper la validation de commande dans une seule fonction côté serveur, appelée par l'UI et par l'API : restaurant ouvert, minimum atteint, stock suffisant, coupon actif et non expiré | BUG-03, 04, 05, 06 | P1 | M | Les règles sont codées deux fois (UI et API) et ne font pas la même chose. Avec une seule fonction, on corrige les deux d'un coup |
| A2 | Supprimer la deuxième commande créée pour PayPal, et empêcher qu'une même commande soit enregistrée deux fois (jeton de checkout à usage unique) | BUG-01 | P1 | S | Le client est débité deux fois |
| A3 | Vérifier le paiement en espèces : un montant numérique au moins égal au total, sinon un message d'erreur | BUG-02 | P1 | S | Aujourd'hui on accepte des commandes payées à perte avec un rendu de monnaie négatif |
| A4 | Authentifier l'utilisateur sur l'API avec un token par compte, retirer le repli sur alice@example.com, et recalculer le sous-total côté serveur dans `/coupons/validate` | BUG-07, 14 et observations | P1 | M | Des données de vrais clients sont lisibles et modifiables par n'importe qui |
| A5 | Définir les transitions de statut autorisées (confirmed, preparing, out_for_delivery puis delivered, annulation seulement avant livraison) | BUG-08 | P2 | S | Sinon le suivi client et les remboursements deviennent incohérents |
| A6 | Passer le coupon ATLAS15 à 15 % et générer le bandeau promo à partir du coupon | BUG-09 | P2 | S | Publicité trompeuse |
| A7 | Corriger le filtre sur les frais (`<=`) et le renommer "Frais de livraison max (€)", chercher aussi dans la description, griser les produits épuisés et les restaurants fermés, et ajouter un filtre `open` à l'API | BUG-10, 11, 13, 05 et retour F1 | P2 | S | U2 a mal compris le libellé, et le filtre renvoie l'inverse de ce qu'on demande |
| A8 | Sécurité web de base : n'accepter que des URL relatives dans `next`, ajouter le CSRF (Flask-WTF), passer le logout en POST, rendre l'adresse obligatoire à l'inscription | observations | P2 | M | Moins de failles avant d'ouvrir le site au public |
| A9 | Écrire des tests pytest pour chaque règle de A1 à A5 et les lancer en CI (GitHub Actions) | tous | P1 | M | Les 24 cas manuels pourront être rejoués à chaque commit |
| A10 | Rendre le coupon accessible plus tôt : champ coupon dès le panier, bouton "Appliquer ce code" sur le bandeau promo, taux affiché à côté de la remise | retours F2 et F3 | P2 | S | U2 a fait plusieurs allers-retours pour saisir ATLAS15 (11 min contre 6 pour U1), avec un risque d'abandon ou de commande sans la promo |
| A11 | N'accepter un avis qu'après une commande livrée dans ce restaurant, et un seul par commande | BUG-12 | P3 | S | Aujourd'hui n'importe quel compte peut laisser des avis sans limite, ce qui rend les avis inutilisables le jour où ils seront affichés ou pris en compte dans la note |

## Dans quel ordre

Je commencerais par A2 et A3, qui tiennent en quelques lignes et arrêtent tout de suite les pertes d'argent. Viennent ensuite A1 et A4 pour les règles serveur et la sécurité de l'API, en écrivant les tests de A9 en même temps que A1 pour que chaque règle corrigée arrive avec son test. A5, A6, A7, A8 et A10 peuvent suivre au sprint d'après, et A11 plus tard.

## Revalidation

Après chaque correctif, on rejoue le cas de test lié dans [le fichier des cas de test](test-cases.xlsx). Le bug passe en "Corrigé", puis en "Fermé" quand le cas repasse en Pass. Avant de décider du Go, on relance toute la campagne.
