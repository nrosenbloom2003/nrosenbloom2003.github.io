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
5. For the PC tunnel, type in `cd ~/wireguard/config` to change into the config directory. Use `ls` to find the file associated with the pc peer (in this case pc_peer1) and then run `cd peer_pc1`. Run `ls` again to find the .conf file, in this case peer_pc1.conf, and run `nano peer_pc1.conf` to open it. Copy down the info in the file and paste in into a blank tunnel in the Wireguard application, then activate it.

Phone Proof (without and with Wireguard Tunnel):
![Phone w/o Tunnel](https://github.com/nrosenbloom2003/nrosenbloom2003.github.io/blob/main/IMG_5203.PNG)
![Phone w/ Tunnel](https://github.com/nrosenbloom2003/nrosenbloom2003.github.io/blob/main/IMG_5204.PNG)

PC Proof (without and with Wireguard Tunnel):
![PC w/o Tunnel](https://github.com/nrosenbloom2003/nrosenbloom2003.github.io/blob/main/Screenshot%202023-12-08%20152518.png)
![PC w/ Tunnel](https://github.com/nrosenbloom2003/nrosenbloom2003.github.io/blob/main/Screenshot%202023-12-08%20153453.png)

