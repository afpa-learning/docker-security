# Composition Docker pour Forgejo

## Objectif

Créer une composition Docker permettant d'héberger [Forgejo](https://forgejo.org/).

Le déploiement devra être fonctionnel, sécurisé et facile à maintenir.

Il devra suivre les recommandations de sécurité de l'ANSSI.

## Services attendus

La composition devra inclure :
- Forgejo pour l'hébergement de dépôts Git et la gestion de projets.
- PostgreSQL pour le stockage des données.
- un reverse proxy pour sécuriser les accès à l'application.

## Exigences de sécurité

La composition devra respecter les principes suivants :
- limiter les services et les ports exposés
- isoler les conteneurs à l'aide de réseaux dédiés
- limiter les privilèges des conteneurs
- utiliser HTTPS pour sécuriser les accès
- utiliser des images docker officielles
- assurer la persistance des données
- désactiver l'inscription publique des utilisateurs

L'accès aux dépôts en SSH est facultatif. S'il est activé, le port exposé devra être justifié.

Les choix de sécurité devront s'appuyer sur les **recommandations de l'ANSSI.**

## Livrables

- le fichier `compose.yaml`
- les fichiers de configuration nécessaires
- un fichier `.env.example` sans données sensibles
- une documentation en **Markdown** aiguillant l'utilisateur sur comment lancer la composition

## Validation

Critères de validation :
- Forgejo est fonctionnel et accessible en HTTPS
- un dépôt peut être créé puis cloné en HTTPS
- la base de données n'est pas directement exposée
- les dépôts et les comptes persistent après le redémarrage des conteneurs
- les mesures de sécurité recommandées par l'ANSSI et vues précédemment sont appliquées
- la documentation fournie est explicite
