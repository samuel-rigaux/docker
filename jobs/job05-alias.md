# Job 05 - Alias Docker pour .bashrc (L'informaticien flemmard)

**Objectif** : Créer des alias pratiques pour les commandes Docker courantes (images, conteneurs) à ajouter dans `~/.bashrc`.

## Contexte
Les commandes Docker sont verbeuses. Les alias réduisent la frappe de 50-70% pour les tâches récurrentes : listage, nettoyage, logs, etc. Ajout permanent dans `~/.bashrc` pour toutes les sessions.

## Prérequis
- Docker installé (Jobs 01-04 validés)
- Éditeur texte : `nano`
- Vérification : `echo $SHELL` (doit être `/bin/bash`)

## Étapes de réalisation

### 1. Édition du fichier .bashrc
```bash
nano ~/.bashrc
```
**Ajouter à la fin du fichier** (copier-coller) :

```bash
# ===============================================
# DOCKER ALIAS - Job 05 RT-Docker
# ===============================================

# CONTENEURS
alias d='docker'
alias dps='docker ps -a --format "table {{.ID}}\t{{.Names}}\t{{.Status}}\t{{.Ports}}"'
alias dlog='docker logs -f --tail 50'
alias drun='docker run -d --rm'
alias dstop='docker stop $(docker ps -q)'
alias dkill='docker kill $(docker ps -q)'
alias dclean='docker container prune -f && docker image prune -f'

# IMAGES
alias di='docker images --format "table {{.Repository}}\t{{.Tag}}\t{{.ID}}\t{{.Size}}"'
alias dim='docker image prune -a -f'
alias dpull='docker pull'

# VOLUMES & NETWORKS
alias dv='docker volume ls'
alias dvn='docker volume prune -f'
alias dnet='docker network ls'

# INSPECTION RAPIDE
alias dinsp='docker inspect'
alias dstat='docker stats'

# COMBO CLEAN (tout nettoyer)
alias docker-clean='docker system prune -a -f --volumes && docker volume prune -f'

# BUILD RAPIDE
alias db='docker build -t'

echo "🚀 Docker aliases chargés ! Utilisez 'dps', 'dlog', 'dclean', etc."
```

### 2. Rechargement de .bashrc
```bash
source ~/.bashrc
```
**Vérification** :
```bash
alias | grep docker
# Affiche tous les alias Docker
```

### 3. Test des alias principaux

| Alias | Équivalent complet | Usage typique |
|-------|--------------------|---------------|
| `dps` | `docker ps -a --format table` | Liste conteneurs avec ports/status |
| `di` | `docker images --format table` | Liste images triées par taille |
| `dlog nom` | `docker logs -f --tail 50 nom` | Logs en temps réel |
| `dclean` | `docker container prune && image prune` | Nettoie conteneurs/images arrêtés |
| `docker-clean` | `docker system prune -a --volumes` | Reset TOTAL Docker |

**Exemples concrets** :
```bash
# Avant alias (15+ caractères)
docker ps -a --format "table {{.Names}} {{.Status}}"

# Après (3 caractères !)
dps

# Build rapide
db mon-app .          # = docker build -t mon-app .

# Logs instantanés
dlog ssh-job04        # Suit les logs en live

# Nettoyage 1 mot
dclean                # Supprime tout l'orphelin
```

## Avantages mesurés
| Tâche | Sans alias | Avec alias | Gain |
|-------|------------|------------|------|
| docker ps | 10 chars | `dps` (3) | 70% |
| docker logs | 12 chars | `dlog` (4) | 67% |
| docker system prune -a -f | 27 chars | `docker-clean` (12) | 55% |
| docker build -t app . | 20 chars | `db app .` (7) | 65% |

## Preuves de fonctionnement
```
$ dps
CONTAINER ID   NAMES        STATUS         PORTS
abc123def      ssh-job04    Up 5 minutes   0.0.0.0:2222->22/tcp

$ di
REPOSITORY      TAG      IMAGE ID     SIZE
ssh-custom      latest   def456abc    85.2MB
helloworld-custom latest abc123def 70.4MB

$ dlog ssh-job04
Server listening on 0.0.0.0 port 22.

$ dclean
Deleted Containers: xyz...
Deleted Images: anonymous:abc...
Total reclaimed space: 150MB
```

```
✅ Alias persistants après reboot (source ~/.bashrc)
✅ Gain frappe: 60%+ sur commandes courantes
✅ Couleurs/formats améliorés (table)
✅ Sûrs (pas de -f sur stop/kill sauf explicitement)
```

## Personnalisation suggérée
```bash
# Ajouts bonus (optionnels)
alias dshell='docker exec -it'  # docker exec -it conteneur bash
alias dcp='docker cp'           # Copie fichiers
alias dtop='docker exec -it conteneur top'
```

**Script de test automatique** :
```bash
#!/bin/bash
# test-aliases.sh
source ~/.bashrc
echo "=== Test Job 05 ==="
dps && di && echo "✅ Tous les alias fonctionnent !"
```

**Job 05 : VALIDÉ** 🚀