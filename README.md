# playlist-generator

Formulaire public du **générateur de playlist classique** Radio Nostalgie, utilisé
toute l'année.

- Page : https://laurent7850.github.io/playlist-generator/
- Webhook : `POST /webhook/playlist-generator` (VPS Hostinger)
- Workflow n8n : `Génération Playlist Personnalisée` (`8N7Vb3R8mrBK6DLl`)
- Jours de passage antenne : **lundi-vendredi**

## Édition Été : projet séparé

L'édition Été **ne vit plus dans ce repo**. Elle a son propre projet, son propre site et
son propre workflow :

- Repo : [`playlist-generator-ete`](https://github.com/laurent7850/playlist-generator-ete)
- Page : https://laurent7850.github.io/playlist-generator-ete/
- Workflow n8n : `Génération Playlist Personnalisée — ÉTÉ` (`Mrvg6cCeYZEcpv1y`)
- Jours de passage antenne : samedi uniquement, et seulement pendant la saison

`ete.html` n'est plus qu'une page de redirection vers ce nouveau site, conservée pour les
liens et favoris existants.

Cette séparation vient d'une régression réelle : les deux pages ayant longtemps cohabité
ici, la règle « samedi uniquement » de l'été a été appliquée par erreur au générateur
classique, qui refusait alors toutes les dates valides. **Aucune modification faite pour
l'été ne doit atterrir dans ce repo.**

## Fonctionnement

`index.html` est une page statique sans build ni dépendance : elle poste le formulaire au
webhook n8n. Un push sur `main` déploie via GitHub Pages (compter ~1 minute).

Règles de date : lundi-vendredi. Un samedi ou un dimanche est refusé avec un message
persistant et le champ revient à la dernière valeur valide — jamais de correction
silencieuse (l'ancien comportement décalait la date choisie au lundi suivant sans le dire).
Le parsing ISO est fait en heure locale pour éviter la dérive UTC.

## Données

Catalogue et feuille `Blacklist` (exclusion 21 jours) sur Google Sheets, côté n8n. Ils
restent partagés avec l'édition Été : un titre sorti d'un côté est écarté de l'autre
pendant 21 jours. C'est le seul lien restant entre les deux projets, et il est volontaire.
