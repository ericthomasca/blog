---
title: Installing Jellyfin as a Podman Quadlet
date: 2024-10-01T17:59:03-02:30
tags: [Podman, Jellyfin, Selfhosting]
cover:
  image: cover.png
  relative: true
draft: true
---

Jellyfin is everyone's favourite open-source multimedia player. This guide goes over how to install it as a Podman Quadlet. This assumes you already have Podman setup for Quadlets. Follow [this guide](/posts/setting-up-podman-quadlets-debian) to setup Podman for Quadlets.

## Create the container

First, create and edit `sudo nvim ~/containers/jellyfin.container`, if you don't use Neovim, replace `nvim` with whichever text editor you prefer.

Paste the follwing. Replace `{USER}` with your user, `{PUID}` with the value of `id {USER} -u`, `{PGID}` with the value of `id {USER} -g`, and change your time zone accordingly. Replace the volumes with your prefered config and media folder. Save and exit.

```ini
[Unit]
Description=Podman - Jellyfin
Wants=network-online.target
After=network-online.target

[Container]
Image=lscr.io/linuxserver/jellyfin:latest
AutoUpdate=registry
ContainerName=jellyfin
Environment=PUID=1000
Environment=PGID=100
Environment=TZ=America/St_Johns
Volume=/home/{USER}/services/jellyfin:/config
Volume=/home/{USER}/movies:/movies
Volume=/home/{USER}/tv:/tv
PublishPort=8096:8096
PublishPort=8920:8920
PublishPort=7359:7359/udp
PublishPort=1900:1900/udp

[Service]
Restart=always
TimeoutStartSec=900

[Install]
WantedBy=default.target
# vim: set filetype=systemd :
```

## Themes

If you would like to use themes from https://theme-park.dev/, add the following lines to the `[Container]` section. I prefer their dracula theme. More themes are [here](https://docs.theme-park.dev/themes/jellyfin/). This works with all [Linuxserver images](https://fleet.linuxserver.io/).

```ini
Environment=DOCKER_MODS=ghcr.io/themepark-dev/theme.park:jellyfin
Environment=TP_THEME=dracula
```

If you want to utilize a Nvidia card, you will need to utilize Nvidia Container Toolkit. A guide for this will come later and I will link to it here.

## Load and start the Jellyfin service

```bash
sudo systemctl daemon-reload
sudo systemctl start jellyfin.service
```

You'll be able to access Jellyfin from [http://{SERVER_IP}:8096](http://{SERVER_IP}:8096). Check the [linuxserver jellyfin github](https://hub.docker.com/r/linuxserver/jellyfin) for any extra configuration not covered in this guide.