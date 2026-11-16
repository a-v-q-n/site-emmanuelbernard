# site-emmanuelbernard

Le site personnel d'Emmanuel Bernard, `https://emmanuelbernard.ch` : une page
statique, Astro 5, React pour le menu mobile, Tailwind 4, servie par nginx. Le
portrait, l'image de partage et les polices viennent de `cdn.avqn.ch`.

## Construire et lancer

Le `Dockerfile` à la racine construit l'image seul, sans secret.

```bash
docker build --build-arg GIT_SHA="$(git rev-parse HEAD)" -t site-emmanuelbernard .
docker run --rm -p 8080:80 site-emmanuelbernard   # http://localhost:8080
```

- Un seul port exposé : **80**.
- La sonde : `GET /healthz.json` et `GET /healthz` rendent
  `200 {"ok":true,"sha":"<GIT_SHA>"}`, écrit dans `dist/healthz.json` au build ;
  « inconnu » sans l'argument de build `GIT_SHA`.
- Le `HEALTHCHECK` demande `/healthz` par le `wget` de busybox.
- Aucune variable d'environnement.

## La publication

Le site se publie par une poussée sur `main` : `.github/workflows/publier.yml`
construit l'image avec `GIT_SHA` au commit et la pousse sur
`ghcr.io/a-v-q-n/site-emmanuelbernard`, étiquetée `main` et par le commit. Le serveur
d'AVQNOS tire l'étiquette `main` toutes les cinq minutes et sert la nouvelle
image dès qu'elle change : `<adresse du site>/healthz.json` dit le commit servi.

Le serveur tire l'image sans identifiant, le paquet GHCR étant public.

## Travailler dessus

**pnpm**, épinglé par `packageManager`.

```bash
pnpm install
pnpm run dev        # http://localhost:4321
pnpm run build
```

## Claude Code

- `CLAUDE.md` : les gestes de la personne qui fait évoluer le site, pour Claude
  Code : préparer l'ordinateur, prévisualiser, publier, revenir à une version
  d'avant.
- `.claude/settings.json` : Claude ne modifie ni `.github/` ni `.claude/`, ne
  lance ni `gh secret`, ni `gh api`, ni `gh variable set` ou `delete`, ni les
  gestes de `gh auth` qui rendent ou changent le jeton, et ne force aucune
  poussée.
