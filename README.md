# Description

This is the updated docker-compose repo of all the media, home, and web server apps described in the following guides on their respective pages.

- [Ultimate Traefik Docker Compose Guide [2022] with LetsEncrypt](https://www.smarthomebeginner.com/traefik-docker-compose-guide-2022/)

<div style="padding:20px;border: 3px solid red;">
<h3>IMPORTANT</h3>
If you are going to start from scratch using this repo, be prepared to be patient and start slow. There are so many details to pay attention to. First start with the basic Docker Media Server guide linked above (with Nginx Proxy Manager). 

Go step-by-step. If you bite too big of a piece, I guarantee you will choke.

</div>

<strong>Supporting Articles:</strong>

- [How to Install Docker and Docker Compose on Ubuntu 22.04 LTS](https://www.smarthomebeginner.com/install-docker-on-ubuntu-22-04/)
- [How to Install Docker and Docker Compose on Ubuntu 20.04 LTS](https://www.smarthomebeginner.com/install-docker-on-ubuntu-20-04/)
- [Crowdsec Docker Compose Guide Part 1: Powerful IPS with Firewall Bouncer](https://www.smarthomebeginner.com/crowdsec-docker-compose-1-fw-bouncer/)
- [CrowdSec Docker Part 2: Improved IPS with Cloudflare Bouncer](https://www.smarthomebeginner.com/crowdsec-cloudflare-bouncer/)
- [CrowdSec Multiserver Docker (Part 4): For Ultimate Protection](https://www.smarthomebeginner.com/crowdsec-multiserver-docker/)
- 
## Docker, Docker Compose, and Traefik Versions (updated March, 2023)

- Docker: 23.0.1
- Docker Compose: v2.16.0

### Security
For security, I implemented CrowdSec multi-server setup recently. From the stats, it is blocking/mitigating well over 600 intrusion attempts per day on my servers. I will cover this in a separate guide later but you will find the docker-compose CrowdSec, and Cloudflare Bouncer Bouncers in my repo already.

## What apps are included in this stack?

The apps I use are scattered around in several different docker-compose files. Some apps are used in more than one host and some on only one.

### FRONTENDS

- Nginx Proxy Manager - Reverse Proxy (base.yml)
- Portainer - Container Management (background.yml)
- Homepage - Unified Frontend (background.yml)
- Wordpress - Wordpress Website (wordpress.yml)

### PROGRAMS

- Moodle - eLearning environment (moodle.yml)
- Nextcloud - Self Hosted alternative to Google Drive / Onedrive etc (nextcloud.yml)

### UTILITIES

- Glances - System Information (background.yml)
- Dozzle - Docker logs viewer (background.yml)
- Syncthing - Directory syncing / backup utility (background.yml)
- WhoAmI - For Testing and Troubleshooting (utils.yml)

### MAINTENANCE

- Watchtower - Automatic Docker Container Updates (utils.yml)
- Docker-GC - Automatic Docker Garbage Collection (utils.yml)

# Installation and Usage

Follow the guides linked at the beginning of this readme. 

--------- ANYTHING THAT HAS "example" IN THE NAME WILL HAVE TO BE RENAMED APPROPRIATELY ---------

Create a unified network for common communication between NGINX Proxy Manager and all orther docker containers.
`docker network create --subnet 192.168.90.0/24 npm_proxy`

Copy the .env folder to your working docker directory using the following command. Assign User RW permissions and denies all others.
`cp ~/docker/shared/.env.example ~/docker/.env && chmod u=rw,g=,o= ~/docker/.env`

Edit the .env file with the basic values and complete fields as required for each new service.
`nano ~/docker.env`

If you need to generate random values for some .env values you can use the following command for random 22 character hexadecimal passwords.
`for i in {1..5}; do openssl rand -hex 22; done`

## Starting and Stopping

I use bash_aliases to simplify starting and stopping containers/stack. Included in the repo is an example of bash_aliases I use (replace USER with your Linux username). Here are some example alias commands:

- <strong>dup</strong> - Start Docker Services based on NAME.yml in /docker/ymlfiles
- <strong>ddown</strong> - Stop Docker Services based on NAME.yml in /docker/ymlfiles
- <strong>dclogs</strong> - See real-time logs for the corresponding stack or service

## Ports Used

- 80 Nginx Proxy Manager            https://proxy.$DOMAINNAME
- 81 Nginx Proxy Manager Admin Port
- 82 Filebrowser                    https://files.$DOMAINNAME
- 84 Moodle                         https://elearning.$DOMAINNAME
- 85 Nextcloud                      https://cloud.$DOMAINNAME
- 86 Wordpress                      https://$DOMAINNAME
---
- 3000 Homepage                     https://home.$DOMAINNAME
---
- 8080 Reserved
- 8081 Dozzle docker logs           https://dclogs.$DOMAINNAME
- 8082 ReStreamer http              https://stream.$DOMAINNAME
- 8181 ReStreamer https
- 8384 Syncthing                    https://sync.$DOMAINNAME
---
- 9000 Portainer docker manager     https://dock.$DOMAINNAME
---
- 1935 RTMP ReStreamer
- 1936 RTMPS ReStreamer
- 6000/udp ReStreamer
---
- 61208 Glances                     https://system.$DOMAINNAME