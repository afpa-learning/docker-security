# Composition Docker pour un laboratoire d'audit web (Kali linux)

## Objectif

Créer une composition Docker permettant de réaliser un **audit de sécurité web** : un conteneur [Kali Linux](https://hub.docker.com/r/kalilinux/kali-rolling) attaque une application volontairement vulnérable, dans un réseau de laboratoire isolé.

Le déploiement devra être fonctionnel, sécurisé et facile à reproduire.

Il devra suivre les recommandations de sécurité de l'ANSSI.

## Services attendus

La composition devra inclure :
- un conteneur Kali Linux, construit à partir de l'image `kalilinux/kali-rolling`
- une application cible volontairement vulnérable (l'application DVWA semble idéal)
- MariaDB pour le stockage des données de la cible (déjà intégré dans la composition)

## Points de sécurité

La composition devra respecter les principes suivants :
- isoler le laboratoire dans un réseau dédié **sans accès à l'extérieur**
- ne publier aucun port de la cible sur l'hôte
- limiter les privilèges des conteneurs (ajouter uniquement les capabilities justifiées)
- installer les outils à la **construction de l'image** (Dockerfile), et non à la main dans un conteneur en marche
- assurer la persistance des rapports d'audit sur un volume dédié
- n'attaquer que les services du laboratoire

Les choix de sécurité devront s'appuyer sur les **recommandations de l'ANSSI.**

## Livrables

- le fichier `compose.yaml`
- le Dockerfile de l'image Kali et les fichiers de configuration nécessaires
- un fichier `.env.example` sans données sensibles
- une documentation en **Markdown** aiguillant l'utilisateur sur comment lancer le laboratoire et reproduire l'audit

## Validation

Critères de validation :
- au moins deux vulnérabilités sont identifiées et documentées
- les rapports persistent après la suppression du conteneur Kali
- les mesures de sécurité recommandées par l'ANSSI et vues précédemment sont appliquées
- la documentation fournie est explicite
