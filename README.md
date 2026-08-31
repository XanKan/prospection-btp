# Chantier Prospect — Prospection B2B BTP

Petite application web qui liste les entreprises du BTP (construction, génie civil, travaux spécialisés) filtrées par **département** et **tranche d'effectif**, à partir de l'[API Recherche d'entreprises](https://recherche-entreprises.api.gouv.fr) de l'État (base SIRENE / RNE). Export **CSV compatible Excel** en un clic.

- 100 % statique : un seul fichier `index.html`, aucun serveur, aucune clé API.
- Données publiques, gratuites, mises à jour quotidiennement.
- Par défaut, les filtres correspondent à « BTP, plus de 2 salariés ».

## Mise en ligne sur GitHub Pages (5 minutes)

1. Créez un nouveau dépôt sur GitHub (par exemple `prospection-btp`), public.
2. Ajoutez les deux fichiers `index.html` et `README.md` (bouton **Add file → Upload files**), puis validez le commit.
3. Dans le dépôt : **Settings → Pages → Build and deployment**, source **Deploy from a branch**, branche `main`, dossier `/ (root)`, puis **Save**.
4. Après une minute, votre outil est en ligne à l'adresse :
   `https://VOTRE-PSEUDO.github.io/prospection-btp/`

Ou en ligne de commande :

```bash
git init
git add index.html README.md
git commit -m "Chantier Prospect"
git branch -M main
git remote add origin https://github.com/VOTRE-PSEUDO/prospection-btp.git
git push -u origin main
# puis activez Pages dans Settings → Pages
```

## Ce que l'outil sort

Raison sociale, SIREN, SIRET du siège, code NAF, tranche d'effectif INSEE, adresse complète du siège, dirigeant(s), date de création, et un lien vers la fiche officielle sur l'Annuaire des Entreprises.

## Limites à connaître

- **Pas d'email ni de téléphone** : ces données n'existent pas dans SIRENE. Pour les obtenir, il faut un service d'enrichissement (Societeinfo, Dropcontact…) ou une recherche manuelle.
- L'effectif est la tranche déclarée à l'INSEE, généralement datée de 2 à 3 ans ; il n'est pas renseigné pour beaucoup d'entreprises récentes.
- L'API plafonne la pagination à environ 10 000 résultats par recherche : pour de gros volumes, lancez la recherche département par département.
- Limite de débit : 7 requêtes/seconde (l'outil reste en dessous automatiquement).

## RGPD

Les noms de dirigeants sont des données publiques du Registre National des Entreprises, mais leur utilisation en prospection reste soumise au RGPD : base légale de l'intérêt légitime, information des personnes lors du premier contact, respect du droit d'opposition.
