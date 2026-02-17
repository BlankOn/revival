# Installing Docker on BlankOn Linux (Verbeek)

## Add Docker's official GPG key:

```bash
sudo apt update
sudo apt install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/debian/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc
```

## Add the repository to Apt sources:

```bash
sudo tee /etc/apt/sources.list.d/docker.sources <<EOF
Types: deb
URIs: https://download.docker.com/linux/debian
Suites: bookworm
Components: stable
Signed-By: /etc/apt/keyrings/docker.asc
EOF

sudo apt update
```

### Why use `bookworm` instead of `Verbeek`?

The official Docker repository provides packages based on Debian releases (like `bookworm`, `bullseye`, etc.) and does not have a specific distribution for BlankOn (or `Verbeek`). Since BlankOn Verbeek is based on Debian 12 (Bookworm), we must use `Suites: bookworm` to ensure we get the correct and compatible packages. Using `Verbeek` here would cause `apt update` to fail as the Docker repository doesn't recognize it.

## Install Docker Engine

To install the latest version, run:

```bash
sudo apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

### Install Specific Version

To list the available versions:

```bash
apt list -a docker-ce
```

This will output something like:
```text
docker-ce/bookworm 5:27.2.1-1~debian.12~bookworm amd64
docker-ce/bookworm 5:27.2.0-1~debian.12~bookworm amd64
...
```

You can select the specific version you want to install. For example, to install version `5:27.2.1-1~debian.12~bookworm`:

```bash
VERSION_STRING=5:27.2.1-1~debian.12~bookworm
sudo apt install docker-ce=$VERSION_STRING docker-ce-cli=$VERSION_STRING containerd.io docker-buildx-plugin docker-compose-plugin
```

## Verify Docker Engine Installation

Verify that the Docker Engine installation is successful by running the `hello-world` image:

```bash
sudo docker run hello-world
```

This command downloads a test image and runs it in a container. When the container runs, it prints a confirmation message and exits.

## Uninstall Docker Engine

1. Uninstall the Docker Engine, CLI, containerd, and Docker Compose packages:

```bash
sudo apt purge docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin docker-ce-rootless-extras
```

2. Images, containers, volumes, or custom configuration files on your host are not automatically removed. To delete all images, containers, and volumes:

```bash
sudo rm -rf /var/lib/docker
sudo rm -rf /var/lib/containerd
```

## References

For more detailed information, please visit the official Docker documentation:
[https://docs.docker.com/engine/install/debian/](https://docs.docker.com/engine/install/debian/)
