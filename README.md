# Carnet de podcast

Site statique Astro qui publie les cours écrits en Markdown. Copie du thème de
`Ada/cours-ada` (Carnet de dev), sans ses cours.

Déploiement sur Coolify avec le `Dockerfile` du dépôt : choisir le build pack
Dockerfile et le port `80`. Le build produit `dist/`, servi par Nginx. Aucun
déploiement GitHub Pages n’est nécessaire.

- `src/cours/<cours>/index.md` : page d'accueil d'un cours, `title` et `order` en frontmatter.
  Les chapitres sont les autres `.md` du dossier, triés par `order`.
- `src/layouts/Lesson.astro` : assemblage de la page et données du cours.
- `src/components/` : en-tête, navigation du cours, recherche, sommaire et Pochette.
- `src/pages/[course]/imprimer.astro` : le cours entier sur une page, sommaire en tête, pour
  l'imprimer ou l'enregistrer en PDF (`/ux-ui/imprimer/`). Règles d'impression : `src/styles/print.css`.
- `src/styles/theme.css` : ordre des couches et imports CSS. Les couleurs et polices
  sont dans `settings.css`, les styles sont répartis par fonction.
- `src/scripts/diagrams.js` : rendu Mermaid, légende `accTitle` / `accDescr`, lecture pas à pas des flowcharts et zoom plein écran.
- `src/lib/remark-mermaid.mjs` : transforme les blocs ```` ```mermaid ```` en `<div class="mermaid">`.

```sh
npm install
npm run dev      # http://localhost:4321
npm run build    # dist/
npm test         # tests des fonctions sans navigateur
npm run check    # format, tests, build, dates et cours Three.js
npm run format   # formater le code du site
```

Les liens du contenu affichent un aperçu au survol ou au focus clavier. Les résumés
externes sont conservés dans `src/data/link-previews.json`, sans requête vers les
sites liés pendant la lecture. Après ajout de liens, lancer `npm run build` puis
`npm run previews` pour actualiser ce fichier et le versionner. Le build suivant
publie les aperçus. Les sites inaccessibles gardent leur ancien résumé, s'il existe.

## Organisation du site

Les composants Astro rendent le HTML. Leurs petits blocs `<script>` importent les
modules de `src/scripts/`. Le script de préférences est la seule exception :
`PageHead.astro` insère `preferences-init.js` avant le premier rendu pour éviter
un flash du mauvais thème ou de la mauvaise taille de texte.

- `preferences.js` gère thème, taille du texte et Focus. `chapters.js` gère la
  navigation sur téléphone, `reading-progress.js` le sommaire actif et la reprise.
- `reading.js` ajoute la copie du code et les liens des titres.
- `notebook.js` gère les rubans et les compteurs, `annotations.js` les sélections
  et notes, `pocket.js` la page de sauvegarde. Les clés de stockage et le format
  des exports restent compatibles avec les versions précédentes.
- `src/lib/notebook.mjs` valide, fusionne et exporte les données sans toucher au DOM.
  `src/lib/search.mjs` classe les résultats et prépare les extraits.
- `src/lib/urls.ts` construit les liens avec le chemin de base Astro.
- `diagrams.js` et `sequence.js` sont chargés seulement si la page contient leurs
  diagrammes. Les vues des séquences sont dans `sequence-views.js`. Mermaid lit
  les couleurs de `settings.css`, y compris après un changement de thème.

Le CSS utilise [Piloti 2](https://github.com/theopaolo/piloti) pour les réglages et
compositions `cluster` et `stack`. La base typographique de Piloti n'est pas
importée : Carnet conserve ses polices, tailles et espacements de lettres.
`PageHead.astro` fixe l'ordre avant les styles injectés par Astro, et
`theme.css` reprend l'ordre `settings`, `reset`, `base`, `compositions`, `components`,
`utilities`. Les styles propres aux pages et composants sont dans la couche
`components`, y compris les styles Astro scopés. Les feuilles globales par
fonction couvrent aussi les éléments créés par les scripts et le Markdown.

Ajouter une couleur dans `settings.css`, un style de contenu dans `prose.css`,
un style d'interface dans la feuille de sa fonction. Utiliser les compositions
Piloti pour les rangées et piles, et `.button` pour les boutons communs. Garder
les fonctions de transformation de données dans `src/lib/`, avec un test Node
quand elles contiennent des règles métier.

Chaque page de cours porte deux dates dans son frontmatter :

```yaml
publishedAt: "2026-09-23"
updatedAt: "2026-09-25"
```

`publishedAt` reste la date de première publication sur le site. Modifier `updatedAt`
quand le contenu de la page change, notamment les versions, les recommandations de
sécurité et les règles juridiques. Un changement de thème ou une reconstruction du
site ne change pas ces dates. Le build exige des dates valides au format `AAAA-MM-JJ`
et refuse une mise à jour antérieure à la publication.

Dans un diagramme, `Node:::accent` (orange, ce qui tourne) et `Node:::store` (vert, données)
forcent la couleur. Les ids `Container*`, `API`, `Worker`, `Migrate`, `Volume*`, `Data`, `Dist`
la reçoivent automatiquement.

## Cours privés

Un cours privé reste sur l'ordinateur de l'enseignant. Ajouter son identifiant dans
`src/lib/private-courses.mjs` et ignorer ses dossiers dans `.gitignore` et `.dockerignore`.
Un fichier déjà suivi par Git reste dans le dépôt et son historique malgré `.gitignore`.

Pour l'afficher avec `npm run dev`, créer `.env.development.local` :

```dotenv
SHOW_PRIVATE_COURSES=true
```

Redémarrer le serveur après avoir changé la variable. `npm run build` exclut toujours
les cours privés, même si la variable vaut `true`. Après le build,
`node scripts/check-private-courses.mjs` vérifie leur absence dans les pages, la
recherche et les ressources. Ce réglage ne fournit pas d'authentification sur le site en ligne.
