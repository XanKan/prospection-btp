# Piloz Prospection

Outil interne publié sur https://prospection.piloz.fr (GitHub Pages). Il trouve des entreprises du BTP dans les données publiques puis **envoie automatiquement un e-mail de prospection personnalisé à chacune**, depuis la boîte Hostinger de Piloz, au rythme réglé.

La page est statique ; tout ce qui touche à l'envoi tourne côté serveur Supabase (projet PILOZ-APP). L'ordinateur peut rester éteint : l'automate continue d'envoyer.

## Accès

Connexion avec le compte **super administrateur Piloz** (le même que sur admin.piloz.fr), double authentification comprise. Aucun mot de passe n'est stocké dans la page.

## Écrans

| Écran | Rôle |
|---|---|
| Automate | État en direct (en marche, en pause, hors créneau, quota atteint…), envois du jour, file d'attente, 14 derniers jours, pilote automatique |
| Recherche | Ciblage par métier (NAF), effectif, zone (départements) et source RGE ; balayage en tourniquet département par département |
| Résultats | Prospects classés par score Piloz, fiche détaillée avec l'e-mail exact qui partira, bouton « Envoyer à l'automate », export CSV |
| Carte | Répartition géographique des résultats |
| Messages | Modèles d'e-mail avec variables (`{bonjour}`, `{prenom}`, `{entreprise}`, `{ville}`, `{metier}`…), aperçu en direct, envoi de test |
| Envois | File d'attente et historique (envoyés, échecs, ignorés), contenu exact de chaque message envoyé |
| Exclusions | Désinscriptions, adresses refusées, ajouts manuels, import de l'ancien historique Brevo du navigateur |
| Réglages | Expéditeur, signature, cadence (quota, montée en charge, créneau, jours, intervalle), pilote automatique |

## Fonctionnement de l'automate

- Chaque entreprise (SIREN) et chaque adresse ne reçoivent **qu'un seul message, pour toujours**.
- Un message à la fois, espacé au hasard (20 à 45 secondes par défaut), du lundi au vendredi de 8 h 30 à 17 h 30, heure de Paris. L'automate enchaîne les envois à chaque réveil pour tenir les gros quotas.
- Quota journalier réglable jusqu'à **3 000 messages** (le plafond de l'offre e-mail Hostinger), réglé à 1 000. La **montée en charge** progressive reste disponible dans les réglages (désactivée actuellement).
- **Pause automatique** après 3 erreurs consécutives ou si la boîte refuse la connexion. Une adresse refusée est exclue automatiquement.
- **Pilote automatique** (facultatif) : quand la file passe sous deux jours d'envoi, le serveur va chercher seul de nouvelles entreprises avec le ciblage enregistré et un score minimum.
- Messages au nom du **Service commercial Piloz** (jamais d'un nom de personne), signature graphique avec le logo, vidéo de présentation d'une minute en image cliquable (emplacement `{video}` du modèle).
- « Bonjour Prénom » systématique : par défaut, seules les entreprises dont le prénom du dirigeant est connu sont contactées.
- **Suivi des messages** : pixel d'ouverture et liens traqués dans la version HTML uniquement (fonction publique `prospection-track`, redirection limitée aux domaines piloz.fr, YouTube et Calendly). L'outil affiche qui a ouvert, regardé la vidéo ou cliqué un lien ; le lien de désinscription n'est jamais traqué.

## Cadre légal (prospection B2B)

Chaque message identifie l'expéditeur, indique l'origine des données (annuaire RGE de l'ADEME, base SIRENE) et contient un lien de désinscription vers https://piloz.fr/desinscription.html. Les en-têtes `List-Unsubscribe` et `List-Unsubscribe-Post` permettent la désinscription en un clic depuis Gmail et Outlook. Une adresse désinscrite n'est plus jamais contactée.

## Architecture

| Élément | Emplacement |
|---|---|
| Interface | `index.html` (ce dépôt) |
| Tables, file, quota, planificateur pg_cron | `PILOZ-APP/supabase/migrations/202610010100_prospection_automate.sql` |
| API de l'outil (super admin + MFA) | `PILOZ-APP/supabase/functions/prospection-api` |
| Envoi SMTP, réveillé par pg_cron | `PILOZ-APP/supabase/functions/prospection-sender` |
| Désinscription publique | `PILOZ-APP/supabase/functions/prospection-unsubscribe` + `PILOZ-SITE/desinscription.html` |
| Suivi (pixel + redirections) | `PILOZ-APP/supabase/functions/prospection-track` |
| Logique partagée | `PILOZ-APP/supabase/functions/_shared/prospection-*.ts` |

Secrets des fonctions Supabase : `PROSPECTION_SMTP_PASSWORD` (obligatoire, mot de passe de la boîte Hostinger). Facultatifs : `PROSPECTION_SMTP_HOST` (défaut `smtp.hostinger.com`), `PROSPECTION_SMTP_PORT` (défaut `465` ; les ports 25 et 587 sont bloqués par Supabase), `PROSPECTION_SMTP_USER` (défaut : l'adresse d'envoi).

**Ordre de mise en production** : migration et fonctions Supabase d'abord, page `desinscription.html` du site ensuite, et cette interface en dernier (sinon elle appelle une API qui n'existe pas encore).

## Sources de données

- **API Recherche d'entreprises** (SIRENE + RNE) : raison sociale, effectif, chiffre d'affaires, dirigeants, établissements, coordonnées GPS.
- **ADEME RGE** (Licence Ouverte Etalab) : e-mail, téléphone et site des entreprises qualifiées RGE — liste actuelle (≈ 60 200 entreprises) et historique depuis 2014 (≈ 173 400).

Le score Piloz (0-100) classe des profils à partir de six signaux publics (effectif, établissements, chiffre d'affaires, croissance, ancienneté, qualifications RGE). Il ne mesure pas une intention d'achat.
