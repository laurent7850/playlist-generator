# playlist-generator

Deux formulaires statiques (GitHub Pages) qui alimentent les générateurs de playlist
Radio Nostalgie via des webhooks n8n hébergés sur le VPS Hostinger.

| Page | URL publique | Webhook n8n | Workflow n8n | État |
|------|--------------|-------------|--------------|------|
| `index.html` | https://laurent7850.github.io/playlist-generator/ | `POST /webhook/playlist-generator` | `Génération Playlist Personnalisée` (`8N7Vb3R8mrBK6DLl`) | actif toute l'année |
| `ete.html` | https://laurent7850.github.io/playlist-generator/ete.html | `POST /webhook/playlist-generator-ete` | `Génération Playlist Personnalisée — ÉTÉ` (`Mrvg6cCeYZEcpv1y`) | saisonnier, éteint hors saison |

Les deux workflows partagent le même catalogue et la même feuille `Blacklist`
(exclusion 21 jours) : ce qui est généré d'un côté est écarté de l'autre.

## Règles communes

- Date de passage antenne : **samedi uniquement**. Une date non-samedi est refusée et
  le champ revient à la dernière valeur valide — pas de correction silencieuse.
- Le parsing de date est local (`parseLocalDate` / `toIsoLocal`) pour éviter le décalage
  UTC de `new Date("2026-07-04")`.
- Toute la logique vit dans le HTML : pas de build, pas de dépendance. Un push sur `main`
  déploie via GitHub Pages (compter ~1 minute).

## Saisonnalité de `ete.html`

Hors saison, le workflow n8n Été est désactivé volontairement : il consomme des tokens LLM
et écrit dans la blacklist partagée avec le générateur classique. Le webhook répond alors
`404` et la page l'annonce au visiteur au lieu d'afficher une erreur technique.

L'état est piloté par un seul drapeau en tête de script :

```js
const SEASON_OPEN = false;
```

- `false` → l'encart d'intro est remplacé par un message de fermeture renvoyant à l'été
  suivant et vers le générateur classique ; date, zone de texte et bouton sont désactivés.
  Un `404` renvoyé par le webhook affiche le même message ; les vraies pannes (5xx, timeout)
  gardent « Erreur lors de la génération. Réessayez. », donc les deux cas restent
  distinguables.
- `true` → comportement normal du formulaire.

### Ouvrir la saison (juin)

1. Activer le workflow `Mrvg6cCeYZEcpv1y` dans n8n.
2. Passer `SEASON_OPEN` à `true` dans `ete.html`, commit + push.

### Fermer la saison

L'inverse, dans cet ordre : `SEASON_OPEN = false` d'abord (la page cesse d'accepter des
soumissions), puis désactivation du workflow.

Les deux gestes vont ensemble : un drapeau à `true` avec un workflow éteint redonne une
erreur technique au visiteur, un drapeau à `false` avec un workflow actif rend le
générateur inaccessible alors qu'il tourne.

## Historique saisonnier

- **2026-06-25** — création de l'édition Été (`ete.html` + workflow dédié).
- **2026-08-17** — fin de saison : workflow Été désactivé.
- **2026-08-19** — réactivation ponctuelle pour la dernière playlist d'été, puis
  désactivation ; `SEASON_OPEN` introduit et mis à `false`.
