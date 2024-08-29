# Cloud Config

This contains the default configuration for my normal server.
Using [cloud-init](https://cloudinit.readthedocs.io/en/latest/reference/modules.html) and the install scripts, it should be fairly fast to spin up a new server.


``` sh-session
Create new server in Hetzner Cloud Console with cloud-init config.
$ TODO: Use hcloud to automate new server creation on hetzner.

# Connect via ssh
$ ssh flori@IP-ADDRESS -p 4444

# Get scripts and data
$ git clone github.com/fidge123/cloud-init
$ cd cloud-init

# Decrypt secrets using key in password manager
$ gpg -d secrets.tgz.gpg | tar xz

# Run postgres script
$ chmod +x secrets/install_postgres.sh
$ ./secrets/install_postgres.sh

# Run docker
$ docker compose up -d
```

For debugging these logs are helpful:
- `/var/log/cloud-init.log`: The actual process logs for cloud-init's processing of the configuration files.
- `/var/log/cloud-init-output.log`: Any output produced by the processing of the configuration can be found here.

## Secrets

Create secrets: `tar -cz secrets/ | gpg -c -o secrets.tgz.gpg`

Decrypt: `gpg -d secrets.tgz.gpg | tar xz`

## Services

- [Nginx Proxy Manager](https://nginxproxymanager.com): Expose your services easily and securely (Port 81)
  - https://npm.flori-richter.de
  - Default credentials: admin@example.com / changeme
  - TODO: Copy config so it works on first start
- [Stirling PDF](https://github.com/Stirling-Tools/Stirling-PDF): Perform various operations on PDF files (Port 8080)
  - https://pdf.flori-richter.de
  - It is necessary to set `client_max_body_size 100M;` so large PDFs are also handled
- [Miniflux](https://miniflux.app): A minimalist and opinionated feed reader (Port 8080)
  - https://rss.flori-richter.de
  - TODO: Backup and restore DB
- [IT-Tools](https://github.com/CorentinTh/it-tools): Collection of handy online tools for developers (Port 80)
  - https://tools.flori-richter.de
- [PGAdmin](https://www.pgadmin.org): Administration and development platform for PostgreSQL (Port 80)
  - https://pgadmin.flori-richter.de
  - TODO: How to store the config for servers...
- [Invidious](https://invidious.io): Open source alternative front-end to YouTube (Port 3000)
  - https://yt.flori-richter.de
  - TODO: Stop crashes...
- [Rallly](https://support.rallly.co/self-hosting/introduction): A tool for creating scheduling polls (Port 3000)
  - https://rallly.flori-richter.de
  - TODO: Verify on x86 system...
- [PostgreSQL](https://www.postgresql.org): A powerful, open source object-relational database system (Port 5432)
  - postgres://pg.flori-richter.de:5432 (only local connections)
  - TODO: Backups and restore, automatic updates

TODO:
- CI runner

## My apps

- Resume / CV
  - https://flori-richter.de -> https://cv.flori-richter.de/en
  - TODO: Create small nginx docker container
- Tippspiel App
  - https://nfl-tippspiel.de
  - TODO: Dockerize, migrate email service?
- Tippspiel Logos
  - https://nfl-tippspiel.de/logos
  - TODO: Dockerize
- Tippspiel Updater
  - TODO: Dockerize
- Discord Bot
  - https://vtt.flori-richter.de
  - TODO: Dockerize
