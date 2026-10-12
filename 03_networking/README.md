# Segmentation réseau

Une des règles fondamentale de la sécurité est : la base de données ne doit jamais être joignable depuis l'extérieur.

Comme dans une infrastructure traditionnellei (VLAN, pare-feu) il va falloir segmenter le réseau afin de limiter la surface d'attaque si le réseau exposé est conpromis.

> [!INFO]
> Jusqu'à présent aucun réseau n'a été configuré dans les fichiers de configuration Docker.
> 
> Dans cette configuration Docker crée un réseau "bridge" avec la machine hôte et tous les conteneurs se retrouvent sur le même réseau.


Réseaux à mettre en place :
- frontend (172.20.1.0/24) : zone exposée, qui contient le proxy et l'application.
- backend (172.20.2.0/24) : zone protégée, qui contient l'application et la base. Ce réseau est déclaré **`internal`** : aucune sortie vers l'extérieur, aucun accès possible.

L'application web est le seul pont entre les deux zones : elle est rattachée aux deux réseaux.

Le reverse-proxy n'est que sur frontend. La base n'est que sur backend.

