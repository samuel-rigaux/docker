# Job 01 - Installation Docker CLI sur Debian Console (8Go/1Go/1vCPU)

## Configuration VM requise
- OS : Debian (mode console uniquement)  
- Disque : 8 Go
- RAM : 1 Go  
- CPU : 1 vCPU


___

***--> Documentation suivante venant de*** https://docs.docker.com/engine/install/debian/

___ 



# Install Docker Engine on Debian

To get started with Docker Engine on Debian, make sure you
[meet the prerequisites](#prerequisites), and then follow the
[installation steps](#installation-methods).

## Prerequisites

### Firewall limitations

> [!WARNING]
>
> Before you install Docker, make sure you consider the following
> security implications and firewall incompatibilities.

- If you use ufw or firewalld to manage firewall settings, be aware that
  when you expose container ports using Docker, these ports bypass your
  firewall rules. For more information, refer to
  [Docker and ufw](/engine/network/packet-filtering-firewalls/#docker-and-ufw).
- Docker is only compatible with `iptables-nft` and `iptables-legacy`.
  Firewall rules created with `nft` are not supported on a system with Docker installed.
  Make sure that any firewall rulesets you use are created with `iptables` or `ip6tables`,
  and that you add them to the `DOCKER-USER` chain,
  see [Packet filtering and firewalls](/engine/network/packet-filtering-firewalls/).

### OS requirements

To install Docker Engine, you need one of these Debian versions:

- Debian Trixie 13 (stable)
- Debian Bookworm 12 (oldstable)
- Debian Bullseye 11 (oldoldstable)

Docker Engine for Debian is compatible with x86_64 (or amd64), armhf (arm/v7),
arm64, and ppc64le (ppc64el) architectures.

### Uninstall old versions

Before you can install Docker Engine, you need to uninstall any conflicting packages.

Your Linux distribution may provide unofficial Docker packages, which may conflict
with the official packages provided by Docker. You must uninstall these packages
before you install the official version of Docker Engine.

The unofficial packages to uninstall are:

- `docker.io`
- `docker-compose`
- `docker-doc`
- `podman-docker`

Moreover, Docker Engine depends on `containerd` and `runc`. Docker Engine
bundles these dependencies as one bundle: `containerd.io`. If you have
installed the `containerd` or `runc` previously, uninstall them to avoid
conflicts with the versions bundled with Docker Engine.

Run the following command to uninstall all conflicting packages:

```console
$ sudo apt remove $(dpkg --get-selections docker.io docker-compose docker-doc podman-docker containerd runc | cut -f1)
```

`apt` might report that you have none of these packages installed.

Images, containers, volumes, and networks stored in `/var/lib/docker/` aren't
automatically removed when you uninstall Docker. If you want to start with a
clean installation, and prefer to clean up any existing data, read the
[uninstall Docker Engine](#uninstall-docker-engine) section.

## Installation methods

You can install Docker Engine in different ways, depending on your needs:

- Docker Engine comes bundled with
  [Docker Desktop for Linux](/desktop/setup/install/linux/). This is
  the easiest and quickest way to get started.

- Set up and install Docker Engine from
  [Docker's `apt` repository](#install-using-the-repository).

- [Install it manually](#install-from-a-package) and manage upgrades manually.

- Use a [convenience script](#install-using-the-convenience-script). Only
  recommended for testing and development environments.



Apache License, Version 2.0. See [LICENSE](https://github.com/moby/moby/blob/master/LICENSE) for the full license.

### Install using the `apt` repository {#install-using-the-repository}

Before you install Docker Engine for the first time on a new host machine, you
need to set up the Docker `apt` repository. Afterward, you can install and update
Docker from the repository.

1. Set up Docker's `apt` repository.

   ```bash
   # Add Docker's official GPG key:
   sudo apt update
   sudo apt install ca-certificates curl
   sudo install -m 0755 -d /etc/apt/keyrings
   sudo curl -fsSL https://download.docker.com/linux/debian/gpg -o /etc/apt/keyrings/docker.asc
   sudo chmod a+r /etc/apt/keyrings/docker.asc

   # Add the repository to Apt sources:
   sudo tee /etc/apt/sources.list.d/docker.sources <<EOF
   Types: deb
   URIs: https://download.docker.com/linux/debian
   Suites: $(. /etc/os-release && echo "$VERSION_CODENAME")
   Components: stable
   Signed-By: /etc/apt/keyrings/docker.asc
   EOF

   sudo apt update
   ```

   > [!NOTE]
   >
   > If you use a derivative distribution, such as Kali Linux,
   > you may need to substitute the part of this command that's expected to
   > print the version codename:
   >
   > ```console
   > $(. /etc/os-release && echo "$VERSION_CODENAME")
   > ```
   >
   > Replace this part with the codename of the corresponding Debian release,
   > such as `bookworm`.

2. Install the Docker packages.

   **Latest**



   To install the latest version, run:

   ```console
   $ sudo apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
   ```

   **Specific version**



   To install a specific version of Docker Engine, start by listing the
   available versions in the repository:

   ```console
   $ apt list --all-versions docker-ce

   docker-ce/bookworm 5:29.2.1-1~debian.12~bookworm <arch>
   docker-ce/bookworm 5:29.2.0-1~debian.12~bookworm <arch>
   ...
   ```

   Select the desired version and install:

   ```console
   $ VERSION_STRING=5:29.2.1-1~debian.12~bookworm
   $ sudo apt install docker-ce=$VERSION_STRING docker-ce-cli=$VERSION_STRING containerd.io docker-buildx-plugin docker-compose-plugin
   ```

   

    > [!NOTE]
    >
    > The Docker service starts automatically after installation. To verify that
    > Docker is running, use:
    > 
    > ```console
    > $ sudo systemctl status docker
    > ```
    >
    > Some systems may have this behavior disabled and will require a manual start:
    >
    > ```console
    > $ sudo systemctl start docker
    > ```


Résultat attendu :

- ✅ VM Debian console fonctionnelle (8Go/1Go/1vCPU)
- ✅ Docker CLI installé (sans daemon/serveur)
- ✅ Prêt pour connexion à daemon distant

Mission Job 01 accomplie !