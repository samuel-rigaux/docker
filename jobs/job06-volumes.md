# Job 06 - Explications Volumes Docker
 
**Objectif** : Comprendre l'utilisation des volumes entre conteneurs et leur gestion.

## Volumes entre deux conteneurs

**Principe** : Un volume nommé peut être **monté simultanément** par plusieurs conteneurs. Les modifications d'un conteneur sont **immédiatement visibles** dans les autres.

**Syntaxe** :
```bash
# Création volume partagé
docker volume create mon-volume

# Montage identique sur plusieurs conteneurs
docker run -v mon-volume:/data conteneur1
docker run -v mon-volume:/data conteneur2
```

**Exemples concrets** :
- **Nginx + PHP-FPM** : Volume `/var/www/html` partagé
- **Web + Worker** : Volume `/uploads` (web écrit, worker traite)
- **DB + Backup** : Volume `/var/lib/mysql` (DB écrit, backup lit)

**Méthodes de partage** :
1. **Named volume** (recommandé) : `docker volume create data`
2. **Bind mount** : `-v /host/path:/container/path`
3. **`--volumes-from`** (déprécié) : Héritage volumes d'un conteneur

## Gestion des volumes

**Commandes principales** :
```
docker volume ls          # Liste volumes
docker volume create nom  # Crée volume nommé
docker volume rm nom      # Supprime (si inutilisé)
docker volume prune       # Supprime volumes orphelins
docker volume inspect nom # Détails (mountpoint hôte)
docker system df -v       # Espace disque volumes
```

**Lifecycle** :
- Volume créé automatiquement si `-v /path` (anonymous)
- Volume **survit** `docker rm` (contrairement aux conteneurs)
- Volume supprimé seulement par `docker volume rm` ou `prune`

## Bonnes pratiques

| Usage | Recommandation |
|-------|----------------|
| Persistance | Named volume (`docker volume create`) |
| Lecture seule | `-v volume:/path:ro` |
| Backup | `docker run -v volume:/data alpine tar /backup` |
| Nettoyage | `docker volume prune -f` (⚠️) |

**Points clés** :
- **Performance** : Volumes > bind mounts pour I/O intensif
- **Sécurité** : `ro` pour conteneurs lecteurs
- **Labels** : `--label env=prod` pour filtrer `prune`

**Cas d'usage typiques** :
- **Stockage** : Bases de données, fichiers uploads
- **Cache** : Redis, sessions
- **Logs** : Centralisation logs applicatifs

**Job 06 : COMPRIS** 📚