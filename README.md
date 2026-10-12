# Progression AIS

Découverte Docker :
1. Découverte des principes de base :
  - de l'image au conteneur
  - réseau et DNS interne
  - customisation d'une image Docker
  - pulbication d'une image Docker

2. Initiation à la composition Docker :
  - composition sur un seul réseau
  - segmentation réseau avec Docker
```text
| Réseau "frontend"  | Réseau "backend" |
| reverse proxy <-> app <->  DB
```
La BDD jamais exposée en directement, injoignable depuis le proxy.o
  - mise en place des recommandations ANSSI/CIS (Center for Internet Security) -> durcissement d'images Docker

3. Composition avancée
  - mise en place d'un conteneur Wireguard pour rejoindre le LAN

Pour aller plus loin :
  - scan des CVE des images
  - mise en place de Grafana pour superviser l'infrastructure
  - mise en place d'un SOC (Security Operations Center) basé sur :
    - un IDS/IPS réseau (peut-être Suricata ?)
    - un gestionnaire de collecte de logs et évènements réseau : Wazuh (peut recevoir les logs du reverse proxy et de l'application)

# Docker

## Introduction

Votre organisation intègre un service de développement afin de développer des solutions sur-mesure que ce soit pour une utilisation interne ou des services fournis en externe.

L'équipe nouvellement composée adopte une approche CI/CD ("Continuous Integration / Continuous Development") et trouve un accord avec pour utiliser un approche de développement basée sur des conteneurs Docker.

Ces conteneurs seront exploitables en production et devront être intégrés dans votre infrastructure existante : **Docker** deviendra un nouvel élément de l'infrastructure à sécuriser.

## Phase 1 : montée en compétences des équipes

### Objectif

Cette première phase à pour objectif de vous permettre d'appréhender les concepts fondamentaux de Docker afin de l'utiliser au sein de votre équipe.

Compétences abordées :
- utilisation d'image Docker pour instanciation de conteneurs
- construction d'images sur-mesure

### Travail à effectuer

1. Test d'images
2. Configuration d'une image sur-mesure
3. Publication d'une image
4. Configuration d'une image de serveur web `nginx`

## Phase 2 : déploiement d'un service

### Objectif

En paralèlle de votre montée en compétence l'équipe de développement avance sur la solution web.

Cette solution se base sur plusieurs conteneurs, chacun fournissant un service spécifique :
1. un conteneur de BD
2. un conteneur applicatif web (application en Python)

Jusqu'à présent des conteneurs uniques ont été mis en place, l'objectif de cette phase est de prendre en main la "composition" afin de faire cohabiter plusieurs conteneurs.

### Travail à effectuer

1. prise en main de la composition Docker applicative
2. modification de la configuration de `nginx` pour `reverse-proxy`

## Phase 3 : Segmentation en sous-réseaux

### Objectif

Le RSSI exige que la base de données ne soit pas joignable de l'extérieur, il vous impose d'être en accord avec les recommandations de l'ANSSI en ce qui concerne le cloisonnement des conteneurs (cf. documentation disponible [ici](https://messervices.cyber.gouv.fr/documents-guides/docker_fiche_technique.pdf))

### Travail à effectuer

1. Segmentation en réseaux `Docker` pour suivre les recommandations de l'ANSSI
2. Protection de la BDD avec publication de ports minimale

## Phase 4 : durcissement des images

### Mise en situation

Toujours dans une perspective de sécurisation, il vous est demandé de vous assurer que les images utilisée sont exemptes de failles.

### Travail à effectuer

1. Application des recommandations de sécurité de l'ANSSI

## Phase 5 : Exploitation du service

### Objectif

Pour être en accord avec le SLA (["Service-Level Agreement" ou "Accord de niveau de sécurité"](https://fr.wikipedia.org/wiki/Service-level_agreement)) il vous est demandé de mettre en place une sauvegarde des données de certains conteneurs.

### Travail à effectuer

## Phase 6 : distribution de l'image Docker

### Objectif

Il est décidé de rendre disponible solution à l'auto-hebergement ("on-premise"), les clients pourront, s'ils le souhaitent, récupérer l'image et instancier des conteneurs sur les serveurs de leur choix.

On vous demande de publier une image sur une banque d'images (par exemple `docker.hub`) et d'écrire une procédure de déploiement pour un service "cloud" (Microsoft Azure, AWS).

### Travail à effectuer

1. Publier l'image sur "docker.hub"
2. Déployer un conteneur basé sur cette image sur "Microsoft Azure"
