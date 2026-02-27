# Job 8 - Conteneur Nginx personnalisé

Ce job consiste à créer un conteneur Docker Nginx sans utiliser d'image officielle, en partant d'une base Debian 13-slim, avec gestion des ports et connexion au serveur web. 
## Contenu du Dockerfile

Voici le Dockerfile fourni, qui installe Nginx sur Debian 13-slim, expose le port 80 et lance le serveur en foreground :

```
FROM debian:13-slim

RUN apt-get update && apt-get install -y nginx

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]
```

Cette approche utilise une image légère Debian et configure Nginx pour rester en avant-plan, essentiel pour Docker. 

## Étapes de build et exécution

1. Construisez l'image : `docker build -t mon-nginx .` (exécutez dans le répertoire du Dockerfile).
2. Lancez le conteneur avec redirection de ports : `docker run -d -p 8080:80 mon-nginx` (mappe le port 80 du conteneur sur 8080 de l'hôte). 
3. Vérifiez les conteneurs actifs : `docker ps`.

Ces commandes créent une image nommée `mon-nginx` et redirigent le trafic hôte:8080 vers conteneur:80. 

## Connexion et test

Accédez à http://localhost:8080 dans un navigateur pour voir la page d'accueil Nginx par défaut.

Pour interagir avec le conteneur :
- Logs : `docker logs <container_id>`.
- Shell : `docker exec -it <container_id> bash`.
- Arrêt : `docker stop <container_id>`. 
Le port exposé (80) est documenté par `EXPOSE`, mais la redirection se fait via `-p` lors du `run`. 