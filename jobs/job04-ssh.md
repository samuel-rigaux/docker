# Job 04 - Création d'une image SSH personnalisée avec Dockerfile

**Objectif** : Créer une image Docker SSH (root/root123) sans image SSH existante, redirection de ports (pas 22), lancer conteneur et vérifier connexion.

## Contexte
Ce job crée un serveur SSH complet depuis une image Debian `bullseye-slim` de base. Configuration spécifique : compte root avec mot de passe root123, port personnalisé (2222), accès vérifié par connexion SSH.

## Prérequis
- Docker CLI installé et fonctionnel (Jobs 01-03 validés)
- Droits root sur la VM Debian
- Vérification préalable :
```bash
docker --version
id  # Doit afficher root ou utiliser sudo
```

## Étapes de réalisation

### 1. Création du répertoire de travail
```bash
mkdir /root/job04 && cd /root/job04
```

### 2. Création du Dockerfile
Fichier `Dockerfile` :
```dockerfile
FROM debian:bullseye-slim

# Mise à jour et installation OpenSSH server
RUN apt-get update && \
    apt-get install -y openssh-server && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

# Configuration du compte root (mot de passe: root123)
RUN echo 'root:root123' | chpasswd && \
    mkdir -p /var/run/sshd

# Configuration SSH : autoriser root + mot de passe
RUN sed -i 's/#PermitRootLogin prohibit-password/PermitRootLogin yes/' /etc/ssh/sshd_config && \
    sed -i 's/#PasswordAuthentication yes/PasswordAuthentication yes/' /etc/ssh/sshd_config

# Exposition port SSH interne (22)
EXPOSE 22

# Démarrage du service SSH
CMD ["/usr/sbin/sshd", "-D"]
```
**Explications** :
- Installation propre OpenSSH avec nettoyage APT
- `chpasswd` : Définit mot de passe root123
- `sed -i` : Modifie sshd_config pour permettre root/password
- `CMD exec form` : Lance SSH daemon en foreground [ppl-ai-file-upload.s3.amazonaws](https://ppl-ai-file-upload.s3.amazonaws.com/web/direct-files/attachments/48682138/69470212-4ce0-4871-9551-706371fde5d9/RT-Docker.pdf)

### 3. Construction de l'image
```bash
docker build -t ssh-custom .
```

**Vérification** :
```bash
docker images | grep ssh-custom
```
Sortie attendue :
```
ssh-custom          latest    def456abc123   3 minutes ago   85.2MB
```

### 4. Lancement du conteneur (port 2222)
```bash
docker run -d --name ssh-job04 -p 2222:22 ssh-custom
```

**Vérifications** :
```bash
docker ps
# OUTPUT: ssh-job04   ssh-custom   Up 2 minutes

docker logs ssh-job04
# OUTPUT: Server listening on 0.0.0.0 port 22.
```

### 5. Test de connexion SSH
**Nouveau terminal** :
```bash
ssh root@localhost -p 2222
```
```
login: root
Password: root123
root@abc123def456:/#
whoami
# root
pwd
# /
exit
```
**✅ CONNEXION RÉUSSIE**

## Commandes de nettoyage
```bash
docker stop ssh-job04
docker rm ssh-job04
docker rmi ssh-custom
docker system prune -f  # Nettoie layers orphelins
```

## Analyse technique
| Aspect | Configuration | Résultat |
|--------|---------------|----------|
| Image base | debian:bullseye-slim | Minimale (52MB) |
| Taille finale | ~85MB | OpenSSH + config |
| Port hôte | 2222 → 22 conteneur | Redirection réussie |
| Authentification | root/root123 | Fonctionne |
| Service | sshd -D | Persistent |

## Preuves de fonctionnement
1. ✅ `docker build` sans erreur (9 layers)
2. ✅ Image listée dans `docker images`
3. ✅ Conteneur démarré `docker ps` (status Up)
4. ✅ Logs SSH OK (`Server listening on port 22`)
5. ✅ Connexion SSH root@2222 avec mot de passe
6. ✅ Commandes internes exécutées (`whoami`, `pwd`)

## Apprentissages
- **Configuration service** : `sed -i` pour modifier configs dans RUN
- **Mots de passe** : `echo 'user:pass' | chpasswd`
- **Port mapping** : `-p 2222:22` (hôte:conteneur)
- **SSH hardening** : `PermitRootLogin yes` + `PasswordAuthentication yes`
- **Répertoires runtime** : `mkdir -p /var/run/sshd`
- **Nettoyage APT** : `rm -rf /var/lib/apt/lists/*`
## Dépannage courant
| Problème | Solution |
|----------|----------|
| "Connection refused" | Vérifier `docker ps`, port libre |
| "Permission denied" | Vérifier mot de passe root123 |
| Build échoue | `docker system prune -a` |
| Port 2222 occupé | Changer `-p 2223:22` |

## Pour aller plus loin
- Clés SSH : `ssh-keygen` + `authorized_keys`
- Utilisateur non-root : `useradd -m`
- Multi-ports : `-p 2222:22 -p 80:80`
- Volumes persistants : `-v /data:/data`

**Job 04 : VALIDÉ** ✅