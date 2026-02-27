# Job 10 : Scripts Docker Clean & Install Auto

Scripts bash pour **nettoyer totalement Docker** (images/volumes/conteneurs/paquets) et **installer automatiquement Docker+Compose** sur Debian 13. Idéal pour resets VM ou déploiements propres.

## Prérequis

- Debian 13 (Trixie)
- Accès root/sudo
- **⚠️ Backup avant clean !**

## Structure des fichiers

```
docker-scripts/
├── docker-clean.sh      # Nettoyage total
└── docker-install.sh    # Installation complète
```

## Script 1 : `docker-clean.sh`

**Supprime ABSOLUMENT TOUT Docker** : données, paquets, configs, groupe.

```bash
#!/bin/bash
# docker-clean.sh - Nettoyage total Docker Debian 13
# ⚠️ SUPPRIME TOUTES LES DONNÉES DOCKER !

set -e

echo "🧹 NETTOYAGE TOTAL DOCKER - Debian 13"
echo "ATTENTION : Efface images/volumes/conteneurs/paquets/configs !"
read -p "Confirmer (yes/no) ? " -r confirm
[[ ! $confirm =~ ^[Yy][Ee][Ss]?$ ]] && { echo "❌ Annulé."; exit 1; }

echo "⏹️ Arrêt services..."
sudo systemctl stop docker docker.socket containerd || true

echo "🗑️ Suppression données Docker..."
sudo rm -rf /var/lib/docker
sudo rm -rf /var/lib/containerd
sudo rm -rf /etc/docker

echo "📦 Désinstallation paquets..."
sudo apt purge -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin || true
sudo apt autoremove -y
sudo apt autoclean

echo "👥 Suppression groupe docker..."
sudo delgroup docker || true

echo "✅ Système propre ! Prêt pour réinstallation."
echo "   → ./docker-install.sh"
```

**Usage** :
```bash
chmod +x docker-clean.sh
./docker-clean.sh
```

## Script 2 : `docker-install.sh`

**Installation complète Docker CE + Compose** avec vérifications.

```bash
#!/bin/bash
# docker-install.sh - Docker CE + Compose sur Debian 13

set -e

echo "🐳 Installation Docker CE + Compose - Debian 13"

# Vérification OS
if ! grep -q "13" /etc/debian_version; then
    echo "❌ Debian 13 requis !"
    exit 1
fi

# Update système
sudo apt update && sudo apt upgrade -y

# Dépendances
sudo apt install -y ca-certificates curl gnupg lsb-release

# Docker GPG key (méthode moderne)
sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/debian/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg

# Repository
echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/debian \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# Installation
sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

# Services
sudo systemctl start docker
sudo systemctl enable docker

# Permissions
sudo usermod -aG docker $USER
echo "🔄 Reloge-toi ou exécute 'newgrp docker'"

# Test final
echo "🧪 Test installation..."
sudo docker run --rm hello-world

echo "✅ Installation terminée !"
echo "   Docker : $(sudo docker --version)"
echo "   Compose: $(sudo docker compose version)"
```

**Usage** :
```bash
chmod +x docker-install.sh
./docker-install.sh
newgrp docker  # Ou relogin
```

## Utilisation complète

### Reset + Réinstall

```bash
# 1. Sauvegarder si besoin (volumes externes)
# 2. Nettoyer
./docker-clean.sh

# 3. Réinstaller (après relogin)
./docker-install.sh
```

### Chaînage automatique

Crée `reset-docker.sh` :
```bash
#!/bin/bash
./docker-clean.sh && echo "Reloge-toi puis :" && echo "./docker-install.sh"
```

## Vérifications post-install

| Commande | Résultat attendu |
|----------|------------------|
| `docker --version` | Docker version 27.x.x |
| `docker compose version` | v2.27.x |
| `docker run hello-world` | "Hello from Docker!" |
| `groups` | Contient "docker" |
| `docker info` | Daemon running |

## Fonctionnalités avancées

### Config insecure-registries (pour registry local)

Ajouter dans `/etc/docker/daemon.json` :
```json
{
  "insecure-registries": ["192.168.56.101:5000"]
}
```
Puis `sudo systemctl restart docker`.

### Test avec registry (Job 9)

```bash
# Après install, relance Job 9
docker compose up -d  # docker-registry-local/
```

## Gestion des erreurs

| Erreur | Cause | Solution |
|--------|-------|----------|
| `permission denied` | Pas dans groupe docker | `newgrp docker` ou relogin |
| `Cannot connect to the Docker daemon` | Service arrêté | `sudo systemctl start docker` |
| `docker-ce not found` | Mauvaise version Debian | Vérifier `/etc/debian_version` |
| `GPG key failed` | Problème réseau | `apt update` manuel |

## Sécurité & Best Practices

✅ **Clean** : Supprime **tout** sans résidus  
✅ **Install** : Méthode officielle Docker, testée Debian 13  
✅ **Automatisable** : Scripts idempotents  
⚠️ **Backup** : Volumes custom → `docker volume ls` avant clean  

***

**✅ Job 10 terminé** : Scripts clean/install Docker pour Debian 13 !  

 
[Testé sur VM Debian 13 avec Job 9 registry]