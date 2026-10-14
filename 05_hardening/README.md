# Durcissement des images

> Source des recommandations : ANSSI, *Recommandations de sécurité relatives aux déploiements de conteneur Docker*, fiche technique ANSSI-FT-082, 23/09/2020 disponible [ici](https://messervices.cyber.gouv.fr/guides/recommandations-de-securite-relatives-au-deploiement-de-conteneurs-docker).
> Les « R » (R1, R8…) renvoient aux numéros de cette fiche.

> [!IMPORTANT]
> Avant de continuer il vous est fortement conseillé de consulter cette fiche.

## Contexte

Le RSSI valide l'architecture réseau de la phase précédente. Il demande maintenant de **réduire ce qu'un attaquant pourrait faire s'il compromettait un conteneur**.

Dans un premier temps, le durcissement se fera au niveau du conteneur le plus sensible : le "reverse proxy".

## Les restriction préconisées

Outre les restrictions réseau, Docker permet de configurer :
- des restriction d'accès aux ressources physiques (RAM, CPU...)
- les capacités ("Capbabilities") d'un conteneur
- des restrictions sur le sytème de fichier

### Accès aux ressources physiques

Docker exploite les Control Groups (ou CGroups) qui permettent de limiter, mesurer et prioriser l'accès aux ressources système (CPU, mémoire, I/O disque,etc.) pour chaque conteneur.

### Les "capabilities" Linux

Extrait de du blog de [Stéphane Robert](https://blog.stephane-robert.info/docs/admin-serveurs/linux/internals/capabilities/) :
> Historiquement, un processus Linux était soit root (tout permis), soit un utilisateur normal (rien de privilégié).
> Ce modèle binaire posait un problème simple : un serveur web n'a besoin que d'ouvrir le port 80, mais pour y arriver il devait démarrer en root, donc avec le droit de tout faire, y compris ce dont il n'aurait jamais l'usage.
>
> Les capabilities découpent ce bloc monolithique en une quarantaine de privilèges indépendants.
> Sur la machine qui a servi à écrire cette page, le noyau en expose 41, de CAP_CHOWN à CAP_CHECKPOINT_RESTORE.
>
> Chacune correspond à une vérification précise dans le noyau : CAP_NET_BIND_SERVICE autorise à lier une socket à un port inférieur à 1024, CAP_SYS_TIME à modifier l'horloge système, CAP_SYS_MODULE à charger un module noyau.
>
> Le bénéfice est directement mesurable en surface d'attaque. Un processus qui ne détient que CAP_NET_BIND_SERVICE et qui se fait compromettre ne donne à l'attaquant qu'un seul pouvoir supplémentaire : ouvrir un port bas. Le même processus lancé en root lui offrirait les 41.

En résumé, il est possible de voir les "capabilities" comme des filtres noyau autorisant ou non les processus à effectuer des actions d'administration système.

Les conteneurs, que nous pouvons considéré comme des processus, sont démarrés avec des "capabilities" (cf. document de l'ANSSI) qui ne sont pas toutes nécessaires.

## Étape 0 - État des lieux

Avant toute modification, étudions comment le conteneur "reverse proxy" est configuré :

```bash
docker inspect -f 'cap_drop={{.HostConfig.CapDrop}} mem={{.HostConfig.Memory}} cpu={{.HostConfig.NanoCpus}}' <nom-conteneur>
```

La commande [`docker inspect`](https://docs.docker.com/reference/cli/docker/inspect/) retourne la configuration d'un conteneur sous format JSON.
Son paramètre `-f` (pour "format") peut prendre un template permettant de récupérer uniquement les informations d'intérêt.

Ici nous récupérons :
- `CapDrop` : liste des capacités bloquées
- `mem` : restriction sur la mémoire
- `NanoCpus` : restriction sur le nombre de coeurs utilisables

> [!IMPORTANT]
> Effectuez la commande. Qu'observez-vous ?

## Étape 1 – Limiter les ressources (R10, R11)

L'objectif de la limitation de ressource est d'empêcher un conteneur compromis de saturer la machine.

### Restriction RAM

Sur un hôte Linux lorsque le système détecte qu'il n'y a pas assez de mémoire pour effectuer des fonctions système nécessaire il lance une **OUT OF MEMORY EXCEPTION** et tue (c'est le terme technique) des processus.

Docker, en tant que processus, peut potentiellement être tué par le système en cas de limitation de mémoire, ceci peut bien entendu créer des instabilités.

La [documentation](https://docs.docker.com/engine/containers/resource_constraints/) Docker donne les conseils suivants pour limiter le risque :
- Effectuer des tests pour déterminer les besoins en mémoire de l'application avant de la mettre en production.
- veiller à ce que le conteneur s'exécute sur un hôte ayant des ressources suffisantes
- limiter la quantité de mémoire
- veiller à une bonne utilisation de la mémoire `swap`

### Restriction CPU

Par défaut l'accès CPU est illimité, plusieurs restriction peuvent être appliquées, plus d'information [ici](https://docs.docker.com/engine/containers/resource_constraints/#cpu).

### Nouvelle configuration

Dans la composition Docker :

```yml
services:
  reverse-proxy:
    mem_limit: 128m
    memswap_limit: 128m   # même valeur : pas de swap en plus
    cpus: 0.5
```

Plusieurs unités peuvent être utilisée pour la limitation RAM : b (byte ou octet), k (kilobyte ou kilooctet), m (megabyte ou megaoctet), g (gigabyte ou gigaoctet).

> [!IMPORTANT]
> Une fois la modification effectuée, vous pouvez observer l'effet de plusieurs manières :
> - en inspectant le conteneur ciblé
> - en affichant les ressources utilisées avec `docker stats`

## Étape 2 – Retirer les capabilities (R8)

L'ANSSI recommande de supprimer toutes les capacités d'un conteneur et de seulemet ajouter celles qui sont nécessaires.

Une première configuration peut-être :
```yaml
services:
  reverse-proxy:
    cap_drop: [ALL]
```

> [!IMPORTANT]
> Démarrez la composition avec la suppression de toutes les capacités. Que montrent les "logs" au démarrage ?
>
> Il manque probablement des capacités pour que le conteneur puisse démarrer.

**Méthodologie de travail :**
- si le conteneur ne démarre pas -> l'erreur donne probablement des informations sur la capacité manquante
- si le conteneur démarre -> essayer de supprimer une capacité

```yml
  reverse-proxy:
    # Gestion des capacités Linux
    cap_drop: [ALL]
    cap_add: [<capacite-1>, <capacite-2>, ...]
```

> [!IMPORTANT]
> Complétez la liste des capacité pour que le conteneur puisse démarrer.
>
> Voici une liste (non exhaustives) de capacités utilisable dans le fichier de configuration, à vous de trouver celles qui pourraient être exploitables :
> - AUDIT_CONTROL : permet d'activer/désactiver l'audit du noyau
> - BLOCK_SUSPEND : utilisation de fonctionnalités permettant de mettre en veille le système
> - CHOWN : effectuer les modifications des UID/GUID (appartenance des fichiers)
> - IPC_LOCK : vérouiller les pages mémoires (RAM)
> - NET_BIND_SERVICE : permet d'attacher une socket sur un **port privilégié** (en dessous de 1024)
> - KILL : permet de tuer un processus
> - SETGID : permet de manipuler les GID (Group Id). Autrement dit, changer le groupe d'un processus.
> - SETUID : perment de manipler les UID (User ID). Changer l'utilisateur d'un processus.

## Étape 3 – Système de fichiers en lecture seule (R12, R13)

> [!CAUTION]
> Attention, ce qui suit n'est pas possible avec Docker sous Windows (via WSL2).

Si un attaquant ne peut rien écrire dans le conteneur, il ne peut pas y déposer de code malveillant.

L'objectif est ici de rendre tout le système de fichiers non modifiable, nous autoriserons l'écriture seulement à quelques endroits **temporaires et limités en taille**.

Pour se faire il est possible de :
1. activer le conteneur en "lecture seule" afin d'empêcher toute écriture sur le disque
2. de mettre en place des points de montage `tmpfs` qui permet de monter un chemin du conteneur en RAM (qui disparaitra lors de l'arrêt du conteneur)

L'utilisation de `tmpfs` permet d'autoriser temporairement l'écriture dans un **dossier spécifique du conteneur**.

Pour le mettre en place il faut donc arriver à cibler quels sont les dossiers dans lesquels le conteneur va écrire.

```yaml
services:
  reverse-proxy:
    # Activation de la lecture seule pour le conteneur
    read_only: true
    # Ajout des tmpfs
    tmpfs:
      - /var/cache/nginx:size=32m
      - /etc/nginx/conf.d:size=1m
      - /run:size=1m
```

> [!IMPORTANT]
> Après avoir ajouté la configuration, essayez d'écrire dans un dossier non monté via `tmpfs`.
**Preuve :**

> [!TIP]
> Si le conteneur ne démarre plus, l'erreur indique  dans quel dossier il veut écrire : il faut alors l'ajouter à `tmpfs`.
>
> Vous trouverez plus d'informations sur le options de `tmpfs` dans la documentation disponible [ici](https://oneuptime.com/blog/post/2026-02-08-how-to-use-docker-compose-tmpfs-configuration/view#configuring-tmpfs-options).

## Pour aller plus loin

Appliquer les mêmes mesures aux services `db` puis `app`.

