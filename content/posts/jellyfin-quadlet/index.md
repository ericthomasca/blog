---
title: Installing Jellyfin as a Podman Quadlet
date: 2024-10-01T17:59:03-02:30
tags: [Podman, Jellyfin, Selfhosting]
cover:
  image: cover.webp
  relative: true
draft: false
---

Jellyfin is everyone's favourite open-source multimedia player. This guide goes over how to install it as a Podman Quadlet. This assumes you already have Podman setup for Quadlets. Follow [this guide](/posts/setting-up-podman-quadlets) to setup Podman for Quadlets.

### Create the container

First, create and edit `/etc/containers/systemd/jellyfin.container` as sudo and paste the following.

```ini
[Unit]
Description=Podman - Jellyfin
Wants=network-online.target
After=network-online.target

[Container]
Image=lscr.io/linuxserver/jellyfin:latest
AutoUpdate=registry
ContainerName=jellyfin
Environment=PUID={PUID} # `id -u` to get value needed
Environment=PGID={PGID} # `id -g` to get value needed
Environment=TZ=America/St_Johns # Change to your time zone
Volume={JELLYFIN_CONFIG}:/config
Volume={MOVIE_DIR}:/movies
Volume={TV_DIR}:/tv
PublishPort=8096:8096
PublishPort=8920:8920
PublishPort=7359:7359/udp
PublishPort=1900:1900/udp

[Service]
Restart=always
TimeoutStartSec=900

[Install]
WantedBy=default.target
```

Change the values to the ones that apply to you. Start and check the container.

```bash
sudo systemctl daemon-reload
sudo systemctl start jellyfin.service
sudo systemctl status jellyfin.service
```

You'll be able to access Jellyfin from [http://{SERVER_IP}:8096](http://{SERVER_IP}:8096). Check the [linuxserver jellyfin github](https://hub.docker.com/r/linuxserver/jellyfin) for any extra configuration not covered in this guide.

### Jellyfin Themes

If you would like to use themes from https://theme-park.dev/, add the following lines to the `[Container]` section. I prefer their dracula theme. More themes are [here](https://docs.theme-park.dev/themes/jellyfin/). This works with all [Linuxserver images](https://fleet.linuxserver.io/).

```ini
Environment=DOCKER_MODS=ghcr.io/themepark-dev/theme.park:jellyfin
Environment=TP_THEME=dracula
```
### Conclusion

You'll be able to access Jellyfin from [http://{SERVER_IP}:8096](http://{SERVER_IP}:8096). Check the [linuxserver jellyfin github](https://hub.docker.com/r/linuxserver/jellyfin) for any extra configuration not covered in this guide.

If you want to utilize a Nvidia card, you will need to utilize Nvidia Container Toolkit. A guide for this will come later and I will link to it here.

If you encounter any issues or have suggestions for improvements, feel free to share! If you have any questions, don’t hesitate to reach out!
 