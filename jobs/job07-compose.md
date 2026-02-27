# Job 07 - Serveur Web + FTP avec Docker Compose

Ce tutoriel explique comment créer un environnement avec **2 conteneurs Docker** (nginx + FTP) partageant un dossier `web` commun. Le FTP permet d'uploader des fichiers, nginx les sert sur le web. Tout est configuré avec **variables d'environnement** pour la sécurité. [hub.docker](https://hub.docker.com/_/nginx)

## Prérequis
- Système Debian/Ubuntu avec **Docker** et **docker-compose** installés.
- Accès `sudo` (ou user dans groupe `docker`).
- IP VM accessible : `192.168.29.146`.

## 1. Création du docker-compose.yml
Crée le dossier projet et édite le fichier :

```bash
mkdir ~/job7 && cd ~/job7
nano docker-compose.yml
```

**Contenu du `docker-compose.yml`** :
```yaml
services:
  nginx:
    image: nginx
    ports:
      - "80:80"
    volumes:
      - ./web:/usr/share/nginx/html

  ftp:
    image: garethflowers/ftp-server
    container_name: ftp-server
    environment:
      - FTP_USER=${FTP_USERNAME}
      - FTP_PASS=${FTP_PASSWORD}
    restart: always
    ports:
      - "20-21:20-21/tcp"
      - "40000-40000:40000-40000/tcp"
    volumes:
      - ./web:/home/${FTP_USERNAME}/shared
```

**Points clés** :
- **Volume partagé** `./web` → `/usr/share/nginx/html` (nginx lit) et `/home/samuel/shared` (FTP écrit). [stackoverflow](https://stackoverflow.com/questions/65070121/how-to-configure-vsftpd-with-docker)
- Ports : HTTP `80`, FTP `21` + passive `40000`. [github](https://github.com/garethflowers/docker-ftp-server/blob/main/README.md)
- Vars `${FTP_USERNAME}` et `${FTP_PASSWORD}` chargées depuis `.env`. [hub.docker](https://hub.docker.com/_/nginx)

## 2. Configuration des variables
```bash
nano .env
```

**Contenu du `.env`** :
```
FTP_USERNAME=samuel
FTP_PASSWORD=root
```

**⚠️ Sécurité** : Change `root` en prod ; `.env` est ignoré par git par défaut.

## 3. Création du dossier web
```bash
mkdir web
echo '<h1>Ma page web s\'affiche !</h1>' > web/index.html
ls  # → docker-compose.yml  web
ls -a  # → .  ..  docker-compose.yml  .env  web
```

## 4. Lancement
**❌ Erreur courante** (user non-root) :
```bash
docker compose up -d  # → permission denied
```

**✅ Solution** :
```bash
sudo docker compose up -d
```

**Sortie attendue** :
```
[+] up 8/8
 ✔ Image garethflowers/ftp-server Pulled
 ✔ Container ftp-server Created
 ✔ Container job7-nginx-1 Created
```

## 5. Tests
### Accès Web
Ouvre `http://192.168.29.146` → **Ta page `index.html`** s'affiche. [hub.docker](https://hub.docker.com/_/nginx)

### Accès FTP
- Client : FileZilla / WinSCP
- Hôte : `192.168.29.146:21`
- User : `samuel` / Pass : `root`
- Mode : **Passif** (port 40000 ouvert)
- Upload un nouveau `index.html` → rafraîchis le navigateur, c'est mis à jour instantanément !

## 6. Commandes utiles
| Action | Commande |
|--------|----------|
| Arrêt | `sudo docker compose down` |
| Logs nginx | `sudo docker compose logs nginx` |
| Logs FTP | `sudo docker compose logs ftp` |
| Inspect volume | `docker volume ls` |
| Restart | `sudo docker compose restart` |

## Dépannage
- **"Welcome nginx"** : Volume vide → upload via FTP ou recreé `web/index.html`.
- **FTP connexion refusée** : Vérifie firewall VM (`sudo ufw allow 21,40000`), `PUBLIC_IP` si besoin.
- **Permission denied** : Toujours `sudo` ou `sudo usermod -aG docker samuel` + logout/login.

**Statut** : ✅ Fonctionnel sur Debian (testé 26/02/2026). [github](https://github.com/garethflowers/docker-ftp-server/blob/main/README.md)