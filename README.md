# Chantier Prospect — Prospection B2B BTP pour vendre Piloz

Application web de prospection qui trouve, note et cartographie les entreprises du BTP (construction, génie civil, travaux spécialisés) les plus susceptibles d'acheter **Piloz**, sur toute la France, à partir de données publiques et gratuites.

- 100 % statique : un seul fichier `index.html`, aucun serveur, aucune clé API.
- Accès protégé par mot de passe (contrôle côté navigateur, voir plus bas), non indexé.
- Recherche toujours sur toute la France : aucun filtre géographique n'est proposé.

## Sources de données

| Source | Ce qu'elle apporte |
| --- | --- |
| [API Recherche d'entreprises](https://recherche-entreprises.api.gouv.fr) (SIRENE / RNE) | Raison sociale, effectif, chiffre d'affaires publié et son évolution, dirigeants, nombre d'établissements, coordonnées GPS, code NAF |
| [Liste des entreprises RGE](https://data.ademe.fr/datasets/liste-des-entreprises-rge-2) (ADEME) | Téléphone, e-mail, site web des **60 200** entreprises actuellement qualifiées RGE |
| [Historique des entreprises RGE](https://data.ademe.fr/datasets/historique-rge) (ADEME) | Les mêmes coordonnées pour **173 400** entreprises depuis 2014, qualification parfois expirée |

## Le point clé : trouver des e-mails, pas des noms

En France, **seules les entreprises qualifiées RGE publient leurs coordonnées en open data**. Toutes les autres n'ont ni e-mail ni téléphone accessible gratuitement.

L'outil exploite le fait que l'API SIRENE expose un indicateur `est_rge` : la recherche est donc **filtrée en amont sur les entreprises qui ont un contact**, au lieu de balayer tout le BTP en espérant tomber dessus.

Mesuré en conditions réelles : **189 entreprises analysées pour 188 contacts avec e-mail** (99 %), contre environ quatre entreprises analysées par e-mail obtenu avant ce filtrage.

Deux modes au choix :

- **RGE actifs** (par défaut) — 60 200 entreprises, coordonnées à jour, recherche rapide.
- **+ anciens RGE** — 173 400 entreprises. La qualification a pu expirer mais les coordonnées restent souvent valables. Presque trois fois plus de stock, recherche plus lente.

## Couverture nationale réelle

L'API plafonne chaque requête à 10 000 résultats. Au-delà, l'outil découpe automatiquement la recherche par département et les balaie **en tourniquet** — une page dans chacun, à tour de rôle, dans un ordre aléatoire.

Sans ce tourniquet, une recherche vidait entièrement le premier département tiré et tous les prospects venaient du même coin de France. Mesuré : une recherche de 100 contacts ramène des prospects répartis sur **27 départements**.

## Score Piloz (0–100)

Six signaux publics, corrélés au besoin d'un outil de pilotage de chantiers :

| Signal | Poids | Pourquoi |
| --- | --- | --- |
| Effectif salarié | 30 | Cœur de cible : les équipes de 3 à 20 personnes |
| Établissements ouverts | 22 | Plusieurs sites = coordination à organiser |
| Chiffre d'affaires | 16 | Capacité budgétaire |
| Croissance du CA | 12 | Une entreprise qui grossit change d'outils |
| Ancienneté | 10 | Structure installée, mais pas figée |
| Qualifications RGE | 10 | Activité réelle et diversifiée |

⚠️ **Le score n'indique pas qu'une entreprise cherche un logiciel.** Aucune donnée ouverte ne le dit. Il classe des profils de correspondance avec la cible Piloz, à vérifier au contact.

Le détail du calcul, signal par signal, est affiché dans la fiche de chaque prospect.

## Fonctionnalités

- **Dashboard** : indicateurs (prospects, contacts avec e-mail, profils prioritaires, score moyen) et répartition par profil et par taille d'équipe.
- **Tableau triable** par score, nom, effectif, chiffre d'affaires ou ville, avec filtre texte instantané et sélection par cases à cocher.
- **Fiche prospect** : toutes les données, détail du score, boutons de copie de l'e-mail / du téléphone / du SIREN, et liens directs vers la fiche officielle, Google, Maps et LinkedIn.
- **Carte de France** des prospects, colorés par profil, cliquables.
- **Filtre métier précis** : une trentaine de métiers basés sur les vrais codes NAF, plus un raccourci « métiers à fort potentiel ».
- **Export Brevo (.xlsx)** prêt à importer, et **export complet (CSV)** avec tous les signaux. Si des lignes sont sélectionnées, l'export ne porte que sur elles.
- **Historique** : chaque export Brevo archive les entreprises envoyées ; elles sont ensuite écartées automatiquement des recherches suivantes. Importable et exportable en CSV pour être sauvegardé ou transféré sur un autre poste.

L'objectif de contacts sert de **condition d'arrêt** du balayage : les contacts trouvés au-delà sont conservés, puisqu'ils ont déjà coûté les mêmes appels d'API.

## Mise en ligne

Le dépôt est relié à `prospection.piloz.fr` (fichier `CNAME`). Un `git push` sur la branche par défaut met à jour le site via GitHub Pages.

## Limites à connaître

- **Pas de contact hors RGE.** Une entreprise du BTP qui n'a jamais été qualifiée RGE n'a aucune coordonnée publique gratuite. Sur un métier très précis, le stock peut être inférieur à l'objectif demandé.
- **Le chiffre d'affaires n'est pas toujours publié** (micro-entreprises, dépôts récents) : le score neutralise ce facteur au lieu de pénaliser l'entreprise.
- L'effectif est la tranche déclarée à l'INSEE, généralement datée de 2 à 3 ans.
- Limite de débit de 7 requêtes/seconde côté SIRENE : l'outil reste en dessous automatiquement.
- **L'historique est local au navigateur.** Il n'est pas partagé entre postes. Exportez-le en CSV pour le conserver.
- Le mot de passe est vérifié côté navigateur (hash SHA-256 dans le code source). Cela décourage un accès accidentel, ce n'est pas une protection contre quelqu'un qui lit le code. Aucune donnée sensible n'est protégée derrière : tout provient d'API publiques.

Pour changer le mot de passe, ouvrez la page avec `?hash=votrenouveaupasse` : elle affiche le hash à recopier dans `PWD_HASH`.

## RGPD

Les noms de dirigeants et les coordonnées RGE sont publics, mais leur réutilisation en prospection reste soumise au RGPD : base légale d'intérêt légitime, information des personnes dès le premier contact, lien de désinscription, et respect immédiat du droit d'opposition.
