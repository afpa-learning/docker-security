# Composition Docker pour Vaultwarden

## Objectif

Créer une composition Docker permettant d'héberger [Vaultwarden](https://vaultwarden.com/) afin que des utilisateurs puissent **stocker leurs mots de passe et en partager**.

Le déploiement devra être fonctionnel, sécurisé et facile à maintenir.

Il devra suivre les recommandations de sécurité de l'ANSSI.

## Services attendus

La composition devra inclure :
- [Vaultwarden](https://hub.docker.com/r/vaultwarden/server) pour la gestion des mots de passe.
- un reverse proxy pour sécuriser les accès à l'application.
- [MailPit](https://hub.docker.com/r/axllent/mailpit), un serveur de messagerie **de test** qui capture les e-mails envoyés par Vaultwarden (aucun message n'est réellement envoyé).

## Exigences de sécurité

La composition devra respecter les principes suivants :
- limiter les services et les ports exposés
- isoler les conteneurs à l'aide de réseaux dédiés
- limiter les privilèges des conteneurs
- utiliser HTTPS pour sécuriser les accès
- utiliser des images docker publiées par les projets
- assurer la persistance des données de Vaultwarden
- MailPit n'est joignable en SMTP que par Vaultwarden, et son interface web n'est accessible que depuis le poste local

Les choix de sécurité devront s'appuyer sur les **recommandations de l'ANSSI.**

## Livrables

- le fichier `compose.yaml`
- les fichiers de configuration nécessaires
- un fichier `.env.example` sans données sensibles
- une documentation en **Markdown** aiguillant l'utilisateur sur comment lancer la composition, inviter un nouvel utilisateur et partager un mot de passe avec une équipe

## Validation

Critères de validation :
- Vaultwarden est fonctionnel et accessible en HTTPS
- les utilisateurs peuvent recevoir leurs e-mailis dans MailPit, puis créent leur compte grâce au lien reçu (simulation de fonctionnement réel car nous n'avons pas de serveur SMTP exploitable)
- un mot de passe placé dans une collection partagée est lisible par un autre membre de l'organisation
- les données de Vaultwarden persistent après le redémarrage des conteneurs
- les mesures de sécurité recommandées par l'ANSSI et vues précédemment sont appliquées
- la documentation fournie est explicite
