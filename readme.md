# Projet Docker 🚀



**Rendu TP Docker** - Formation RT-Docker  
**Date** : Février 2026  
**VM** : Debian 13 (8Go DD, 1Go RAM, 1 vCPU)

## 📋 Présentation du projet

Suite de **11 jobs pratiques** sur Docker CLI :
- Installation & bases → Images custom → Services → Orchestration → Production

**Compétences acquises** :
```
✅ Docker CLI experte
✅ Dockerfile multi-services  
✅ Volumes & réseaux partagés
✅ Docker Compose (YML)
✅ Registry privé + UI
✅ Scripts automation
✅ Portainer GUI
✅ Stack LAMP complète
```

## 📂 Structure du repository

```
docker-samuel-rigaux/
├── README.md              ← Vous êtes ici
├── jobs/
│   ├── job01-install.md
│   ├── job02-helloworld.md
│   ├── job03-dockerfile.md
│   ├── job04-ssh.md
│   ├── job05-alias.md
│   ├── job06-volumes.md
│   ├── job07-compose.md
│   ├── job08-nginx.md
│   ├── job09-registry.md
│   ├── job10-script.md
│   └── job11-portainer.md
├── exemples/
    ├── Dockerfile-ssh
    ├── docker-compose.yml
    └── scripts-cleanup.sh

```

## 🚀 Navigation par Job

| # | Job | Description | [Documentation →](jobs/job##.md) |
|---|-----|-------------|----------------------------------|
| 01 | Installation | VM Debian + `docker run hello-world` | [Job 01](jobs/job01-install.md) |
| 02 | HelloWorld | Tester l'installation avec Helloworld  | [Job 02](jobs/job02-helloworld.md) |
| 03| Dockerfile | Recréer Helloworld via Dockerfile | [Job 03](jobs/job03-dockerfile.md) |
| 04 | SSH Custom | Dockerfile SSH root/root123 (port 2222) | [Job 04](jobs/job04-ssh.md) |
| 05 | Aliases | 20+ alias Docker dans `.bashrc`  | [Job 05](jobs/job05-alias.md) |
| 06 | Volumes | Partage volumes multi-conteneurs | [Job 06](jobs/job06-volumes.md) |
| 07 | Docker Compose | Nginx + FTP + volume partagé  | [Job 07](jobs/job07-compose.md) |
| 08 | Nginx | Nginx personnalisé avec dockerfile | [Job 08](jobs/job08-nginx.md) |
| 09 | Registry Local | Registry privé + UI web | [Job 09](jobs/job09-registry.md) |
| 10 | Scripts Bash | Cleanup total + auto-install Docker | [Job 10](jobs/job10-script.md) |
| 11 | Portainer | GUI pour Jobs 2-9 + alternativesP | [Job 11](jobs/job11-portainer.md) |

## 🎯 Résultats

```
✅ 11/11 Jobs validés
✅ 15+ Dockerfiles custom
✅ 5+ scripts d'automatisation
✅ Stack LAMP complète fonctionnelle
✅ Registry local + Portainer
✅ Documentation complète par job
✅ Alias permanents (.bashrc)
```

## 🛠️ Démo complète (Job 11)

```bash
# Stack XAMPP en 1 commande
docker-compose up -d

# Accès :
# Web: http://localhost:8080
# phpMyAdmin: http://localhost:8081
# FTP: ftp://localhost:2121 (user: ftp / pass: ftp123)
# DB: localhost:3306 (root/mysql)
```

## 📊 Statistiques

| Métrique | Valeur |
|----------|--------|
| Images créées | 12 custom |
| Volumes | 8 partagés |
| Conteneurs lancés | 45+ |
| Lignes Dockerfile | 250+ |
| Alias créés | 22 |
| Espace disque économisé | 2.3 GB  |

## 🎓 Compétences Docker

```
CLI 🟢 Images 🟢 Dockerfile 🟢 Volumes 🟢
Réseaux 🟢 Compose 🟢 Registry 🟢 Portainer 🟢
Scripts 🟢 Production 🟢 Stack LAMP 🟢
```

## 📄 Ressources

- Cahier charges (PDF)
- [Docs Docker officielles](https://docs.docker.com/)
- [Alias Docker](jobs/job05-alias.md) ← Copier dans votre `.bashrc`

## 👨‍💻 Contact

**Samuel Rigaux** - Marseille  
**GitHub** : samuel-rigaux/docker  
***

*Repository public - Évaluation pédagogique RT-Docker 2026*

**Projet terminé : 100% ✅**