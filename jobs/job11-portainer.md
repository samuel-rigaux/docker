# Job 11 : Portainer - Recreation Jobs 2-9 en GUI

**Portainer** est l'interface web n°1 pour gérer Docker. Ce job installe Portainer sur Debian 13 et recrée graphiquement les **Jobs 2-9** (Nginx Dockerfile → Registry UI) sans ligne de commande.

## Prérequis

- Debian 13 VM (Job 10 : Docker installé)
- IP VM accessible : `192.168.56.101`
- Jobs 2-9 dossiers/Dockerfiles disponibles

## Installation Portainer CE

```bash
# Volume persistance
docker volume create portainer_data

# Lancement (single command)
docker run -d \
  --name portainer \
  --restart always \
  -p 9000:9000 -p 9443:9443 \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -v portainer_data:/data \
  portainer/portainer-ce:latest
```

**Accès** :
| Protocole | URL | Notes |
|-----------|-----|-------|
| HTTP | http://192.168.56.101:9000 | Standard |
| HTTPS | https://192.168.56.101:9443 | Self-signed OK |

**Setup** : Admin user → Connect "Local" → Dashboard prêt.

## Recreation Jobs via Portainer GUI

### Job 2-8 : Nginx custom (Dockerfile)

**1. Build image** : **Images** → **Build a new image**
```
Path: /path/to/job8-dockerfile/  # Ton Dockerfile debian:13-slim+nginx
Name: mon-nginx-job8
[Build] → Success !
```

**2. Déployer conteneur** : **Containers** → **Add container**
```
Image: mon-nginx-job8
Name: nginx-job8
Ports: 8080:80
[Deploy] → http://IP:8080 = Nginx welcome
```

**Alternative Stack** : **Stacks** → **Web editor**
```yaml
services:
  nginx:
    image: mon-nginx-job8
    ports: ["8080:80"]
    restart: always
```

### Job 9 : Registry Local + UI (Stack Compose)

**Stacks** → **Add stack**
```
Name: local-registry-job9
Method: Web editor
```
```yaml
version: '3.8'
services:
  registry:
    image: registry:2
    ports: ["5000:5000"]
    volumes: ["./registry-data:/var/lib/registry"]
  registry-ui:
    image: joxit/docker-registry-ui:latest
    ports: ["80:80"]
    environment:
      - SINGLE_REGISTRY=true
      - REGISTRY_URL=http://registry:5000
      - DELETE_IMAGES=true
    depends_on: [registry]
```
**[Deploy]**

**Test via Portainer Console** :
```
docker tag nginx 192.168.56.101:5000/test-nginx
docker push 192.168.56.101:5000/test-nginx
```
→ UI http://IP:80 → "test-nginx" listé !

## Jobs 2-7 recreés (similaires)

| Job Type | Portainer Path | Config exemple |
|----------|----------------|---------------|
| **Nginx simple** | Containers/Add | `nginx`, `80:80` |
| **Ports custom** | Containers/Add | `nginx`, `8081:80` |
| **Volumes** | Containers/Add | `/data:/usr/share/nginx/html` |
| **Envs** | Containers/Add | `ENV=prod` |

## Fonctionnalités Portainer clés

| Section | Actions |
|---------|---------|
| **Dashboard** | Stats CPU/RAM, conteneurs live |
| **Containers** | Start/Stop/Logs/Exec/Inspect/Stats |
| **Images** | Pull/Build new/Push/Delete |
| **Stacks** | Compose YAML (Job 9), update/redeploy |
| **Volumes** | List/Create/Inspect (registry-data) |
| **Console** | Bash dans conteneurs |

## Workflow testé

```
1. Portainer:9000 → Login
2. Images/Build → mon-nginx-job8 (Job 8)
3. Containers/Add → nginx-job8:8080 (Jobs 2-7)
4. Stacks/Add → local-registry-job9 (Job 9)
5. Console → docker push → UI verify
```

## Alternatives Portainer (2026)

| Outil | Focus | Installation | Avantages | Inconvénients |
|-------|-------|--------------|-----------|---------------|
| **Dockge** | Docker Compose | `docker run louislam/dockge:release` | Léger, edit YAML live, logs avancés  | Pas aussi complet |
| **Yacht** | Apps templates | Docker simple | Facile débutants | Peu mis à jour |
| **Lazydocker** | Terminal TUI | `go install` | Ultra-rapide, clavier only | Pas web |
| **Rancher** | K8s/Enterprise | Helm | Multi-cluster, GitOps | Overkill solo Docker |
| **Kubernetes Dashboard** | K8s pur | K8s deployment | Standard | Docker → K8s seulement |

**Verdict** : **Portainer** = meilleur équilibre Docker solo. **Dockge** si 100% Compose.

## Nettoyage (Job 10)

```bash
docker stop portainer && docker rm portainer
docker volume rm portainer_data  # Optionnel
```

## Résultats finaux

| Job | CLI → Portainer | URL finale |
|-----|-----------------|------------|
| **2-8** | Dockerfile → Build+Container | http://IP:8080 |
| **9** | Compose → Stack | http://IP:80 (UI), :5000 (API) |

**✅ Job 11 validé** : Portainer installé + Jobs 2-9 recreés 100% GUI !

*27/02/2026 - VM Debian 13 - Testé avec Registry fonctionnel*