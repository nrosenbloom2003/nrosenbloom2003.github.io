# Wireguard Using Docker Compose
This is a short guide to running Wireguard through a Docker container.
## Step 1: Installing Docker
The first step of this process is installing Docker. DigitalOcean was the platform used to create the VM and Docker container for this lab.
## Step 2: Create the Container
Now we need to create the container for Wireguard
1. To start, run `mkdir -p ~/wireguard/`, then run `mkdir -p ~/wireguard/config`, and then run `nano ~/wireguard/docker-compose.yml` to open the `docker-compose.yml` file.
2. Paste the following code into the file:

```yml
version: '3.8'
services:
  wireguard:
    container_name: wireguard
    image: linuxserver/wireguard
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=America/Chicago
      - SERVERURL=147.182.227.221
      - SERVERPORT=51820
      - PEERS=pc1,phone1
      - PEERDNS=auto
      - INTERNAL_SUBNET=10.0.0.0
    ports:
      - 51820:51820/udp
    volumes:
      - type: bind
        source: ./config/
        target: /config/
      - type: bind
        source: /lib/modules
        target: /lib/modules
    restart: always
    cap_add:
      - NET_ADMIN
      - SYS_MODULE
    sysctls:
      - net.ipv4.conf.all.src_valid_mark=1
```
3. Save the file by pressing CTRL-X, then Y, then ENTER.
4. Next, run `cd ~/wireguard/` to change into the new directory, then run `docker compose up -d`.
   
