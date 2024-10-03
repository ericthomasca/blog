---
title: Setting Up Podman and Quadlets
date: 2024-09-30T18:57:10-02:30
tags: [Podman, Quadlets, Selfhosting]
cover:
    image: cover.webp
    relative: true
draft: false
---

In this guide, I’ll walk you through setting up Podman and Quadlets. While this is the method that worked for me, I’m always open to improvements and suggestions. Future posts will cover how to configure specific Quadlets in detail.

### Install Podman

```bash
sudo apt -y install podman
```
For other operation systems, check [here](https://podman.io/docs/installation)

### Create a Quadlet

Create the file `/etc/containers/systemd/postgres.container` and paste in the following data as sudo. 

```ini
[Unit]
Description=Podman - Postgres
Wants=network-online.target
After=network-online.target

[Container]
Image=docker.io/library/postgres:17
AutoUpdate=registry
ContainerName=postgres
# Choose a Postgres username
Environment=POSTGRES_USER={POSTGRES_USER} 
# Choose a Postgres password
Environment=POSTGRES_PASSWORD={POSTGRES_PASSWORD} 
# Change {USER} to your user on the machine
Volume=/home/{USER}/services/postgres:/var/lib/postgresql/data 
PublishPort=5432:5432
HealthCmd="pg_isready -U postgres"
HealthStartPeriod=15s
HealthInterval=30s
HealthTimeout=30s
HealthRetries=3
# USER_ID value is obtained with `id -u` while logged into {USER}
User={USER_ID}
# GROUP_ID value is obtained with `id -g` while logged into {USER}
Group={GROUP_ID}

[Service]
Restart=always
TimeoutStartSec=900

[Install]
WantedBy=default.target
```

If your editor of choice is vim/nvim you can add `# vim: set filetype=systemd :` to the end of the file to enable syntax highlighting.


### Start the service

You may have to create the directories on the host machine mentioned in you `.container` files to prevent errors. If there is a way aroud this, I don't know it. I also recommend a folder like my `services` folder, just to keep things organized

Reload systemd so it sees the service, start, and check the service.

```bash
sudo systemctl daemon-reload
sudo systemctl start postgres.service
sudo systemctl status postgres.service
```

### Conclusion

You’ve successfully set up Podman and created a Quadlet for a Postgres container. This setup provides a robust foundation for self-hosting your Postgres database.

If you encounter any issues or have suggestions for improvements, feel free to share! In future posts, I’ll dive deeper into configuring specific Quadlets and optimizing your Podman setup for different use cases. Happy hosting! If you have any questions, don’t hesitate to reach out!
