
# Docker compose

Docker compose est un outil utiliée pour organiser l'instanciation de plusieurs conteneurs et leur permettre d'avoir des interactions.

Il agit donc comme un "chef d'orchestre" de l'instanciation des conteneurs.
Docker compose permet :
- de simplifier la construction d'images (basé sur un `Dockerfile` ou non) ;
- d'instancier des conteneurs à partir d'images ;
- de mettre en place des dépendances entre les conteneurs ;
- de paramétrer le réseau pour communiquer avec les conteneurs (ouverture et redirection de ports).

![Logo de docker compose](./doc/logo-docker-compose.svg)

Il est beaucoup utilisé pour créer des "stacks" d'applications complètes, par exemple :
- une application Web basée sur serveur web PHP et une base de données PostgreSQL ;
- un service Rest basé sur Java Jakarta et une base de données MySQL
- une application React tournant sur un serveur NodeJS
...


## Plusieurs services : fichier de composition

### Objectif

> [!WARNING]
> Afin de simuler une application web complète (avec une base de données) nous allons utiliser l'application DVWA (pour "Damn Vulnerable Web Application").
> Il s'agit d'une application Web comprenant un grand nombre de failles de sécurité pouvant être utilisée à but pédagogoquique.
>
> Cette application pourra être utilisée par la suite effectuer des test de pénétration.
> Plus d'informations concernant DVWA à l'adresse suivante : [Dépôt Git DVWA](https://github.com/digininja/DVWA)

La documentation indique que des images Docker sont disponibles non pas sur le "Docker Hub" mais sur une banque d'images appellée "Github Container Registry" (`ghcr`).

Les images disponibles sont listées [ici](https://github.com/digininja/DVWA/pkgs/container/dvwa).

La web application DVWA repose sur l'utilisation d'une base de données, vous allez vous servir d'une composition afin de mettre en place **deux conteneurs** :
1. la web application DVWA
2. la base de données utilisée par l'application

La base de données utilisée peut-être [MariaDB](https://hub.docker.com/_/mariadb) (fork de MySQL). Une image MariaDB est disponible sur le "Docker Hub".

### Fichier de configuration de composition

Il vous est conseillé de mettre tous les fichiers de configuration dans un dossier, par exemple :
```text
.
├── compose.yml
└── .env
```

Pour concevoir une composition il est nécessaire d'écrire un fichier `compose.yml` qui contiendra toutes les informations nécessaires à l'instanciation des différents conteneurs.

Voici un squelette pour notre cas d'utilisation (veillez à bien compléter les parties "TODO") :

```yml
# Déclaration de la liste de services
services:
  # Conteneur de base de données
  # TODO choisir une version exploitable
  db:
    image: docker.io/library/mariadb:<?????>
    # Nom du conteneur qui sera instancié
    container_name: web-app-db
    # Re-démarrage du conteneur automatique SAUF s'il est éteint manuellement
    # Les options sont :
    # - no : ne redémarre jamais le conteneur
    # - always : redémarre toujours le conteneur (attention, démarre le conteneur au démarrage de la machine)
    # - on-failure : redémarre unqiuement si le conteneur s'arrête avec un code d'erreur qui n'est pas 0 (0 étant pas de problème)
    # - unless-stopped : comme "always" sauf si le conteneur est éteint manuellement
    restart: unless-stopped
    # Variables d'environnement disponibles pour le conteneur "db"
    environment:
      MARIADB_ROOT_PASSWORD: ${MARIADB_ROOT_PASSWORD}
      MARIADB_DATABASE: ${MARIADB_DATABASE}
      MARIADB_USER: ${MARIADB_USER}
      MARIADB_PASSWORD: ${MARIADB_PASSWORD}
    # Re-redirection de port pour accès direct au conteneur
    # ATTENTION : ACCÈS POSSIBLE -> À ENLEVER POUR SÉCURISATION
    ports:
      - "9806:3306"
      
  # Conteneur de l'application web DVWA
  web-app:
    # Image utilisée pour instancier le conteneur
    # TODO choisir la version qui convient
    image: ghcr.io/digininja/dvwa:<??????>
    # Nom du conteneur
    container_name: web-app
    # 
    restart: unless-stopped
    # Déclaration des variables d'environnement pour le conteneur "web-app"
    environment:
      # Serveur de base de données utilisé
      # Possibilité de mettre un adresse IP ou, beaucoup mieux, le nom du conteneur ciblé !
      # TODO mettre le nom du conteneur de base de données
      DB_SERVER: <????>
      DB_PORT: 3306
      DB_DATABASE: ${MARIADB_DATABASE}
      DB_USER: ${MARIADB_USER}
      DB_PASSWORD: ${MARIADB_PASSWORD}
      # Définition du niveau de sécurité de l'application
      DEFAULT_SECURITY_LEVEL: low
    # Liste de dépendance des autres conteneurs
    depends_on:
      - db
    # Redirection de ports pour le conteneur de l'application web
    # TODO choisir le port qui convient côté "machine hôte"
    ports:
      - ?????:80
```

Ce fichier fait référence à des variables d'environnement telles que `${MARIADB_DATABASE}`, ces variables doivent être déclarées dans un fichier `.env` situé dans le même dossier que `compose.yml`.
