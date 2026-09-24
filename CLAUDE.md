# playlist-generator — générateur classique

## Périmètre

Ce repo contient **uniquement** le générateur de playlist classique Radio Nostalgie,
utilisé toute l'année : une page statique `index.html` publiée sur GitHub Pages, qui poste
vers le webhook n8n `playlist-generator`.

⚠️ L'**édition Été** est un projet **séparé** : repo `playlist-generator-ete`, page
https://laurent7850.github.io/playlist-generator-ete/, workflow n8n `Mrvg6cCeYZEcpv1y`.
Ne jamais y propager une modification faite ici, et inversement.

Motif : quand les deux pages cohabitaient dans ce repo, la règle « samedi uniquement »
de l'édition Été a été appliquée par erreur à `index.html`, qui refusait alors toutes les
dates valides (corrigé le 2026-08-19). C'est cette confusion que la scission supprime.

## Spécificités

- **Jours de passage antenne : lundi-vendredi.** La règle « samedi » appartient à l'été
  et n'a rien à faire ici.
- **Pas de correction silencieuse de date** : une date invalide est refusée avec un message
  persistant et le champ revient à la dernière valeur valide. Ne pas réintroduire de
  décalage automatique — c'était un bug signalé en production.
- **Pas de build** : HTML/CSS/JS dans un seul fichier, push sur `main` = déploiement Pages.
- `ete.html` n'est qu'une redirection vers le projet Été. Ne rien y ajouter d'autre.
- **URL du webhook** : elle vit dans la page (site public). Aucun autre secret ici —
  credentials et clés restent côté n8n.
- **Mémoire des auditeurs** : un HTTP **409** du webhook n'est pas une erreur, c'est
  « déjà passé à l'antenne » (voir README). Ne jamais stocker un téléphone en clair dans
  l'onglet `Auditeurs` : toujours l'empreinte HMAC du nœud `1c`. Toute modification du
  format de normalisation (`1b`) invalide la comparaison avec les empreintes existantes.
