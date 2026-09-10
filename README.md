# Chantier Prospect — Prospection B2B BTP pour vendre Piloz

Application web de prospection qui trouve, note et suit les entreprises du BTP (construction, génie civil, travaux spécialisés) les plus susceptibles d'acheter **Piloz**, à partir de données publiques : l'[API Recherche d'entreprises](https://recherche-entreprises.api.gouv.fr) de l'État (base SIRENE / RNE) pour identifier les entreprises, et le jeu de données « Liste des entreprises RGE » de l'ADEME pour leurs coordonnées (téléphone, e-mail, site web).

- 100 % statique : un seul fichier `index.html`, aucun serveur, aucune clé API.
- Données publiques, gratuites, mises à jour quotidiennement.
- Accès protégé par mot de passe (contrôle client, voir plus bas) et non indexé par les moteurs de recherche.
- Par défaut, les filtres correspondent à « BTP, plus de 2 salariés ».

## Ce qui en fait un outil de prospection, pas juste un export

- **Score Piloz (0–100)** calculé pour chaque entreprise à partir de signaux corrélés au besoin d'un outil comme Piloz : tranche d'effectif la plus proche des offres Piloz, nombre d'établissements ouverts (coordination multi-chantiers), chiffre d'affaires publié quand il est disponible (capacité budgétaire) et ancienneté de la structure. Les résultats sont triés automatiquement du meilleur profil au moins bon ; le détail du calcul apparaît en survolant le score.
  ⚠️ Ce score **n'indique pas** qu'une entreprise recherche activement un CRM — aucune donnée publique ne permet de le savoir. C'est une estimation de correspondance avec le profil client de Piloz, à vérifier au contact.
- **Message de prospection prêt à copier** : bouton « ✉ Message » sur chaque ligne, génère un e-mail personnalisé (prénom du dirigeant si connu, mention du multi-établissements quand c'est pertinent, lien piloz.fr trackable) et le copie dans le presse-papiers.
- **Historique d'export** (bouton en haut à droite) : les entreprises exportées vers Brevo sont mémorisées dans ce navigateur et ne réapparaissent jamais dans une recherche suivante.
- **Export Brevo (.xlsx)** prêt à importer dans une campagne e-mail, et **export complet (CSV)** avec toutes les données récupérées, score et signaux inclus.

## Mise en ligne sur GitHub Pages

Le dépôt est déjà relié à `prospection.piloz.fr` (fichier `CNAME`). Un `git push` sur la branche par défaut suffit à mettre à jour le site après activation de GitHub Pages (**Settings → Pages → Build and deployment**, source **Deploy from a branch**).

## Ce que l'outil sort

Raison sociale, SIREN, SIRET du siège, code NAF, tranche d'effectif INSEE, nombre d'établissements ouverts, chiffre d'affaires publié (et son année), adresse complète du siège, dirigeant(s), date de création, téléphone, e-mail, site web, domaines RGE, score Piloz, et un lien vers la fiche officielle sur l'Annuaire des Entreprises.

## Limites à connaître

- **Coordonnées limitées aux entreprises RGE** : seules les entreprises qualifiées RGE publient leurs coordonnées en open data, soit environ 60 000 établissements sur toute la France. L'outil parcourt donc la liste SIRENE et ne retient que celles qui ont un contact : comptez environ quatre entreprises analysées pour un e-mail obtenu. Sur un département peu dense, le stock disponible peut être inférieur à l'objectif demandé.
- **Le chiffre d'affaires n'est pas toujours publié** (micro-entreprises, dépôts récents…) : le score neutralise ce facteur plutôt que de pénaliser l'entreprise quand la donnée manque.
- L'effectif est la tranche déclarée à l'INSEE, généralement datée de 2 à 3 ans ; il n'est pas renseigné pour beaucoup d'entreprises récentes.
- L'API plafonne la pagination à environ 10 000 résultats par recherche : pour de gros volumes, lancez la recherche département par département.
- Limite de débit : 7 requêtes/seconde côté SIRENE (l'outil reste en dessous automatiquement).
- Le mot de passe d'accès est vérifié côté navigateur (hash SHA-256 dans le code source) : cela décourage un accès accidentel, ce n'est pas une protection contre quelqu'un qui lit le code source. Aucune donnée sensible n'est protégée derrière (tout provient d'API publiques) — c'est un filtre d'usage, pas une sécurité.

## RGPD

Les noms de dirigeants et les coordonnées RGE sont des données publiques, mais leur utilisation en prospection reste soumise au RGPD : base légale de l'intérêt légitime, information des personnes dès le premier contact, lien de désinscription, et respect immédiat du droit d'opposition.
