# Composition Docker pour GLPI

## Objectif

Créer une composition Docker permettant d'héberger [GLPI](https://www.glpi-project.org/en/).

Le déploiement devra être fonctionnel, sécurisé et facile à maintenir.

Il devra suivre les recommandations de sécurité de l'ANSSI.

## Services attendus

La composition devra inclure :
- GLPI pour la gestion du parc informatique.
- MariaDB pour le stockage des données.
- un reverse proxy pour sécuriser les accès à l'application.

## Exigences de sécurité

La composition devra respecter les principes suivants :
- limiter les services et les ports exposés
- isoler les conteneurs à l'aide de réseaux dédiés
- limiter les privilèges des conteneurs
- utiliser HTTPS pour sécuriser les accès
- utiliser des images docker officielle
- assurer la persistance des données

Les choix de sécurité devront s'appuyer sur les **recommandations de l'ANSSI.**

## Livrables

- le fichier compose.yaml
- les fichiers de configuration nécessaires
- un fichier `.env.example` sans données sensibles
- une documentation en **Markdown** aiguillant l'utilisateur sur comment lancer la composition

## Validation

Critères de validation :
- GLPI est fonctionnel et accessible en HTTPS
- la base de données n'est pas directement exposée
- les données persistent après le redémarrage des conteneurs
- les mesures de sécurité recommandées par l'ANSSI et vues précedemment sont appliquées
- la documentation fournie est explicite
