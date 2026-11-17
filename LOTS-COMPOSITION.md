# Lots Bâan Kumlaï — composition en SKU

Relevé le 17/11/2026 sur baankumlai.com, à la source : le métachamp `custom.bk_lot`
rendu par `snippets/bk-lot.liquid` donne les **identifiants de variante** de chaque
composant, convertis ici en SKU via le catalogue public.

**Les lots n'ont pas de SKU chez Shopify, et ils n'en auront pas.** Le bouton du lot
ajoute les joncs un par un au panier (`/cart/add.js`) : le lot lui-même n'arrive jamais
dans une commande passée sur le site. Lui donner un SKU ne servirait à rien et ne
changerait rien au mécanisme.

**Le cas qui pose problème est la commande saisie à la main** (admin Shopify, commande
pro, devis revendeur) : là, le JS du thème ne tourne pas, le lot entre dans la commande
comme une seule ligne, sans SKU — donc impossible à préparer pour Dock Avenue, qui
travaille à la référence article.

**C'est le soft d'export qui règle ça** (`index.html`, base `lotCompositions`) :

- à l'import du CSV, toute ligne sans SKU dont le libellé correspond à un lot est
  **éclatée automatiquement** en une ligne par jonc, avec la bonne taille et les
  quantités multipliées par la quantité commandée. Les lignes issues d'un lot portent
  une puce `LOT` ;
- un sélecteur **« + Ajouter un lot »** permet d'ajouter un lot complet à la main, sur
  une commande importée comme dans la fenêtre SAV / commande manuelle ;
- à la génération du fichier, un **garde-fou** refuse de passer en silence : il liste
  les lignes qui partiraient sans référence article (un lot inconnu de la base, par
  exemple) et demande confirmation.

**À maintenir à la main** : un nouveau lot créé sur Shopify doit être ajouté à
`lotCompositions` dans `index.html`, sinon il ressortira dans le garde-fou.

## Composition des 9 lots

Les compositions sont identiques d'une taille à l'autre : seul le suffixe `_S` / `_M` /
`_L` change. Les quantités > 1 sont notées `x2`, `x3`.

| Lot (titre exact Shopify) | Prix | Composition (`_T` → `_S`, `_M` ou `_L`) |
|---|---|---|
| Lot 7 Joncs Bouddhistes Multicolores | 84,00 € | `JONCCUIVRE_T`, `JONCROSE_T`, `JONC_CHOCO_T`, `JONC_VERTANTIQUE_T`, `JONC_ORANTIQUE_T`, `JONC_ROSEPOUDRE_T`, `JONCARGENT_T` |
| Lot 5 Joncs Bouddhistes Chocolat, Or Rose & Rose Poudré | 65,00 € | `JONC_CHOCO_T` x2, `JONCROSE_T` x2, `JONC_ROSEPOUDRE_T` |
| Lot 5 Joncs Bouddhistes Vert Antique, Chocolat & Or Antique | 65,00 € | `JONC_VERTANTIQUE_T` x2, `JONC_CHOCO_T` x2, `JONC_ORANTIQUE_T` |
| Lot 5 Joncs Bouddhistes Cuivre, Or Antique, Vert Antique, Chocolat & Or Rose | 65,00 € | `JONCCUIVRE_T`, `JONC_ORANTIQUE_T`, `JONC_VERTANTIQUE_T`, `JONC_CHOCO_T`, `JONCROSE_T` |
| Lot 5 Bracelets Bouddhistes Argent — Joncs, Torsadé & Tressé | 81,48 € | `JONCARGENT_T` x3, `JONCTORSADARGENT_T`, `JONC_ARGENT_TRESSE_T` |
| Lot 5 Bracelets Bouddhistes Or — Joncs, Torsadé & Tressé | 81,48 € | `JONCOR_T` x3, `JONCTORSADOR_T`, `JONC_OR_TRESSE_T` |
| Lot 5 Bracelets Bouddhistes Or Rose — Joncs, Torsadé & Tressé | 81,48 € | `JONCROSE_T` x3, `JONC_ORROSE_TORSADE_T`, `JONC_ORROSE_TRESSE_T` |
| Lot 5 Bracelets Bouddhistes Or, Cuivre & Vert Antique — Torsadé & Tressé | 81,48 € | `JONCOR_T`, `JONCTORSADOR_T`, `JONCCUIVRE_T`, `JONC_VERTANTIQUE_T`, `JONC_OR_TRESSE_T` |
| Lot 5 Bracelets Bouddhistes Chocolat, Rose Poudré & Or Rose — Torsadé & Tressé | 81,48 € | `JONC_CHOCO_T` x2, `JONC_ROSEPOUDRE_T`, `JONC_ORROSE_TORSADE_T`, `JONC_ORROSE_TRESSE_T` |

Les 42 SKU de composants (14 modèles × 3 tailles) sont tous présents dans la base
article du soft.

## Disponibilité au 17/11/2026

| Lot | S | M | L |
|---|---|---|---|
| Lot 7 Joncs Multicolores | — | — | — |
| Lot 5 Joncs Chocolat, Or Rose & Rose Poudré | — | oui | oui |
| Lot 5 Joncs Vert Antique, Chocolat & Or Antique | — | — | — |
| Lot 5 Joncs Cuivre, Or Antique, Vert Antique, Chocolat & Or Rose | — | — | — |
| Lot 5 Bracelets Argent | — | oui | oui |
| Lot 5 Bracelets Or | — | oui | oui |
| Lot 5 Bracelets Or Rose | — | — | oui |
| Lot 5 Bracelets Or, Cuivre & Vert Antique | — | — | oui |
| Lot 5 Bracelets Chocolat, Rose Poudré & Or Rose | — | — | oui |

Le stock d'un bundle est calculé depuis ses composants : **aucun lot n'est vendable en
taille S**, et 4 lots sur 9 sont épuisés dans les trois tailles.

## Non vérifiable sans accès Shopify

Le connecteur Shopify n'est plus authentifié. N'ont donc **pas** pu être audités :

- les **EAN / codes-barres**, que `products.json` n'expose pas — or Dock Avenue prépare
  à partir de l'EAN, pas du SKU ;
- les produits en **brouillon** ou publiés sur un autre canal que la boutique en ligne.

Sur les 36 produits publiés, **seuls les 9 lots sont sans SKU** ; tous les autres sont
référencés sur l'ensemble de leurs variantes.

## SKU partagé : normal

`POCHETTE-LIN-OFFERTE` est porté par deux produits distincts — « Pochette en lin —
Offerte (Tirage de Noël) » et « Pochette cadeau en lin — Offerte ». **C'est le même
article physique** (confirmé par David le 17/11/2026) : un seul SKU est donc correct,
l'entrepôt prépare la même pochette dans les deux cas. Ne pas chercher à les
distinguer.

À ne pas confondre avec la pochette **payante** à 5,90 €, qui a son propre SKU
`SAC-ORGANZA-BEIGE`.
