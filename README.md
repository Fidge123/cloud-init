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

# Run postgres script TODO pgbackrest
$ chmod +x secrets/install_postgres.sh
$ ./secrets/install_postgres.sh

# Configure restic
$ restic s3:https://f18a3fb8cd73bc711ef96ccc7864aa43.r2.cloudflarestorage.com/backup

# Run docker
$ docker compose up -d
```

For debugging these logs are helpful:
- `/var/log/cloud-init.log`: The actual process logs for cloud-init's processing of the configuration files.
- `/var/log/cloud-init-output.log`: Any output produced by the processing of the configuration can be found here.

## Check after initial deployment

- Check NTP status: `sudo ntpq -p`
- Check fail2ban status: `sudo fail2ban-client status`
- Check AIDE: `sudo aide.wrapper --check` (accept changes `sudo aideinit -y -f`)
- Check open ports: `sudo ss -lntup`
- Check msmtp: `echo "hello, world!" | msmtp flori@hey.com`

## Secrets

Create secrets: `tar -cz secrets/ | gpg -c -o secrets.tgz.gpg`

Decrypt: `gpg -d secrets.tgz.gpg | tar xz`

## Services

### Docker
- [Nginx Proxy Manager](https://nginxproxymanager.com): Expose your services easily and securely (Port 81)
  - https://npm.flori-richter.de
  - Default credentials: admin@example.com / changeme
  - TODO: Copy config so it works on first start
- [Stirling PDF](https://github.com/Stirling-Tools/Stirling-PDF): Perform various operations on PDF files (Port 8080)
  - https://pdf.flori-richter.de
  - It is necessary to set `client_max_body_size 100M;` so large PDFs are also handled
  - TODO: Protect with authelia
- [Miniflux](https://miniflux.app): A minimalist and opinionated feed reader (Port 8080)
  - https://rss.flori-richter.de
- [IT-Tools](https://github.com/CorentinTh/it-tools): Collection of handy online tools for developers (Port 80)
  - https://tools.flori-richter.de
  - TODO: Protect with authelia
- [PGAdmin](https://www.pgadmin.org): Administration and development platform for PostgreSQL (Port 80)
  - https://pgadmin.flori-richter.de
  - TODO: How to store the config for servers...
- [Rallly](https://support.rallly.co/self-hosting/introduction): A tool for creating scheduling polls (Port 3000)
  - https://rallly.flori-richter.de
  - TODO: Verify on x86 system...
- [Authelia](https://www.authelia.com/): An authentication and authorization server and portal (Port 9091)
  - https://auth.flori-richter.de
- [Umami](https://umami.is/docs): Simple, fast, privacy-focused analytics solution (Port 3000)
  - https://analytics.flori-richter.de
  - TODO: Check if it works, protect with authelia
- [MeTube](https://github.com/alexta69/metube): Web GUI for youtube-dl with playlist support (Port 8081)
  - https://yt-dlp.flori-richter.de
  - TODO: Check if it works, check how it is cleaned, protect with authelia

<!-- TODO:
- CI runner
- [Invidious](https://invidious.io): Open source alternative front-end to YouTube (Port 3000)
  - https://yt.flori-richter.de
  - TODO: Stop crashes... -->

### Local
- [PostgreSQL](https://www.postgresql.org): A powerful, open source object-relational database system (Port 5432)
  - postgres://pg.flori-richter.de:5432 (only local connections)
  - Tune performance: https://bun.uptrace.dev/postgres/performance-tuning.html
  - TODO: SSL, Backups and restore, tune performance 
- [pgBackRest](https://pgbackrest.org): Reliable PostgreSQL Backup & Restore
  - TODO: Create initial setup and store config
- [restic](https://restic.net): Restic is a modern backup program
  - TODO: Create initial setup + Check resticprofile
- [fail2ban](https://www.fail2ban.org): Daemon to ban hosts that cause multiple authentication errors
  - Protect PSQL: https://serverfault.com/questions/627169/how-to-secure-an-open-postgresql-port
  - TODO: Validate config, setup email notifications
- [msmtp](https://wiki.debian.org/msmtp): An SMTP client that can be used to send mails
  - TODO: Validate config
- [aide](https://aide.github.io): A file and directory integrity checker
  - TODO: Validate config

## My apps

- Resume / CV
  - https://flori-richter.de -> https://cv.flori-richter.de/en
  - TODO: Create small nginx docker container (https://static-web-server.net/features/docker/)
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
