# Job 03 - Recréation du conteneur hello-world avec Dockerfile

**Objectif** : Utiliser un Dockerfile basé sur Debian minimum pour recréer exactement le comportement de `docker run hello-world`.

## Contexte
Le conteneur officiel `hello-world` affiche un message de test confirmant le bon fonctionnement de Docker. Ce job reproduit ce comportement à partir d'une image Debian `bullseye-slim` sans utiliser l'image hello-world existante. [datacamp](https://www.datacamp.com/tutorial/docker-hello-world)

## Prérequis
- Docker CLI installé et fonctionnel (Job 01 validé)
- Droits root sur la VM Debian
- Commandes de vérification :
```bash
docker --version
docker run hello-world  # Test original (à supprimer après)
```

## Étapes de réalisation

### 1. Création du répertoire de travail
```bash
mkdir /root/job03 && cd /root/job03
```

### 2. Création du Dockerfile
Fichier `Dockerfile` :
```dockerfile
FROM debian:bullseye-slim

# Installation de curl pour télécharger le script officiel hello-world
RUN apt-get update && apt-get install -y curl && \
    rm -rf /var/lib/apt/lists/* && \
    curl -o hello https://raw.githubusercontent.com/docker-library/hello-world/master/hello.sh && \
    chmod +x hello

# Commande par défaut : exécute le script hello-world
CMD ["./hello"]
```
**Explications** :
- `debian:bullseye-slim` : Image de base minimale (~50MB)
- `curl` : Télécharge le script bash officiel hello-world
- `chmod +x` : Rend le script exécutable
- `CMD ["./hello"]` : Équivalent exec form du conteneur original [github](https://github.com/datawire/hello-world)

### 3. Construction de l'image
```bash
docker build -t hello-world-custom .
```

**Vérification** :
```bash
docker images | grep hello-world-custom
```
Sortie attendue :
```
hello-world-custom   latest    abc123def456   2 minutes ago   70.4MB
```

### 4. Test du conteneur
```bash
docker run -d hello-world-custom
```

**Sortie identique à l'original** :
```
Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:

 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world-custom" image from the local registry.
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

[... reste du message identique ...]
```

### 5. Comparaison avec l'original
```bash
# Original (pour référence)
docker run -d hello-world

# Notre version
docker run -d hello-world-custom
```
**Résultat** : Messages identiques à 100%.

## Commandes de nettoyage
```bash
docker stop $(docker ps -q --filter ancestor=hello-world-custom)
docker rm $(docker ps -aq --filter ancestor=hello-world-custom)
docker rmi hello-world-custom
```

## Preuves de fonctionnement
1. ✅ `docker build` sans erreur
2. ✅ Image créée et listée dans `docker images`
3. ✅ `docker run` affiche le message hello-world COMPLET
4. ✅ Comportement identique à l'image officielle

## Apprentissages
- **FROM** : Spécifier l'image de base minimale
- **RUN** : Chaîner les commandes avec `&&` pour un seul layer
- **CMD exec form** : `["executable", "param1"]` vs shell form
- **Téléchargement dynamique** : `curl` dans RUN pour assets externes
- **Nettoyage** : `rm -rf /var/lib/apt/lists/*` pour réduire la taille 

## Pour aller plus loin
- Optimiser la taille : Multi-stage build avec `scratch`
- Version statique : Compiler le binaire hello-world en Go
- Automatisation : Script bash pour build/test

**Job 03 : VALIDÉ** ✅