# Job 9 : Registry Docker local avec UI

Ce job 9 démontre la création d'un **registry Docker privé local** sur une VM Debian 13 avec une **interface web** pour sa gestion. Tout fonctionne via Docker Compose avec persistance des données.

## Prérequis

- VM Debian 13 (Trixie) avec Docker et Docker Compose installés
- Accès SSH/root sur la VM
- IP VM accessible depuis l'hôte (ex: 192.168.56.101)

## Configuration du registry insecure

Créer `/etc/docker/daemon.json` :

```json
{
  "insecure-registries": ["192.168.56.101:5000", "localhost:5000", "127.0.0.1:5000"]
}
```

Redémarrer Docker :
```bash
sudo systemctl restart docker
```

## Fichiers de configuration

### docker-compose.yml

```yaml
services:
  registry:
    image: registry:2
    restart: always
    ports:
      - "5000:5000"
    volumes:
      - ./registry-data:/var/lib/registry

  registry-ui:
    image: joxit/docker-registry-ui:latest
    restart: always
    ports:
      - "80:80"
    environment:
      - SINGLE_REGISTRY=true
      - REGISTRY_URL=http://registry:5000
      - REGISTRY_TITLE="Mon Registry Local - Job 9"
      - DELETE_IMAGES=true
    depends_on:
      - registry
```

### Structure des dossiers

```
docker-registry-local/
├── docker-compose.yml
└── registry-data/     # Créé automatiquement (persistance)
```

## Déploiement

```bash
# Créer le répertoire de travail
mkdir docker-registry-local && cd docker-registry-local

# Copier le docker-compose.yml
# Lancer les services
docker compose up -d

# Vérifier le statut
docker compose ps
```

## Accès et utilisation

| Service | URL | Description |
|---------|-----|-------------|
| **Registry API** | http://192.168.56.101:5000/v2/ | API Docker Registry (retourne `{}`) |
| **Interface Web** | http://192.168.56.101 | UI de gestion (liste images, suppression) |

### Test complet

```bash
# 1. Tagger une image
docker tag nginx 192.168.56.101:5000/test-nginx

# 2. Pusher vers le registry
docker push 192.168.56.101:5000/test-nginx

# 3. Vérifier dans l'UI
# → http://192.168.56.101 → test-nginx (latest)

# 4. Pull depuis le registry
docker pull 192.168.56.101:5000/test-nginx

# 5. Lancer le conteneur
docker run -d -p 8080:80 192.168.56.101:5000/test-nginx
# → http://localhost:8080 (page Nginx)
```

## Gestion via l'UI

1. **Liste des repositories** : `test-nginx` apparaît après push
2. **Détails tag** : latest, taille (~150MB), date
3. **Suppression** : Bouton "Delete" sur chaque tag (active avec `DELETE_IMAGES=true`)
4. **Refresh** : Actualise la liste automatiquement

## Commandes utiles

```bash
# Logs
docker compose logs registry
docker compose logs registry-ui

# Redémarrer
docker compose restart

# Arrêt complet
docker compose down

# Nettoyage stockage (après delete UI)
docker exec $(docker compose ps -q registry) bin/registry garbage-collect /etc/docker/registry/config.yml
```

## Dépannage

| Problème | Solution |
|----------|----------|
| `ERR_CONNECTION_REFUSED` | Vérifier `docker compose ps`, utiliser IP VM (pas localhost) |
| UI "Cannot connect registry" | `REGISTRY_URL=http://registry:5000` dans docker-compose.yml |
| `http: server gave HTTP response to HTTPS client` | `insecure-registries` dans `/etc/docker/daemon.json` |
| Ports bloqués | `ufw allow 80/tcp && ufw allow 5000/tcp` |

## Sécurité (optionnel)

### Authentification HTpasswd

1. Créer le fichier auth :
```bash
mkdir auth
docker run --rm --entrypoint htpasswd httpd:2 -Bbn monuser monpass > auth/htpasswd
```

2. Modifier `docker-compose.yml` (service registry) :
```yaml
volumes:
  - ./registry-data:/var/lib/registry
  - ./auth:/auth
environment:
  - REGISTRY_AUTH=htpasswd
  - REGISTRY_AUTH_HTPASSWD_REALM=Registry\ Realm
  - REGISTRY_AUTH_HTPASSWD_PATH=/auth/htpasswd
```

## Réseau VM

| Type réseau | Configuration |
|-------------|---------------|
| **NAT** | VirtualBox: Règles redirection ports 80→VM:80, 5000→VM:5000 |
| **Bridged** | IP VM directement accessible (192.168.x.x) |
| **Host-only** | IP réseau virtuel (ex: 192.168.56.101) |

***

**✅ Job 9 terminé** : Registry local + UI web opérationnel sur Debian 13 !