---
title: Setting Up Podman and Quadlets on Debian
date: 2024-09-30T18:57:10-02:30
tags: [Podman, Debian, Quadlets, Selfhosting]
cover:
    image: cover.png
    relative: true
draft: false
---

In this guide, I’ll walk you through setting up a Debian home server with Cockpit and Quadlets. While this is the method that worked for me, I’m always open to improvements and suggestions. Future posts will cover how to configure specific Quadlets in detail. This setup assumes you already have a Debian server with SSH access and the backports repository enabled.

Install Podman

```bash
sudo apt -y install podman
```

To make things easier, symlink `/etc/containers/systemd` to `~/containers`

```bash
ln -s /etc/containers/systemd ~/containers
```

Create a folder to holder the data and configs each service

```bash
mkdir ~/services
```

Let's create a quadlet for a Postgres database. Paste the following in a file `~/containers/postgres.container` replace `{USER}` with your server username and `{PASSWORD}` with a secure password, you can also change the Postgres username if you like. Replace `USER=1000` with the value of `id {USER} -u` and `GROUP=100` with the value of `id {USER} -g`. The `# vim: set filetype=systemd :` at the bottom allows the `.container` file to highlight like a `.service` file, which it essentially is.

```ini
[Unit]
Description=Podman - Postgres
Wants=network-online.target
After=network-online.target

[Container]
Image=docker.io/library/postgres:17
AutoUpdate=registry
ContainerName=postgres
Environment=POSTGRES_USER=postgres
Environment=POSTGRES_PASSWORD={PASSWORD}
Volume=/home/{USER}/services/postgres:/var/lib/postgresql/data
PublishPort=5432:5432
HealthCmd="pg_isready -U postgres"
HealthStartPeriod=15s
HealthInterval=30s
HealthTimeout=30s
HealthRetries=3
User=1000
Group=100

[Service]
Restart=always
TimeoutStartSec=900

[Install]
WantedBy=default.target
# vim: set filetype=systemd :
```

Reload systemd to recognize the service

```bash
sudo systemctl daemon-reload
```

Enable the service

```bash
sudo systemctl enable --now postgres.service
```

Check the status of the serice

```bash
sudo systemctl status postgres.service
```

Keep an eye out for more posts similar to this with details on how to set up specific services. 
